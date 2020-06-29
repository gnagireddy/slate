---
title: MFIT API

language_tabs: # must be one of https://git.io/vQNgJ
  - java
  - python
  - javascript
  - php

toc_footers:
  - <a>Sign Up for a Developer Key</a>
  - <a href='#'>Rahul Sharma [rahul@mfit.co.in]</a>

includes:
  - errors

search: true
---

# Introduction

Welcome to MFIT API documentation. Here, You'll find comprehensive information for integrating with our endpoints. 

MFIT API's are typically used to process PDF,XLS,HTML files. MFIT internally processes (parses, enriches and analyzes) the input files and the output is relayed back in API call result. Output is typically either a JSON or zip file containing xls or both.

### Version ChangeLog

Version | Description
--------- | -----------
V0 |  Can handle one file at a time.<br>Two endpoints for giving Json/zip output.
V1 |  Can handle multiple files at a time.<br>Single endpoint for giving both Json/zip output(controlled by a query parameter).

<br><br>MFIT uses API keys to allow access to the API. API key shall always be included in all API requests to the server.

<br>We have language bindings in Java, Python, JavaScript and PHP! You can view code examples in the dark area to the right, and you can switch the programming language of the examples with the tabs in the top right.

<aside class="success">
Remember — API Key is a must to use MFIT's API. Get your key by emailing Rahul Sharma [rahul@mfit.co.in]
</aside>

# API Overview

MFIT uses a single API endpoint, which gives out all the tagged data. 

1.	MFIT API works real time and instantaneously gives a response in the output. The flow would be such that output data can be directly pushed onto the client systems.

2.	MFIT’s API is able to categorise the document format and yield one of the two kinds of outputs given above either investment or banking. This avoids documentFormat being passed as an input parameter in the API initiation call. One of the api output tags is “Documentformat”.

3.	MFIT API provides three kinds of services which is embedded in the same output 
  
&emsp;3.1.	Parsing : This is just the plain extraction of data from the pdf as is 
  
&emsp;3.2.	Enrichment: This is additional data which is provided along with the parsed to make the data useful for the client. eg, RTA Code to ISIN lookup, SecurityDescription to ISIN lookup, ISIN to MutualFund type etc as per the input document type.  
    
4.	Analytics: We also perform various analytics such as IRR calculations, Ageing for units, Tax calculations, exit loads. These tags have been coloured green


Language specific API code examples can be seen in the right tab. 


# How to Read Output

## Broad Sections

> "/scrape" returns JSON structure like this:
> -	Wherever there is a [n] this denotes an array with n items

```json
[
  "startDate": "01-Apr-2000",
  
  "endDate" : "15-Jun-2020",
  
  "fileName": "Name of the file",
  
  "documentFormat": "DocumentFormat",
  
  "personalInfo"{
      "name": "Example Bob",
      "email": "example@yahoo.com",
      "address": "#1, HS Colony, Sector 32YX, Mars",
      "pan": "Govt Unique Identifier",
  },
  
  "groups[n]":{
      
     "Bob the husband": {

        "Holdings[n]":{
          "Transactions[n]"  

          "Holding specific Analytics"
        }       
        "Group Bob level Analytics"
     }

     "Jackie the wife": {

       "Holdings[n]":{    
          "Transactions[n]"  

          "Holding specific Analytics"
       }       
       "Group Jackie Level Analytics"
    }
    
    "All groups combined Level Analytics"
  },

  "errors"{
      "Errors if Any, blank otherwise"
  }
]
```

<aside class="notice">
HighLevel Json Structure example given on right tab. Document Type examples are given in the <a href="#examples">Examples</a> section below 
</aside>


API output has been broadly broken down into 5 segments as mentioned below:

a.	Document Type 

b.	Personal Information 

c.	Start Date – End date for the documents 

d.	Error responses (in case there are errors in reading the document)

e.	Investment Accounts 

&emsp;1.	Account details

&emsp;2.	Holdings

&emsp;&emsp;.	Transactions

&emsp;3.	Enrichment

&emsp;4.	Analytics

&emsp;&emsp;.	Holding Level Analytics

&emsp;&emsp;.	Group Level Analytics

&emsp;&emsp;.	Document Level Analytics


<aside class="success">
Don't be surprised to see few tags in JSON which are not present in document. MFIT API automatically enriches commonly useful information. 
</aside>

# API Specifications

## /scrape

> To call MFIT API, use this code:

```java
OkHttpClient client = new OkHttpClient().newBuilder()
  .build();
MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = new MultipartBody.Builder().setType(MultipartBody.FORM)
  .addFormDataPart("file","file",
    RequestBody.create(MediaType.parse("application/octet-stream"),
    new File("/path/to/file")))
  .addFormDataPart("password", "")
  .build();
Request request = new Request.Builder()
  .url("http://portal.mfit.co.in/rest/file/scrape?apiKey=API_KEY")
  .method("POST", body)
  .build();
Response response = client.newCall(request).execute();
```

```python
import requests

url = "http://portal.mfit.co.in/rest/file/scrape?apiKey=API_KEY"

payload = {'password': ''}
files = [
  ('file', open('/path/to/file','rb'))
]
headers= {}

response = requests.request("POST", url, headers=headers, data = payload, files = files)

print(response.text.encode('utf8'))
```

```javascript
var form = new FormData();
form.append("file", fileInput.files[0], "file");
form.append("password", "");

var settings = {
  "url": "http://portal.mfit.co.in/rest/file/scrape?apiKey=API_KEY",
  "method": "POST",
  "timeout": 0,
  "processData": false,
  "mimeType": "multipart/form-data",
  "contentType": false,
  "data": form
};

$.ajax(settings).done(function (response) {
  console.log(response);
});
```

```php
require_once 'HTTP/Request2.php';
$request = new HTTP_Request2();
$request->setUrl('http://portal.mfit.co.in/rest/file/scrape?apiKey=API_KEY');
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->addPostParameter(array(
  'password' => ''
));
$request->addUpload('file', '/path/to/file', 'file', '<Content-Type Header>');
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}
```

> Make sure to replace `API_KEY` with your API key.

This endpoint retrieves JSON of the processed file

### HTTP Request

`POST http://portal.mfit.co.in/rest/file/scrape`

### Query Parameters

Parameter | Description
--------- | -----------
apiKey |  Your API_KEY


### Body  formdata

Parameter | Description
--------- | -----------
file |  File to be processed
password |  password of the above file

## /scrapeToFile

> To call MFIT API, use this code:

```java
OkHttpClient client = new OkHttpClient().newBuilder()
  .build();
MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = new MultipartBody.Builder().setType(MultipartBody.FORM)
  .addFormDataPart("file","file",
    RequestBody.create(MediaType.parse("application/octet-stream"),
    new File("/path/to/file")))
  .addFormDataPart("password", "")
  .build();
Request request = new Request.Builder()
  .url("http://portal.mfit.co.in/rest/file/scrapeToFile?apiKey=API_KEY")
  .method("POST", body)
  .build();
Response response = client.newCall(request).execute();
```

```python
import requests

url = "http://portal.mfit.co.in/rest/file/scrapeToFile?apiKey=API_KEY"

payload = {'password': ''}
files = [
  ('file', open('/path/to/file','rb'))
]
headers= {}

response = requests.request("POST", url, headers=headers, data = payload, files = files)

print(response.text.encode('utf8'))
```

```javascript
var form = new FormData();
form.append("file", fileInput.files[0], "file");
form.append("password", "");

var settings = {
  "url": "http://portal.mfit.co.in/rest/file/scrapeToFile?apiKey=API_KEY",
  "method": "POST",
  "timeout": 0,
  "processData": false,
  "mimeType": "multipart/form-data",
  "contentType": false,
  "data": form
};

$.ajax(settings).done(function (response) {
  console.log(response);
});
```

```php
require_once 'HTTP/Request2.php';
$request = new HTTP_Request2();
$request->setUrl('http://portal.mfit.co.in/rest/file/scrapeToFile?apiKey=API_KEY');
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->addPostParameter(array(
  'password' => ''
));
$request->addUpload('file', '/path/to/file', 'file', '<Content-Type Header>');
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}
```

> Make sure to replace `API_KEY` with your API key.

This endpoint retrieves zip file containing XLS of the processed file. 

<aside class="notice">
Outupt is a zip file containing xls file(s). This accomodates multiple xls output formats generated from a single input file. 
</aside>

### HTTP Request
`POST http://portal.mfit.co.in/rest/file/scrape`

### Query Parameters

Parameter | Description
--------- | -----------
apiKey |  Your API_KEY


### Body  formdata

Parameter | Description
--------- | -----------
file |  File to be processed
password |  password of the above file

## /v1/scrape

> To call MFIT API, use this code:

```java
OkHttpClient client = new OkHttpClient().newBuilder()
  .build();
MediaType mediaType = MediaType.parse("text/plain");
RequestBody body = new MultipartBody.Builder().setType(MultipartBody.FORM)
  .addFormDataPart("file","file",
    RequestBody.create(MediaType.parse("application/octet-stream"),
    new File("/path/to/file")))
  .addFormDataPart("password", "")
  .build();
Request request = new Request.Builder()
  .url("http://portal.mfit.co.in/rest/file/v1/scrape?apiKey=API_KEY&fmt=zip+json")
  .method("POST", body)
  .build();
Response response = client.newCall(request).execute();
```

```python
import requests

url = "http://portal.mfit.co.in/rest/file/v1/scrape?apiKey=API_KEY&fmt=zip+json"

payload = {'password': ''}
files = [
  ('file', open('/path/to/file','rb'))
]
headers= {}

response = requests.request("POST", url, headers=headers, data = payload, files = files)

print(response.text.encode('utf8'))
```

```javascript
var form = new FormData();
form.append("file", fileInput.files[0], "file");
form.append("password", "");

var settings = {
  "url": "http://portal.mfit.co.in/rest/file/v1/scrape?apiKey=API_KEY&fmt=zip+json",
  "method": "POST",
  "timeout": 0,
  "processData": false,
  "mimeType": "multipart/form-data",
  "contentType": false,
  "data": form
};

$.ajax(settings).done(function (response) {
  console.log(response);
});
```

```php
require_once 'HTTP/Request2.php';
$request = new HTTP_Request2();
$request->setUrl('http://portal.mfit.co.in/rest/file/v1/scrape?apiKey=API_KEY&fmt=zip+json');
$request->setMethod(HTTP_Request2::METHOD_POST);
$request->setConfig(array(
  'follow_redirects' => TRUE
));
$request->addPostParameter(array(
  'password' => ''
));
$request->addUpload('file', '/path/to/file', 'file', '<Content-Type Header>');
try {
  $response = $request->send();
  if ($response->getStatus() == 200) {
    echo $response->getBody();
  }
  else {
    echo 'Unexpected HTTP status: ' . $response->getStatus() . ' ' .
    $response->getReasonPhrase();
  }
}
catch(HTTP_Request2_Exception $e) {
  echo 'Error: ' . $e->getMessage();
}
```

> Make sure to replace `API_KEY` with your API key.
This endpoint retrieves zip file containing (json or xls or both) of the processed file(s). 

<aside class="notice">
If more than one file is being processed, output contains information of all individual docs in seperate zip files along with an additional file containing consolidated information. If n > 1, output has n + 1 zip files. 
</aside>

### HTTP Request
`POST http://portal.mfit.co.in/rest/file/v1/scrape`

### Query Parameters

Parameter | Description
--------- | -----------
apiKey |  Your API_KEY
fmt | Format of output (default is json)

### Body  formdata

Parameter | Description
--------- | -----------
file |  File(s) to be processed
password |  password(s) of the above file

### Various combinations possible with the "fmt" parameter are mentioned below:

fmt | Description
--------- | -----------
json | Output is json (Default option if "fmt" parameter doesn't exist in the API call)
zip | Output is xls files
json+zip | Output is json and xls files

# Examples

## Cams/Karvy eCAS statement

> Sample CAMS/KARVY JSON is structured like this:

```json
[
  {
    "fileName":"FileName.pdf",
    "startDate":"01-Jan-1980",
    "endDate":"21-Aug-2017",
    "documentFormat":"CAMS",
    "errors":[],

    "personalInfo":
    {
      "address":"F156, BHARTEEYA KALA CHS, MUMBAI - 400099, Maharashtra, Phone Off: xxxxxxxxxx",
      "name":"Jigar xxxxxx",
      "mobile":"+91xxxxxxxx",
      "email":"xxxxxxx@gmail.com"
    },

    "groups":{
      "Jigar xxxxx":{
        
        "mutualFunds":
        [
          {
            "groupName": "Jigar xxxxx",
            "pan": "xxxxxxxxx",
            "kycStatus": "OK",
            "registrar": "CAMS",
            "advisor": "DIRECT",
            "advisorName": "DIRECT",
            "accountId": "10184833790 / 0",
          
            "serviceProviderName": "Aditya Birla Sun Life Mutual Fund",
            "securityDescription": "Aditya Birla Sun Life MIP II - Wealth 25 Plan - Growth-Direct Plan",
            "schemeId": "B313GZ",
            "isin": "INF209K01XH4",
            "amfiCode": "120705",
            "amcCode": "BirlaSunLifeMutualFund_MF",          
            "bseSchemeCode": "BS313GZ-GR",
            "schemeType": "DEBT",
            "schemeSubcategory": " Conservative Hybrid Fund ",
            "schemeBucket": "Open Ended Schemes",
            "rtaPrefix": "B",
            "dividendReinvestmentFlag": "Z",
            "navason31jan2018": "40.415",

            "openingBalance": "137.749",
            "nav": "39.9579",
            "valueDate": "18-Aug-2017",
            "closingBalance": "271.178",
            "closingNetAmount": "10,835.70",

            "loadStructure": "\"WEF 10-Oct-2016 In respect of each purchase / switch-in of Units, upto 15% of the units may be redeemed / switched-out without any exit load from the date of allotment. Any redemption in excess of the above limit shall be subject to the following exit load:Wef 01-Jun-2016 - For redemption/switch out of units within 365 days from the date of allotment 1.00% of applicable NAV For redemption/switch out of units after 365 days from the date of allotment: NilNote: The exit load rate levied at the time of redemption/switch-out of units will be the rate prevailing at the time of allotment of the corresponding units. Customers may request for a separate Exit Load Applicability Report by calling our toll free numbers 1800-270-7000 / 1800-22-7000 or from any of our Investor Service Centers.\"",

            "mftransactions": [
              {
                "date": "05-Apr-2017",
                "transactionDetails": "Purchase - via Internet",
                "amount": "5,000.00",
                "price": "37.4731",
                "pan": "xxxxxxxx",
                "openingBalance": "137.749",
                "units": "133.429",
                "credit": 133.429,
                "closingBalance": "271.178",
                "txType": "P",

                "securityDescription": "Aditya Birla Sun Life MIP II - Wealth 25 Plan - Growth-Direct Plan",
                "schemeId": "B313GZ",
                "accountId": "10184833790 / 0",
                "amfiCode": "120705",
                "isin": "INF209K01XH4"
              }
            ],

            "mf_ageing": [
              {
                "purchaseDate": "05-Apr-2017",
                "purchaseTxDetails": "Purchase - via Internet",
                "purcahsedUnits": 133.429,
                "purchasePrice": 37.4731,
                "type": "ACTIVE",

                "redemptionDate": "18-Aug-2017",
                "redemptionPrice": 39.9579,
                "ageOfUnitsSold": 135,
                "unitsCategory": "SHORTTERM",

                "unitsSquaredOff": 0,
                "unitsUnsold": 133.429,
                "profitOrLoss": 331.5443792,

                "exitLoadApplicable": "YES",
                "exitLoadValue": 53.315426391,
                "exitLoadPercentage": 1,
                "DaystoUnlocking": 230,

                "folioId": "10184833790 / 0",
                "securityDescription": "Aditya Birla Sun Life MIP II - Wealth 25 Plan - Growth-Direct Plan",
                "schemeType": "DEBT",
                "schemeId": "B313GZ",
                "isin": "INF209K01XH4",
              }
            ],

            "mf_taxInfo": [
              {
                "financialYear": "2017 - 2018",
                "activeUnitsTax_ST": 99.46331376,
                "schemeType": "DEBT",
                "securityDescription": "Aditya Birla Sun Life MIP II - Wealth 25 Plan - Growth-Direct Plan",
                "schemeId": "B313GZ",
                "activeUnitsSquared_ST": 133.429,
                "folioId": "10184833790 / 0",
                "isin": "INF209K01XH4",
                "activeUnitsGainorLoss_ST": 331.5443792
              }
            ],

            "mf_returnsInfo": 
            {
              "closingNetAmount": 10835.7,
              "amountWithdrawn": 0,
              "accountId": "10184833790 / 0",
              "annualizedReturn":-8.140030877945161,
              "schemeType": "DEBT",
              "amountInvested": 5000,
              "securityDescription": "Aditya Birla Sun Life MIP II - Wealth 25 Plan - Growth-Direct Plan",
              "schemeId": "B313GZ",
              "advisorName": "DIRECT",
              "isin": "INF209K01XH4",
              "dividendPaid": 0
            },
          }
        ],

        "mf_returnsInfo":[
          {
            "closingNetAmount":2038378.4300000002,
            "unrealizedProfitorLoss":-179202.56999999983,
            "amountWithdrawn":0,
            "annualizedReturn":-8.140030877945161,
            "schemeType":"Sub Total - DEBT",
            "amountInvested":2217581,
            "annualizedReturn_3years":-8.22249270241133,
            "absoluteReturn":-8.080993208365324,
            "dividendPaid":0
          }
        ],
        
        "Panwise_returnsInfo":[
          
          {
            "closingNetAmount":1822658.6099999999,
            "unrealizedProfitorLoss":-162192.39000000013,
            "amountWithdrawn":0,
            "groupName":"SUB TOTAL",
            "annualizedReturn":-7.58439743116152,
            "amountInvested":1984851,
            "annualizedReturn_3years":-7.669462913949933,
            "absoluteReturn":-8.171514637622678,
            "pan":"XXXXXXXXXX",
            "dividendPaid":0
          },

          {
            "closingNetAmount":1822658.6099999999,
            "unrealizedProfitorLoss":-162192.39000000013,
            "amountWithdrawn":0,
            "annualizedReturn":-7.58439743116152,
            "schemeType":"DEBT",
            "amountInvested":1984851,
            "annualizedReturn_3years":-7.669462913949933,
            "absoluteReturn":-8.171514637622678,
            "pan":"XXXXXXXXXX",
            "dividendPaid":0
          },

        ],

        "advisor_returnsInfo":[
          {
            "closingNetAmount":211141.45,
            "unrealizedProfitorLoss":-8858.549999999988,
            "amountWithdrawn":0,
            "annualizedReturn":-1.7004657140460293,
            "amountInvested":220000,
            "annualizedReturn_3years":-2.0489497773675627,
            "absoluteReturn":-4.026613636363631,
            "advisorName":"DIRECT",
            "dividendPaid":0},
        ],
      }
    },
  }
]
```

We showcase an example for a cams/karvy statement as a input document and the corresponding output. The system automatically is able to recognize the document as a cams statement and give an output. The document format is given in the tag explained as document format. Sample JSON Output example is shown on the right tab. 

Highlevel JSON structure is explained in <a href="#how-to-read-output">How To Read Output</a> section above

Specific to this statement, explanation about various sections is as below:

1. Within a particular group - mutualFund holdings are represented by mutualFunds[n]

2. Within a particular holding - Mutualfund transactions is represented by mftransactions[n]

3. Within a particular holding - you'll see analytics sections represented in below sections:

      3.1.	mf_returnsInfo[n] : returns information (overall and 3 year).

      3.2.	mf_ageing[n] : More useful for teams who wants a deepdive on FIFO application on transactions. 

      3.3.  mf_taxInfo[n] : Tax information Summary.

4. Within a particular group - analytics section are presented in below sections: 

      4.1.	mf_returnsInfo[n] : returns information (overall and 3 year) for GrandTotal and assetclass (ex: Sub Total - EQUITY)

      4.2.	Panwise_returnsInfo[n] : returns information (overall and 3 year) for each PAN and PAN+assetclass combinations.

      4.3.  advisor_returnsInfo[n] : returns information (overall and 3 year) for each advisorName in document.


<aside class="success">
Don't be surprised to see few tags in JSON which are not present in document. MFIT API automatically enriches commonly useful information. 
</aside>


## NSDL eCAS statement

> Sample NSDL JSON is structured like this:

```json
[
  {
  "fileName": "NSDLe-CAS_102348389_JUN_2017.pdf",
  "consolidatePortfolioValue": "7,36,01,343.13",
  "endDate": "30-Jun-2017",
  "documentFormat": "NSDL",
  "startDate": "01-Jun-2017",
  "errors": [],

  "personalInfo": {
    "address": "xxxxxxxx",
    "identifierId": "102348389",
    "name": "ARUN BABA"
  },

  "groups": {
    "arun baba": {
      "assetClassComposition": [
        {
          "percentage": "14.74%",
          "assetClass": "Equities (E)",
          "value": "72,89,246.05"
        },
        {
          "percentage": "0.00%",
          "assetClass": "Preference Shares (P)",
          "value": "0.00"
        },
        {
          "percentage": "26.23%",
          "assetClass": "Mutual Funds (M)",
          "value": "1,29,71,402.01"
        },
        {
          "percentage": "8.94%",
          "assetClass": "Corporate Bonds (C)",
          "value": "44,20,000.00"
        },
      ],
      "mf_returnsInfo": [
        {
          "closingNetAmount": 24753235.4,
          "amountWithdrawn": 0,
          "returnsCalculationRemarks": "Can't compute returns - has OpeningUnits",
          "schemeType": "GRAND TOTAL",
          "amountInvested": 0,
          "dividendPaid": 15674.83
        },
      ],
      "Panwise_returnsInfo": [
        {
          "closingNetAmount": 24753235.4,
          "amountWithdrawn": 0,
          "returnsCalculationRemarks": "Can't compute returns - has OpeningUnits",
          "groupName": "GRAND TOTAL",
          "amountInvested": 0,
          "dividendPaid": 15674.83
        },
      ],
      "advisor_returnsInfo": [
        {
          "closingNetAmount": 24753235.4,
          "amountWithdrawn": 0,
          "returnsCalculationRemarks": "Can't compute returns - has OpeningUnits",
          "amountInvested": 0,
          "advisorName": "Unknown",
          "dividendPaid": 15674.83
        },
      ],
      "accounts": [
        {
          "numIsinsOrSchems": "11",
          "serviceProviderName": "HDFC BANK LTD",
          "holdingMode": "JOINT",
          "aadhar": "Not Registered",
          "depository": "NSDL",
          "pan": "xxxxx",
          "linkedBankAccountNumber": "xxxx",
          "value": "54,25,531.90",
          "Dp Id": "IN300476",
          "email": "Not Registered",
          "jointholder1Mobile": "Not Registered",
          "Client Id": "40201922",
          "jointholder1DOB": "Not Registered",
          "linkedBankAccountName": "INDIAN OVERSEAS BANK",
          "jointholder1Email": "Click here to Register",
          "linkedBankAccountIFSC": "IOBA0000052",
          "accountType": "DEMAT",
          "mobile": "Not Registered",
          "nomineeName": "Not Registered",
          "jointholder1Aadhar": "Not Registered",
          "jointholder1Name": "SEEMA BABA",
          "accountId": "IN300476_40201922",
          "groupName": "arun baba seema baba",
          "holderDetails": [
            {
              "dob": "Not Registered",
              "name": "ARUN BABA",
              "mobile": "xxxx",
              "aadhar": "Not Registered",
              "pan": "xxxx",
              "email": "xxxx@xxxxx.com"
            },
            {
              "dob": "Not Registered",
              "name": "SEEMA BABA",
              "mobile": "Not Registered",
              "aadhar": "Not Registered",
              "pan": "xxxxx",
              "email": "Click here to Register"
            }
          ],
          "dob": "Not Registered",
          "name": "ARUN BABA",
          "jointholder1Pan": "xxxxx"
        },
      ],
      "cdslEquities": [
        {
          "safeKeepBal": "0.000",
          "marketPrice": "643.25",
          "freeBal": "1.000",
          "pledgedBal": "0.000",
          "pledgeeBal": "0.000",
          "lockedInBal": "0.000",
          "closingBalance": "1.000",
          "securityDescription": "RELIANCE CAPITAL LIMITED EQUITY SHARES",
          "earmarkedBal": "0.000",
          "serviceProviderName": "ANAND RATHI SHARE AND STOCK BROKERSLIMITED",
          "lentBal": "0.000",
          "closingNetAmount": "643.25",
          "accountId": "12010600_02623174",
          "groupName": "arun baba",
          "pledgeSetupBal": "0.000",
          "isin": "INE013A01015"
        },
      ],
      "equities": [
        {
          "closingNetAmount": "5,19,717.00",
          "accountId": "IN300476_40201967",
          "marketPrice": "2,362.35",
          "groupName": "arun baba",
          "stockSymbol": "TCS.NSE",
          "faceValue": "1.00",
          "closingBalance": "220",
          "securityDescription": "TATA CONSULTANCY SERVICES LIMITED ",
          "serviceProviderName": "HDFC BANK LTD",
          "isin": "INE467B01029"
        }
      ],
      "cdslDematMutualFunds": [
        {
          "safeKeepBal": "0.000",
          "freeBal": "5,17,844.937",
          "schemeType": "EQUITY",
          "mf_ageing": [],
          "closingBalance": "5,17,844.937",
          "securityDescription": "ICICI PRUDENTIAL MUTUAL FUND",
          "dividendReinvestmentFlag": "N",
          "schemeId": "P8177P",
          "amcCode": "ICICIPrudentialMutualFund_MF",
          "serviceProviderName": "EDELWEISS SECURITIES LIMITED",
          "advisor": "Unknown",
          "mf_returnsInfo": {
            "closingNetAmount": 7566750.22,
            "amountWithdrawn": 0,
            "accountId": "12032300_01206167",
            "returnsCalculationRemarks": "Can't compute returns - zero Amount Invested",
            "schemeType": "EQUITY",
            "amountInvested": 0,
            "securityDescription": "ICICI PRUDENTIAL MUTUAL FUND",
            "schemeId": "P8177P",
            "advisorName": "Unknown",
            "isin": "INF109K017O2",
            "dividendPaid": 0
          },
          "pledgeBal": "0.000",
          "navason31jan2018": "14.8878",
          "nav": "14.61",
          "pledgeeBal": "0.000",
          "rtaPrefix": "N.A",
          "lockedInBal": "0.000",
          "schemeBucket": "Open Ended Schemes ",
          "advisorName": "Unknown",
          "lentBal": "0.000",
          "closingNetAmount": "75,66,750.22",
          "bseSchemeCode": "8177P-DP",
          "accountId": "12032300_01206467",
          "groupName": "arun baba seema baba",
          "amfiCode": "120365",
          "pledgedSetupBal": "0.000",
          "isin": "INF109K017O2",
          "schemeSubcategory": " Arbitrage Fund ",
          "earMarkedBal": "0.000"
        }
      ],
      
      "corporateBonds": [
        {
          "closingNetAmount": "44,20,000.00",
          "accountId": "IN300476_40201922",
          "groupName": "arun baba seema baba",
          "coupon": "8.63",
          "maturityDate": "13-Jan-2029",
          "faceValue": "5,000.00",
          "closingBalance": "884",
          "amountInvested": "44,20,000.00",
          "securityDescription": "NATIONAL HOUSING BANK Fixed Interest Bonds",
          "serviceProviderName": "HDFC BANK LTD",
          "isin": "INE557F07090",
          "frequency": " Once a year"
        }
      ],
      "mutualFunds": [
        {
          "kycStatus": "OK",
          "annualizedReturn": "4.88",
          "registrar": "CAMS",
          "schemeType": "LIQUID",
          "mf_ageing": [
            {
              "purcahsedUnits": 34938.409,
              "redemptionDate": "30-Jun-2017",
              "redemptionPrice": 100.225,
              "schemeType": "LIQUID",
              "securityDescription": "Birla Sun Life Cash Plus - Weekly Dividend-Regular Plan",
              "unitsSquaredOff": 0,
              "schemeId": "B153WD",
              "unitsCategory": "INSUFFICIENT_INFO",
              "folioId": "1017905021",
              "type": "ACTIVE",
              "financialYear": "2017 - 2018",
              "purchaseTxDetails": "Opening Balance",
              "unitsUnsold": 34938.409,
              "isin": "INF209KA1LK7"
            }
          ],
          "closingBalance": "34,938.409",
          "securityDescription": "Birla Sun Life Cash Plus - Weekly Dividend-Regular Plan",
          "dividendReinvestmentFlag": "N",
          "schemeId": "B153WD",
          "amcCode": "Aditya Birla Sun Life Mutual Fund",
          "serviceProviderName": "Birla Sun Life Mutual Fund",
          "holdingMode": "JOINT",
          "advisor": "Unknown",
          "jointholder1Kycstatus": "OK",
          "ucc": "MFBRLA0014",
          "mf_returnsInfo": {
            "closingNetAmount": 3501702.04,
            "amountWithdrawn": 0,
            "accountId": "1017905021",
            "returnsCalculationRemarks": "Can't compute returns - has OpeningUnits (3 Year IRR) has openingUnits",
            "schemeType": "DEBT",
            "amountInvested": 0,
            "securityDescription": "Birla Sun Life Cash Plus - Weekly Dividend-Regular Plan",
            "schemeId": "B153WD",
            "advisorName": "Unknown",
            "isin": "INF209KA1LK7",
            "dividendPaid": 15674.83
          },
          "navason31jan2018": "108.1071",
          "pan": "xxxxxxx",
          "openingBalance": "34938.409",
          "email": "xxxx@xxxx.net",
          "nav": "100.2250",
          "rtaPrefix": "N.A",
          "accountType": "MF",
          "schemeBucket": "Open Ended Schemes ",
          "mobile": "XXXXXXXXXX691",
          "nomineeName": "Registered",
          "advisorName": "Unknown",
          "closingNetAmount": "35,01,702.04",
          "jointholder1Name": "Seema Baba",
          "bseSchemeCode": "BS153WD-DP",
          "accountId": "1017905021",
          "unrealizedProfitorLoss": "-1,935.59",
          "groupName": "arun baba seema baba",
          "mftransactions": [
            {
              "date": "30-JUN-2017",
              "transactionDetails": "Dividend Payout @ Rs.0.09312296 per unit",
              "amount": "3253.57",
              "nav": "100.2250",
              "annualizedReturn": "4.88",
              "closingBalance": "34938.409",
              "securityDescription": "Birla Sun Life Cash Plus - Weekly Dividend-Regular Plan",
              "schemeId": "B153WD",
              "units": "0.000",
              "txType": "DP",
              "serviceProviderName": "Birla Sun Life Mutual Fund",
              "closingNetAmount": "35,01,702.04",
              "accountId": "1017905021",
              "unrealizedProfitorLoss": "-1,935.59",
              "groupName": "arun baba seema baba",
              "amfiCode": "100048",
              "ucc": "MFBRLA0014",
              "amountInvested": "35,03,637.63",
              "credit": "0.000",
              "averageCost": "100.2804",
              "openingBalance": "34938.409",
              "isin": "INF209KA1LK7"
            }
          ],
          "amfiCode": "100048",
          "amountInvested": "35,03,637.63",
          "name": "Arun Baba",
          "averageCost": "100.2804",
          "jointholder1Pan": "xxxxx",
          "isin": "INF209KA1LK7",
          "schemeSubcategory": " Liquid Fund "
        },
      ]
    },
  },
}
]
```

We showcase an example for a NSDL statement as a input document and the corresponding output. The system automatically is able to recognize the document as a NSDL statement and give an output. The document format is given in the tag explained as document format. Sample JSON Output example is shown on the right tab. 

Highlevel JSON structure is explained in <a href="#how-to-read-output">How To Read Output</a> section above

Specific to this statement, explanation about various sections is as below:

1. Within a particular group -

      1.1. DEMAT account holdings where DP is either NSDL/CDSL is represented by accounts[n]. This contains KYC details of these holding accounts.

      1.2. MutualFund holdings outside Depository Participant(DP) purview are represented by mutualFunds[n]. This contains both holding and KYC details of the respective folio number

      1.3. MutualFund holdings where DP is NSDL/CDSL is represnted by nsdlDematMutualFunds[n] and cdslDematMutualFunds[n] respectively.

      1.4. Equities where DP is NSDL(or DEMAT is under NSDL) is represented by equities[n] and where DP is CDSL is represented by cdslEquities[n]

      1.5. Corporate Bonds irrepective of the DP is represented by corporateBonds[n]

      1.6. Very specific to NSDl - assetClassComposition[n] lists out asset class breakup of holdings which is present in the document. 

2. Within a particular holding - 

      2.1. Mutualfund transactions outside of DP purview are represented by mftransactions[n]

      2.2. Mutualfund transactions which are within DP purview (or bought from DEMAT accounts) are represented by dematmfTransactions[n]

      2.3. Equity transactions where DEMAT is held by NSDL is represented by equityTransactions[n]

      2.4. Equity transactions where DEMAT is held by CDSL is represented by cdslEquityTransactions[n]

      2.5  Bond transactions irrespectve of the DP is represented by bondTransactions[n]


3. Within a particular Mutual Fund holding - you'll see analytics sections represented in below sections:

      3.1.	mf_returnsInfo[n] : returns information (overall and 3 year).

      3.2.	mf_ageing[n] : More useful for teams who wants a deepdive on FIFO application on transactions. 

      3.3.  mf_taxInfo[n] : Tax information Summary.

4. Within a particular group - mutual fund analytics section are presented in below sections: 

      4.1.	mf_returnsInfo[n] : returns information (overall and 3 year) for GrandTotal and assetclass (ex: Sub Total - EQUITY)

      4.2.	Panwise_returnsInfo[n] : returns information (overall and 3 year) for each PAN and PAN+assetclass combinations.

      4.3.  advisor_returnsInfo[n] : returns information (overall and 3 year) for each advisorName in document.


<aside class="success">
Don't be surprised to see few tags in JSON which are not present in document. MFIT API automatically enriches commonly useful information. 
</aside>

## Bank Statement


> Sample Bank Statement JSON is structured like this:

```json
[
 {
  
  "fileName": "RRCSB 64609.pdf",
  "documentFormat": "AXIS BANK CURRENT ACCOUNT",
  "startDate": "01-04-2019",
  "endDate": "08-05-2019",
  "errors": [],

  "personalInfo": {
    "address": "xxxxxx",
    "name": "SOME NAME STOCK BROKERS PVT LTD N S EEXCHANGE D"
  },
  
  "bankAccountInfo": {
    "accountType": "CA-NSCCL EXCH DUES OPERAT",
    "customerID": "xxxxxx",
    "currency": "INR",
    "accountNumber": "xxxxxxxx"
  },
  
  "bankAccountsSummary": {
    "TotalamountWithdrawn": 18322,
    "debitCount": 1,
    "closingBalance": "31678.00",
    "creditCount": 1,
    "TotalamountDeposited": 50000,
    "openingBalance": "0.00"
  },

  "bankTransactions": [
    {
      "date": "03-04-2019",
      "transactionDetails": "INB/IFT/",
      "amount": "50000.00",
      "closingBalance": "50000.00",
      "credit": "50000.00",
      "openingBalance": "0.00"
    },
    {
      "date": "03-04-2019",
      "transactionDetails": "EXCHANGE DUES - 00103",
      "amount": "18322.00",
      "closingBalance": "31678.00",
      "debit": "18322.00",
      "openingBalance": "50000.00"
    },
  ]
}
]
```

We showcase an example for a BANK statement as a input document and the corresponding output. The system automatically is able to recognize the document as a BANK statement and give an output. The document format is given in the tag explained as document format. Sample JSON Output example is shown on the right tab. 

Highlevel JSON structure is explained in <a href="#how-to-read-output">How To Read Output</a> section above

Specific to this statement, explanation about various sections is as below:

1. Bank Account Info is provided in the section "bankAccountInfo"

2. Bank Account Summary is provided in the section "bankAccountsSummary"

3. Bank Transactions are listed in the section bankTransactions[n]


<aside class="success">
Don't be surprised to see few tags in JSON which are not present in document. MFIT API automatically enriches commonly useful information. 
</aside>

