# Code Generation and Usage for [Media Shuttle OAS specification](https://developer.signiant.com/openapi/mediaShuttleOpenAPI.yaml)
This documentation includes instructions to generate and test API client for different programming languages. Code for OAS specification can be generated using different tools like [Swagger Codegen](https://swagger.io/docs/open-source-tools/swagger-codegen/) or [OpenAPI Generator](https://github.com/OpenAPITools/openapi-generator).

## Client generation via Swagger Codegen

If you have swagger-codegen installed, use
```
swagger-codegen generate -i OAS_SPEC_PATH -l LANGUAGE -o OUTPUT_DIRECTORY
```
Else, you can use
```
java -jar swagger-codegen-cli.jar generate -i OAS_SPEC_PATH -l LANGUAGE -o OUTPUT_DIRECTORY
```

## Client generation via OpenAPI Generator

If you have openapi-generator-cli installed, use
```
openapi-generator-cli generate -i OAS_SPEC_PATH -g LANGUAGE -o OUTPUT_DIRECTORY
```
Else, you can use
```
java -jar openapi-generator-cli.jar generate -i OAS_SPEC_PATH -g LANGUAGE -o OUTPUT_DIRECTORY
```

**LANGUAGE** can be java, python, nodejs-server, go

## API Client Usage

### NodeJS

NodeJs API client can be tested as follows:

- Install [NodeJs](https://nodejs.org/en/download)
- Update your server url to http://localhost:8080 in the OAS specification file in the generated source.
- Add axios dependency in package.json and install `npm i axios`
- Run the server - `npm start`
- Execute API call, for example, http://localhost:8080/storage from postman with Authorization: API_KEY in header.

```
exports.getStorage = function(storageId) {
    var axios = require("axios");

    var options = {
        method: "GET",
        url: "API_BASE_URL/storage/{storageIdVal}",
        headers: {
            authorization: API_KEY,
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
- Run as `python3 main.py`
```
from __future__ import print_function
import time
import swagger_client
from swagger_client.rest import ApiException
from pprint import pprint

configuration = swagger_client.Configuration()
configuration.api_key['Authorization'] = API_Key
api_instance = swagger_client.StorageApi(swagger_client.ApiClient(configuration))

try:
    api_response = api_instance.list_storage()
    pprint(api_response)
except ApiException as e:
    print("Exception when calling StorageApi->get_storage: %s\n" % e)
```
**Note:** While trying to access the Portal APIs, you might get an error as follows:
```
ValueError: Invalid value for `type` (Share), must be one of ['send', 'share', 'submit'] 
```
In order to fix above, you need to capitalize the portal types like ['Send', 'Share', 'Submit'] in /swagger_client/models/portal.py

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
    auth := context.WithValue(context.Background(), sw.ContextAPIKey, sw.APIKey{
        Key: API_Key,
    })

    // Call an API method using the client and context
    storageList, resp, err := client.StorageApi.ListStorage(auth, nil)
    if err != nil {
        fmt.Printf("Error getting list of storage: %s\n", err.Error())
        return
    }

    fmt.Printf("Storage list: %v\n", storageList)

    if resp.StatusCode > 400 {
        fmt.Printf("Error response from server: %v\n", resp.Body)
    }
}
```

### Java

- Install [Java](https://www.oracle.com/in/java/technologies/downloads/)
- Setup [JAVA_HOME](https://docs.oracle.com/cd/E19182-01/821-0917/inst_jdk_javahome_t/index.html)
- Change directory to `cd src/main/java` in the generated client.
- Add a new file - `touch Main.java`
- Add main method in the `Main.java` as given below.
- Click the Run against the main method and select Run 'Main.main()' in the popup. The IDE (Intellij as an example) starts compiling your code.

```
import io.swagger.client.ApiClient;
import io.swagger.client.ApiException;
import io.swagger.client.Configuration;
import io.swagger.client.api.PortalsApi;
import io.swagger.client.auth.ApiKeyAuth;
import io.swagger.client.model.PortalList;

public class Main {
    public static void main(String[] args) throws ApiException {
        ApiClient defaultClient = Configuration.getDefaultApiClient();
        defaultClient.setBasePath(API_BASE_URL);
        ApiKeyAuth apiKey = (ApiKeyAuth) defaultClient.getAuthentication("ApiKey");
        apiKey.setApiKey(API_KEY);

        PortalsApi portalsApi = new PortalsApi();
        PortalList portalList = portalsApi.listPortals(null);
        System.out.println("Portals List - " + portalList);
    }
}
```

**Note**: 
- This OpenAPI Specification file uses **oneOf** keyword to combine schemas. As Swagger Codegen doesn't support very well some of these OpenAPI Specification keywords, it's recommended to use OpenAPI generator for Java code generation.
- In the `/portals` API, portal type is returned as capitalized. This can be fixed by capitalizing values for `properties.type.enum` and `properties.type.default` in the OpenAPI Specification file.
