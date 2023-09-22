# Code Generation and Usage for [Flight Deck Manager OAS specification](https://developer.signiant.com/openapi/managerOpenAPI.yaml)
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
- Execute API call, for example, http://localhost:8080/listUsers from postman.

```
exports.listUsers = function(username,password) {
    var axios = require("axios");
    var options = {
          method: "GET",
          url: "MANGAER_URL/listusers",
          headers: {
                      username: USER_NAME,
                      password: PASSWORD
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

Note: TLS certificate errors can be resolved by disabling TLS certificate verification as given below:
- export NODE_TLS_REJECT_UNAUTHORIZED=0
- npm install

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

# create an instance of the API class
configuration = swagger_client.Configuration()
username = USER_NAME
password = PASSWORD

try:
    api_instance = swagger_client.JobGroupsApi(swagger_client.ApiClient(configuration))
    api_response = api_instance.list_job_groups(username, password)
    pprint(api_response)
except ApiException as e:
    print("Exception calling API %s\n" % e)
```
Note: TLS certificate errors can be resolved by disabling TLS certificate verification in `configuration.py`:
- self.verify_ssl = False


### Go

- Install [Go](https://go.dev/doc/install)
- Create a new project and add the generated client named "go" (or name corresponding to the output directory used while generation) on the root path of the project.
- Add a new file at the root level of the project - `touch main.go`
- Import generated code in `main.go` as `sw "ProjectName/go"` or `sw "./go"`
- Run as `go run main.go`

```
package main

import (
	"context"
	sw "./go"
)

func main() {
    client := sw.NewAPIClient(sw.NewConfiguration())
    context := context.Background()
    
    username := USER_NAME
    password := PASSWORD
    
    client.JobGroupsApi.ListJobGroups(context, username, password)
}
```
Note: TLS certificate errors can be resolved by disabling TLS certificate verification in `client.go` in function `NewAPIClient` as given below:

```
import (
     "crypto/tls"
     )

transCfg := &http.Transport{ TLSClientConfig: &tls.Config{InsecureSkipVerify: true},}

if cfg.HTTPClient == nil {
    cfg.HTTPClient = &http.Client{Transport: transCfg}
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
import org.openapitools.client.ApiClient;
import org.openapitools.client.ApiException;
import org.openapitools.client.Configuration;
import org.openapitools.client.api.*;
import org.openapitools.client.model.*;

import java.util.Arrays;
import java.util.List;

public class Main {
    public static void main(String[] args) {
        ApiClient defaultClient = Configuration.getDefaultApiClient();
        defaultClient.setBasePath(MANAGER_URL);
        String username = USER_NAME;
        String password = PASSWORD;
        try {
            JobGroupsApi apiInstance = new JobGroupsApi(defaultClient);
            apiInstance.getApiClient().setVerifyingSsl(false);
            List<ListJobGroupsResponseInner> result = apiInstance.listJobGroups(username, password);
            System.out.println(result);
        } catch (ApiException e) {
            System.err.println("Error: " + e.getResponseBody());
            e.printStackTrace();
        }
    }
}
 ```

**Note**:
- Generated source for Java gives exception in some APIs if there are additional fields in the response from the server which are not defined in schema in OpenAPI Specification. Please comment the line which is the source of error as all fields are not always required to be defined in schema.