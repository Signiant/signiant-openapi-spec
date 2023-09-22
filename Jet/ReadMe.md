# Code Generation and Usage for [Jet OAS specification](https://developer.signiant.com/openapi/jetOpenAPI.yaml)
This documentation includes instructions to generate and test API client for different programming languages. Code for OAS specification can be generated using different tools like [Swagger Codegen](https://swagger.io/docs/open-source-tools/swagger-codegen/) or [OpenAPI Generator](https://github.com/OpenAPITools/openapi-generator).

## Client generation via Swagger Codegen

```
If you have swagger-codegen installed, use
swagger-codegen generate -i OAS_SPEC_PATH -l LANGUAGE -o OUTPUT_DIRECTORY
Else, you can use
java -jar swagger-codegen-cli.jar generate -i OAS_SPEC_PATH -l LANGUAGE -o OUTPUT_DIRECTORY
```

## Client generation via OpenAPI Generator

```
If you have swagger-codegen installed, use
openapi-generator-cli generate -i OAS_SPEC_PATH -g LANGUAGE -o OUTPUT_DIRECTORY
Else, you can use
java -jar openapi-generator-cli.jar generate -i OAS_SPEC_PATH -g LANGUAGE -o OUTPUT_DIRECTORY
```

**LANGUAGE** can be java, python, nodejs-server, go

## API Client Usage

### NodeJS

NodeJs API client can be tested as follows:

- Install [NodeJs](https://nodejs.org/en/download)
- Update your server url to http://localhost:8080/ in the OAS specification file in the generated source.
- Add axios dependency in package.json and install `npm i axios`
- Run the server - `npm start`
- Execute API call, for example, http://localhost:8080/v1/endpoints/ from postman with Authorization: `TOKEN_TYPE` `ACCESS_TOKEN` in header.

```
exports.getEndpoint = function(endpointId) {
    var axios = require("axios");
    var options = {
        method: "GET",
        url: "API_BASE_URL/v1/endpoints/",
        headers: {
            authorization: `TOKEN_TYPE` `ACCESS_TOKEN`,
        },
    };
    axios(options)
    .then(function (response) {
        console.log(response);
    })
    .catch(function (error) {
        console.log(error);
    });
}
```

### Python

- Install [Python](https://www.python.org/downloads/)
- Add a new file at the root level of the project - `touch main.py`.
- Run as `python main.py`
```
from __future__ import print_function
import time
import swagger_client
from swagger_client.rest import ApiException
from pprint import pprint

configuration = swagger_client.Configuration()
configuration.username = '{client_id}'
configuration.password = '{client_secret}'

# create an instance of the API class
api_instance = swagger_client.AuthenticationApi(
    swagger_client.ApiClient(configuration))

try:
    api_response = api_instance.create_token({'grant_type': 'client_credentials'})
    pprint(api_response)

    api_instance = swagger_client.SubscriptionsApi(swagger_client.ApiClient(configuration))
    api_instance.api_client.default_headers['Authorization'] = api_response.token_type + ' ' + api_response.access_token
    api_response = api_instance.get_subscriptions()
    pprint(api_response)
except ApiException as e:
    print("Exception when calling API: %s\n" % e)
```

### Go

- Install [Go](https://go.dev/doc/install)
- Create a new project and add the generated client named "go" (or name corresponding to the output directory used while generation) on the root path of the project.
- Add a new file at the root level of the project - `touch main.go`
- Import generated code in `main.go` as `sw "ProjectName/go"` or `sw "./go"`
- Run as `go run main.go`

```
package main
import (
    "fmt"
    "context"
    sw "./go"
)

func main() {
	client := sw.NewAPIClient(sw.NewConfiguration())

	auth := context.WithValue(context.Background(), sw.ContextBasicAuth, sw.BasicAuth{
		UserName: "{client_id}",
		Password: "{client_secret}",
	})

	oauthRequest := sw.OAuthTokenRequest{
		OAuthTokenClientCredentialsGrant: sw.OAuthTokenClientCredentialsGrant{
			GrantType: "client_credentials",
		},
	}
	OAuthTokenResponse, resp, err := client.AuthenticationApi.CreateToken(auth, oauthRequest)
    authWithAccessToken := context.WithValue(context.Background(), sw.ContextAccessToken, OAuthTokenResponse.AccessToken)
    subscriptionsList, resp, err := client.SubscriptionsApi.GetSubscriptions(authWithAccessToken)
    if err != nil {
		fmt.Printf("Error getting subscriptions: %s\n", err.Error())
		return
	}
}
```

###Note:
- If you encounter the error `undefined: ModelMap` after running the command: `go run main.go`, you can fix it by removing the "ModelMap" declaration from the file giving error.

### Java

- Install [Java](https://www.oracle.com/in/java/technologies/downloads/)
- Setup [JAVA_HOME](https://docs.oracle.com/cd/E19182-01/821-0917/inst_jdk_javahome_t/index.html)
- Change directory to `cd src/main/java` in the generated client.
- Add a new file - `touch Main.java`
- Add main method in the `Main.java` as given below.
- Click the Run against the main method and select Run 'Main.main()' in the popup. The IDE (Intellij as an example) starts compiling your code.

```
import org.openapitools.client.ApiClient;
import org.openapitools.client.ApiException;
import org.openapitools.client.Configuration;
import org.openapitools.client.api.EndpointsApi;
import org.openapitools.client.auth.*;
import org.openapitools.client.model.*;
import org.openapitools.client.api.AuthenticationApi;

import java.util.UUID;

public class Main {
public static void main(String[] args) {
ApiClient defaultClient = Configuration.getDefaultApiClient();
defaultClient.setBasePath("https://platform-api-service.services.cloud.signiant.com");

        OAuthTokenClientCredentialsGrant clientCredentialsGrant = new OAuthTokenClientCredentialsGrant();
        clientCredentialsGrant.setClientId("client_id");
        clientCredentialsGrant.setClientSecret("client_secret");
        clientCredentialsGrant.setGrantType(OAuthTokenClientCredentialsGrant.GrantTypeEnum.CLIENT_CREDENTIALS);

        AuthenticationApi apiInstance = new AuthenticationApi(defaultClient);
        OAuthTokenRequest oauthTokenRequest = new OAuthTokenRequest(clientCredentialsGrant);
        try {
            OAuthTokenResponse result = apiInstance.createToken(oauthTokenRequest);
            System.out.println("Token: " + result);

            //Endpoints API
            EndpointsApi api = new EndpointsApi();
            api.getApiClient().setBearerToken(result.getAccessToken());
            EndpointListResponse list = api.listEndpoints();
            System.out.println("List: " + list);


        } catch (ApiException e) {
            System.err.println("Reason: " + e.getMessage());
            e.printStackTrace();
        }
    }
}
```

**Note**:
- This OpenAPI Specification file uses **oneOf** and **allOf** keywords in schemas. As Swagger Codegen doesn't support very well some of these OpenAPI Specification keywords, it's recommended to use OpenAPI generator for Java code generation.
- Generated source for Java gives exception in some APIs if there are additional fields in the response from the server which are not defined in schema in OpenAPI Specification. Please comment the line which is the source of error as all fields are not always required to be defined in schema.
  
`Exception example: The field ADDITIONAL FIELD in the JSON string is not defined in the SCHEMA properties.`