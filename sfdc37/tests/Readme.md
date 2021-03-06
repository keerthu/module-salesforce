## Compatibility

| Ballerina Version  | API Version  |
| ------------------ | ------------ |
| 0.983.0            |   v37.0      |

### Prerequisites

Create a Salesforce developer account and create a connected app by visiting [Salesforce](https://www.salesforce.com) and obtain the following parameters:
* Base URl (Endpoint)
* Client Id
* Client Secret
* Access Token
* Refresh Token
* Refresh URL

IMPORTANT: This access token and refresh token can be used to make API requests on your own account's behalf. 
Do not share your access token, client secret with anyone.

Create an external id field under "Account" SObject, named with `"SF_ExternalID__c"` in your connected app. 
This field will be used in the test cases related with external id. 

Visit [here](https://help.salesforce.com/articleView?id=remoteaccess_authenticate_overview.htm) for more information on obtaining OAuth2 credentials.

### Working with Salesforce REST endpoint.

In order to use the Salesforce endpoint, first you need to create a 
Salesforce Client endpoint by passing above mentioned parameters.

Find the way of creating Salesforce endpoint as following. 

```ballerina
endpoint Client salesforceClient {
    clientConfig:{
        url:url,
        auth:{
            scheme:http:OAUTH2,
            accessToken:accessToken,
            refreshToken:refreshToken,
            clientId:clientId,
            clientSecret:clientSecret,
            refreshUrl:refreshUrl
        }
    }
};

```

#### Running salesforce tests
Create `ballerina.conf` file in `module-salesforce`, with following keys:
* ENDPOINT
* ACCESS_TOKEN
* CLIENT_ID
* CLIENT_SECRET
* REFRESH_TOKEN
* REFRESH_URL

Assign relevant string values generated for Salesforce app. 

Go inside `module-salesforce` and give the command `ballerina init`using terminal and run test.bal file 
using `ballerina test sfdc37 --config ballerina.conf` command.

* Sample Test Function

```ballerina
@test:Config
function testGetResourcesByApiVersion() {
    log:printInfo("salesforceClient -> getResourcesByApiVersion()");
    string apiVersion = "v37.0";
    response = salesforceClient -> getResourcesByApiVersion(apiVersion);
    match response {
        json jsonRes => {
            test:assertNotEquals(jsonRes, null, msg = "Found null JSON response!");
            try {
                test:assertNotEquals(jsonRes["sobjects"], null);
                test:assertNotEquals(jsonRes["search"], null);
                test:assertNotEquals(jsonRes["query"], null);
                test:assertNotEquals(jsonRes["licensing"], null);
                test:assertNotEquals(jsonRes["connect"], null);
                test:assertNotEquals(jsonRes["tooling"], null);
                test:assertNotEquals(jsonRes["chatter"], null);
                test:assertNotEquals(jsonRes["recent"], null);
            } catch (error e) {
                test:assertFail(msg = "Response doesn't have required keys");
            }
        }
        SalesforceConnectorError err => {
            test:assertFail(msg = err.message);
        }
    }
}

```

* Sample Result 

```ballerina
---------------------------------------------------------------------------
    T E S T S
---------------------------------------------------------------------------
---------------------------------------------------------------------------
Running Tests of module: sfdc37
---------------------------------------------------------------------------
...
2018-04-13 13:35:19,154 INFO  [sfdc37] - salesforceClient -> getResourcesByApiVersion() 
...
sfdc37............................................................. SUCCESS
---------------------------------------------------------------------------
```
