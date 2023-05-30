# Restclient-Swagger-api-tests
VSCode REST Client Syntax
The REST Client plugin requires just a plain text file with the extension .http or .rest. The basic syntax is very simple as following

### Request 1
[GET|POST] [REST API URL] # Request Line
[Request Headers]
[Request Body]
### Request 2
[GET|POST] [REST API URL]
[Request Headers]
[Request Body]
The file can contain multiple requests, each request is separated by ### delimiter (three or more consecutive #).

Request Line
The first line of the request is the Request Line. It contains the request method (GET or POST), space, and then followed by the API URL endpoint.

Examples

### RDP historical-pricing: retrieve time series pricing Interday summaries data
GET https://api.refinitiv.com/data/historical-pricing/v1/views/interday-summaries/FB.O HTTP/1.1
or

GET https://api.refinitiv.com/data/historical-pricing/v1/views/interday-summaries/FB.O
or

### RDP Auth Service
POST https://api.refinitiv.com/auth/oauth2/v1/token HTTP/1.1
Request Line: Query Parameters
You can set the API query parameters into API URL directly as follows:

### RDP News Service: Get News Headlines
GET https://api.refinitiv.com/data/news/v1/headlines?query="USA"
If the request contains multiple parameters, you can split it into multiple lines. The REST Client extensions parse the lines immediately after the Request Line which starts with ? and & operations.

### RDP ESG /VIEWS/MEASURES-FULL: Returns scores and measures with full history
GET https://api.refinitiv.com/data/environmental-social-governance/v1/views/measures-full
 ?universe=FB.O
 &start=-5
 &end=0
Request Header
The lines immediately after the Request Line are Request Header. The supported syntax is the field-name: field-value format, each line represents one header. The Authentication also can be set in this Request Header section.

### RDP ESG /UNIVERSE: Return the full universe available for ESG
GET https://api.refinitiv.com/data/environmental-social-governance/v1/views/basic?universe=IBM.N HTTP/1.1
Content-Type: application/json
Authorization: Bearer <access_token>
Variables
You can declare your File variables to keep the values and reuse them in the script such as API base endpoint, version with @variableName = variableValue syntax in a separate line from request block. Then the request can use the defined variable with {{variableName}} syntax. Please note that you do not need the "" or '' characters for a string value.

Example:

@baseUrl = https://api.refinitiv.com
@rdpVersion = v1
###
POST {{baseUrl}}/auth/oauth2/{{rdpVersion}}/token HTTP/1.1
###
@symbol = "IBM.N"
GET {{baseUrl}}/data/environmental-social-governance/{{rdpVersion}}/views/basic?universe={{symbol}}
The extension will parse the above Request Lines to https://api.refinitiv.com/auth/oauth2/v1/token and https://api.refinitiv.com/data/environmental-social-governance/v1/views/basic?universe=IBM.N endpoints.

Note: the variable name must not contain any spaces.

Variables: Request Variables
You can declare Request Variables to get a request or response message content. The syntax is just # @name requestName on a line before the Request Line. Once the request is sent, the script can access response (or request) message information from {{requestName.(response|request).(body|headers).(*|JSONPath|XPath|Header Name)}} syntax.

This feature is mandatory for RDP APIs because the consumer needs to get a token from RDP Auth Service first, then use that token to request further protected resources from the platform.

### RDP Auth Service
# @name rdpAuth
POST {{baseUrl}}/auth/oauth2/{{rdpVersion}}/token HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Authorization: token {{client_id}}
Accept: application/json
username={{username}}&password={{password}}&client_id={{client_id}}&grant_type=password&scope=trapi&takeExclusiveSignOnControl=true
#### Variable Response
@accessToken = {{rdpAuth.response.body.$.access_token}}
### RDP ESG /UNIVERSE: Return the full universe available for ESG
@symbol = "IBM.N"
GET {{baseUrl}}/data/environmental-social-governance/{{rdpVersion}}/views/basic?universe={{symbol}} HTTP/1.1
Content-Type: application/json
Authorization: Bearer {{accessToken}}
The example above named RDP Auth Service request with rdpAuth name, then gets access token from {{rdpAuth.response.body.$.access_token}} command and set it to variable name accessToken. Then set this access token to the request message Authentication in all subsequent RDP APIs requests with Authorization: Bearer {{accessToken}} Request header syntax.
