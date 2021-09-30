# Capitol Trades data source
> This dataset collects financial disclosure reports for Senators, former Senators and Senate candidates filed from 
> 2012 to present.

The Ethics in Government Act (EIGA, specifically 5 U.S.C. app. §§ 101-112), Senate Rule 34, and Senate Rule 41, 
require Members, officers, certain employees, and candidates to file public Financial Disclosure Reports with the 
Secretary of the Senate, Office of Public Records.    
The Committee administers the financial disclosure reporting program for the U.S. Senate.    

In addition, the Stop Trading on Congressional Knowledge Act (STOCK Act) requires the Senate to establish and 
maintain an electronic filing system for Financial Disclosure Reports.
 
Visit [senate.gov/financialdisclosure][4] for authoritative references

## Data stuarts   
- Data structure and field definitions: Huzaifa Waseem [<huzaifa.waseem@2iqresearch.com>][1]    
- Technical: Muaz ul Haq [<muaz.ul-haq@2iqresearch.com>][2]


## Data delivery methods       
#### 1. Postgres database     
    hostname: some-server.some-domain.pk   
    database: data-collection   
    read-only credentials upon request
  
  
#### 2. API endpoint    
  There is a public API endpoint that can be queried using standard HTTP tools ([where-is-the-swagger-spec][3])    
  Please note that bot protection is in effect - the requests will be rate limited    
  

#### 3. S3 bucket    
  A set of files is available for delivery into an S3 bucket upon request    
  There are two main sets of files: a snapshot file with the current view of the data, and, an incremental file with    
  changes as they happen over time.   

  Replaying the snapshot files will rebuild the snapshot.

  The standard bucket's structure:

    capitol_trades
      │
      ├─── datasource-info.yaml
      ├─── datasource-info.md
      ├─── trade
      │       ├─── incremental       
      │       │       ├─── 20210811_170903-trade.csv               <- a file with trade changes every 3 min
      │       │       └─── 20210811_170906-trade.csv   
      │       └─── snapshot
      │               ├─── 20210731_235959_trade_snapshot.csv      <- a monthly snapshot
      │               └─── 20210831_235959_trade_snapshot.csv   
      ├─── person
      │       ├─── incremental       
      │       │       ├─── 20210811_170903-person.csv              <- a file with person changes every 3 min
      │       │       └─── 20210811_170906-person.csv   
      │       └─── snapshot
      │               ├─── 20210731_235959_person_snapshot.csv     <- a monthly snapshot
      │               └─── 20210831_235959_person_snapshot.csv   
      └─── readme.md

[or... grouped by entity type]

    capitol_trades
      │
      ├─── datasource-info.yaml
      ├─── datasource-info.md
      ├─── incremental       
      │       ├─── 20210811_170903-trade.csv                      <- a file with trade changes every 3 min
      │       ├─── 20210811_170906-trade.csv                      
      │       ├─── 20210811_170903-person.csv                     <- a file with person changes every 3 min
      │       └─── 20210811_170906-person.csv   
      ├─── snapshot
      │        ├─── 20210731_235959_trade_snapshot.csv            <- a monthly snapshot
      │        ├─── 20210831_235959_trade_snapshot.csv   
      │        ├─── 20210731_235959_person_snapshot.csv           <- a monthly snapshot
      │        └─── 20210831_235959_person_snapshot.csv   
      └─── readme.md


## Data delivery frequency
New data is available **at least** [/**at most**] every 3 minutes after `[being scraped from source / being approved for distribution / ...some other quality gate criteria, ...]`

## Data coverage
Coverage for Senator Transactions starts in `yyyy-mm-dd`  
Coverage for Senators, former Senators and Candidates (see FLD0004, PoliticianID) starts in `yyyy-mm-dd` 

## Point-In-Time approach
There is no point-in-time as of now - each snapshot file contains the entire "view of reality" up until the timestamp of the export.    
If you are looking to reproduce an individual's holdings over time, you need to iterate over all historical snapshots and rebuild it from there.

## Field specification
Catalog of all fields made available by the data source per type of entity type

### Trade
| Field ID 	| Field Name 	| Field Description 	|
|----------	|------------	|-------------------	|
|FLD001   	| TradeID      	| Unique identifier for each transaction |
|FLD002   	| TradeDate    	| Date the transaction settled |
|FLD003   	| PublicationDate | Date the regulator made the information public |
|FLD004   	| ReportingIndividualID | Identifier for the individual that reported the trade |
|FLD005   	| Owner	        | Person who actually traded (see description) |
|FLD006   	| AssetID	    | Identifier of the asset that was traded |
|FLD007   	| TransactionType | The transaction type |
|FLD008   	| SizeRange     | The size range, in units, of the trade (i.e.: number of shares traded) |
|FLD009   	| SizeAverage   | The average size, in units, of the trade (i.e.: number of shares traded) |
|FLD010   	| Price         | The price, in USD, of the shares traded |
|FLD011   	| CapitalGains  | The amount of capital gains, in USD, generated by the transaction |
|FLD012   	| ValueRangeMin  | The min value of the range |
|FLD013   	| ValueRangeMax | The max range of the traded value, in USD |
|FLD014   	| ValueAverage | The average value of the trade |
|FLD015   	| CustodianName | The custodian that is holding the traded assets |
|FLD016   	| FilingURL | The original filing URL |
|FLD017   	| FilingID | The opaque filingID from the original filing |


### Person
| Field ID 	| Field Name 	| Field Description 	|
|----------	|------------	|-------------------	|
|FLD018   	| ReportingIndividualID | |
|FLD019   	| PoliticalParty | The individual's political party |
|FLD020   	| RepresentedStateOrDistrict | The district of the individual |
|FLD021   	| EthnicityId | The ethnicity of the reporting individual |
|FLD022   	| PictureURL | URL of the individual's picture |
|FLD023   	| TwitterProfile | The Twitter handle of the reporting individual |
|FLD024   	| LinkedInProfile | The LinkedIn profile of the reporting individual |


### Assets
| Field ID 	| Field Name 	| Field Description 	|
|----------	|------------	|-------------------	|
|FLD025   	| AssetID	    | Unique identifier of the asset that was traded |
|FLD026   	| AssetType	    | The asset type of the asset |
|FLD027   	| AssetName	    | A descriptive name of the asset |
|FLD028   	| Ticker	    | The Bloomberg Ticker of the asset |
|FLD029   	| PermID	    | The PermID of the asset |
|FLD030   	| CompustatGVKey | Compustat's GVKey identifier for the traded asset |
|FLD031   	| CompustatIID | Compustat's IID identifier for the traded asset |



### Field specification
#### FLD001, TradeID
> `Type:` number    
> `Unique:` yes    
> `Format:`     
> `Description` A unique number that identifies each trade

#### FLD002, TradeDate
> `Type:` date    
> `Unique:` no    
> `Format:` ISO8601 YYYY-MM-DD    
> `Description` An ISO8601 date of when the transaction settled

#### FLD003, PublicationDate
> `Type:` timestamp    
> `Unique:` no   
> `Format:` ISO8601 YYYY-MM-DDThh:mm:ssZ000    
> `Description` An ISO8601 date of when the regulator publicly disclosed the transaction          
> 
>  **Note:** This is the date that should be used for analytics.     
>  It represents the date when "the market" was made aware of the transaction in UTC timezone
>  TODO: from slack: "Huzaifa: There is usually a T+2 gap in reported filing date and file actually going live"

#### FLD004, ReportingIndividualID
> `Type:` number    
> `Unique:` no     
> `Format:`  `Spouse`, `Child`, `--`     
> `Description` A numeric identifier linking a FLD018, PersonID to a trade (see Person lookup table) 

#### FLD0005, Owner
> `Type:` text    
> `Unique:` no    
> `Description`   
>    A text field that contains the relationship type between the reporting member and the ultimate beneficiary of the trade.     
>    In practice this can be someone (spouse, child, ...) that by House of Representatives' regulation `xxxxx` is required to report a transaction    
>    Do not confuse this field with FLD004, ReportingIndividualID which represents the reporting individual

#### FLD006, AssetID
> `Type:` text    
> `Unique:` no    
> `Description`   
>  A text field identifying the traded asset ID

#### FLD007, TransactionType
> `Type:` text    
> `Unique:` no    
> `Format:` BUY | SELL PARTIAL | SELL FULL | EXCHANGED | RECEIVED    
> `Description`  
> A text field identifying the type of transaction    
> 
>     BUY - Increase the holdings of the asset      
>     SELL PARTIAL - Sell part of the position on the asset          
>     SELL FULL - Sell the entire position of the asset    
>     EXCHANGED - TODO: ????  
>     RECEIVED - Received counter trades of Exchange in case of merger trades  

#### FLD008, SizeRange
> `Type:` text    
> `Unique:` no    
> `Format:` nn,nnn,nnn | nn,nnn,nnn - nn,nnn,nnn   ("," is the thousands' separator with no decimal separator)  
> `Description`  
> A text representation of the number of shares traded.    
> This field can either be a single number, or a textual representation of a range             
> Examples: `15 - 221`, `40,000`, `--`     

#### FLD009, SizeAverage
> `Type:` text    
> `Unique:` no    
> `Format:` nn,nnn,nnn   ("," is the thousands' separator with no decimal separator)  
> `Description`  
> A text representation of the average number of shares traded.    
> This field is either be a single number    
> Examples: `1,000`, `--`      

#### FLD010, Price
> `Type:` text    
> `Unique:` no    
> `Format:` "," is the thousands' separator, "." is the decimal separator    
> `Undefined value:` --       
> `Description`  
> The price that the trade was executed quoted in USD
> TODO: how is the price computed?
 
#### FLD011, CapitalGains
> `Type:` money    
> `Unique:` no    
> `Undefined value:` --       
> `Description` Amount of capital gains, quoted in USD

#### FLD012, ValueRangeMin
> `Type:` money    
> `Unique:` no    
> `Undefined value:` --       
> `Description` Range of the total traded amount, quoted in USD    
> TODO: How is the price computed FLD011?    
> TODO: How is the size range computed FLD010?

#### FLD013, ValueRangeMax
> `Type:` money    
> `Unique:` no    
> `Undefined value:` --       
> `Description` Range of the total traded amount, quoted in USD    
> TODO: How is the price computed FLD011?    
> TODO: How is the size range computed FLD010?

#### FLD014, ValueAverage
> `Type:` money    
> `Unique:` no    
> `Undefined value:` --       
> `Description` Average traded amount, quoted in USD    
> TODO: How is the price computed FLD011?    
> TODO: How is the size range computed FLD010?

#### FLD015, CustodianName
> `Type:` text    
> `Unique:` no    
> `Undefined value:` --       
> `Description` The custodian where the assets are being held.    
> TODO: Does not apply to the "Crypto" asset type???

#### FLD016, FilingURL
> `Type:` url    
> `Unique:` no    
> `Format:` RFC 8820       
> `Description` e-Financial Disclosure's filling URL

#### FLD017, FilingID
> `Type:` text    
> `Unique:` no    
> `Description` e-Financial Disclosure's filling ID



### Person
The lookup table for individuals that are legally required to report (Senators, Representatives, ...)

#### FLD018, ReportingIndividualID
> `Type:` text    
> `Unique:` yes    
> `Description` The unique identifier of a reporting individual

#### FLD019, PoliticalParty
> `Type:` text    
> `Unique:` no    
> `Format:`  R | D | O    
> `Description` The political affiliation of the reporting person.
> 
>      R - republican party     
>      D - democrat party 
>      O - ??other undefined?? 
>      TODO: double check all the unique values in the DB

#### FLD020, RepresentedStateOrDistrict
> `Type:` text    
> `Unique:` no    
> `Description` The state represented by the individual
 
#### FLD021, EthnicityId
> `Type:` number    
> `Unique:` no    
> `Description` The ethnicity of the individual     
>  Language Assistance Data Base (LADB), based on CDC Race/Ethnicity Codes and ISO Language Codes.     
>  Agency for Healthcare Research and Quality, Rockville, MD.    
>  https://www.ahrq.gov/research/findings/final-reports/iomracereport/reldataaph1.html      

#### FLD022, PictureURL
> `Type:` url    
> `Unique:` no    
> `Format:` RFC 8820        
> `Description` A url where the official photograph of the individual can be retrieved   

#### FLD023, TwitterProfile
> `Type:` text    
> `Unique:` true    
> `Description` The twitter handle of the individual 

#### FLD024, LinkedInProfile
> `Type:` text    
> `Unique:` true    
> `Description` The LinkedIn name of the individual 



### Asset
The lookup table for assets

#### FLD025, AssetID
> `Type:` text    
> `Unique:` yes     
> `Description`   
> The unique identifier of the traded asset

#### FLD026, AssetType
> `Type:` text    
> `Unique:` no    
> `Format:` STOCKS | STOCK OPTIONS | BONDS | NON-PUBLIC STOCK | OTHER SECURITIES | MUNICIPAL BONDS | ...  
> `Description`   
> The text field with the name of the traded asset.
> TODO: enumerate all existing asset types and expand how they are used

#### FLD027, AssetName
> `Type:` text    
> `Unique:` no    
> `Format:`  
> `Description`   
> The text field with the name of the traded asset

#### FLD028, BBGTicker
> `Type:` text    
> `Unique:` no    
> `Format:`   
> `Description`   
> A text field identifying the ticker of the traded asset

#### FLD029, PermID
> `Type:` text    
> `Unique:` no    
> `Format:`   
> `Description`   
> A text field identifying the PermID code of the traded asset

#### FLD030, CompustatGVKey
> `Type:` text    
> `Unique:` no    
> `Format:`   
> `Description`   
> A text field identifying the Compustat's GVKey code of the traded asset

#### FLD031, CompustatIID
> `Type:` text    
> `Unique:` no    
> `Format:`   
> `Description`   
> A text field identifying Compustat's IID code of the traded asset

[1]: mailto:huzaifa.waseem@2iqresearch.com
[2]: mailto:muaz.ul-haq@2iqresearch.com
[3]: https://api.capitoltrades.com
[4]: https://www.ethics.senate.gov/public/index.cfm/financialdisclosure






