# Deploy a Proxy API in Anypoint Platform with Postman

It has been long time that I wanted to write a post on how to automate API proxy deployment by only Platform API calls. 

Most of the time I do démo with UI to my prospects on API Manager topics and I have always the same question coming from them: “How are we going to add this to our CI/CD pipeline.”  

So I have decided to show by only using API calls how we can import your swagger to Anypoint Exchange, Create the proxy, deploy it, add SLA tiers, apply policies, tag it then activate monitoring with only one postman collection.


The steps the collection contains are:

* Import your swagger file to Anypoint Exchange 
* Create an API in API Manager from an API in Exchange
* Deploy the API proxy  to Runtime Manager
* Add SLA tiers ( Free, Premium) to your API in Anypoint API Manager
* Apply the following policies to your API in Anypoint API Manager:
    * Rate Limiting SLA Based policy
    * JWT Validation Policy
* Tag your API
* Activate Monitoring and Visualiser on Anypoint Runtime Manager for your API

For this postman collection I have only used the APIs available on [Anypoint Platform APIs](https://anypoint.mulesoft.com/exchange/portals/anypoint-platform/). 

You should run by order the API Calls as it is already ordered in postman collection. Because each API call gets the values from the response and sets the postman environment variables that will be used for the following API calls. 

## Pre-requisite

1. You should create a connected app (Access Management → Connected Apps) with the following scopes on your choose of Business org and Environment: Runtime Manager, Exchange Contributor, API Manager, CloudHub Org Admin 
2. Your swagger file
3. Your Business Group Name (the name of the business org to which you want to deploy)
4. Your Environment Name ( the name of the environment to which your want to deploy)
5. Your Environment client_id and client_secret (we need it to activate monitoring)
6. ![Environment client id Secret](/images/env_app_id_secret.png)
7. Import the Postman Collection file to your Postman ( [GitHub repository](https://github.com/ozgurkarahan/Mulesoft-Api-Manager-CI-CD-Postman/blob/master/CI-CD%20Create%20Proxy%20API%20-%20Api%20Manager.postman_collection.json) )
8. Import the Postman environment variables to your Postman ([GitHub Repository](https://github.com/ozgurkarahan/Mulesoft-Api-Manager-CI-CD-Postman/blob/master/CI-CD%20Create%20Proxy%20API%20-%20Api%20Manager.postman_environment.json))

## Postman Environment variables

The following postman environment variables must be filled in:

* anypoint_host: [https://anypoint.mulesoft.com](https://anypoint.mulesoft.com/)
* connected_app_id: The id of the connected app you have created in the pre-requisite step 1
* connected_app_secret: The id of the connected app you have created in the pre-requisite step 1
* env_name: The name of the environment to which you want to deploy your API. ex: Sandbox, Design or Production
* org_name: The  name of Anypoint Business Group to which you want to publish your API
* environment_app_id: your environment client Id that you have get from the pre-requisite step 6
* environment_app_secret: your environment client secret that you have get from the pre-requisite step 6
* domainName: The domain name you want to use as part of your API URI. _It will be the identifier of you app in RM_, it must be unique. ( `domainname`-[de-c1.cloudhub.io](http://de-c1.cloudhub.io/))
* exchangeAssetId: The assetId that will be used in Anypoint Exchange 

## Steps in Postman Collection

_**The very first two steps are the preparation requests to get access token, env_id and org_id**_

### 0.1 - Get Access Token (POST)

This steps calls the token endpoint to get an access_token to be used as bearer token for the following steps.

* gets acces_token for the connected app that I have created in Anypoint Platform.
* sets the acces_token in postman environment variable to use for the following requests

### 0.2 - Get Profile (org Id and Sandbox Env Id) (GET)

For the most of the following steps you will need environment id and organisation id. This request calls the accounts/api/profile to get information on your platform (business org, env, ...). 
The env_name and org_name variable let us to query the response result and extract Ids

_**Once we have the variable set for: access_token, org_id and env_id we can start to import the swagger to Exchange, create your api in api manager and depoy to runtime manager**_

### 1 - Upload swagger to Exchange (POST)

This request will add your swagger to exchange. Check the body of the request to customise the parameters. The important one is the _**asset**_: you should give the full path to your swagger file as value to asset. 
![Image: image.png](/images/asset_param.png)
### 2 - Create an Api From Exchange (POST)

Creates an API in Api Manager from an existing Exchange API (check body of the request if you want to customise it). It sets apiId postman environment variable from the response.

### 3 - Deploy proxy for an existing api (POST)

Creates a proxy for the given apiId, domainName(must be unique, it is part of your api url), env_id and env_name

**_Now your API is imported to exchange, created in API Manager and deployed to Runtime Manager. The following steps are to customise your APIs: SLA Tiers (Subscription), Policies, Tag and activate monitoring on your API._ **

### 4 - Add premium SLA tiers to an existing api (POST)

This request creates a SLA tiers for the given apiId. Check body of the request to customise it

### 4.1 - Add free SLA tiers to an existing api (POST)

This request creates a SLA tiers for the given apiId. Check body of the request to customise it

### 4.2 - SLA tiers from an existing api (GET)

You can use this request to check the SLA tiers created previsouly

### 5 - Apply Rate limiting SLA Based policy to an existing api

Applies the standard Rate limiting SLA based policy to your API. This policy wil use the SLA tiers defined in the previous steps.

### 5.1 - Add Tag to an existing api #ratelimitingSLAbased

Add the following tag to your api: ratelimitingSLAbased

### 5.2 - Apply Jwt validation policy to an existing api

Applies the standard JWT validation policy to your API. Check the body of the request to customise your settings.

### 5.3 - Add Tag to an existing api #jwt

Add the following tag to your api: jwt

### 5.4 - Get Api(s) By query (tag name)

You can query your API manager by tag name. This lets you to tag your API properly to set a context than query easily all APIs with the specific tags


### Optional - Enable monitoring/visualiser in Runtime Manager

This steps is optional. It lets you to activate monitoring and visualiser on your proxy app in runtime manager.










