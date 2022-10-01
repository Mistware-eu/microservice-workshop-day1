# Microservice Workshop Day 1, Exercises

## Exercise 1: Backend
Using Java (Gradle) or NodeJS (Javascript or Typescript), make an HTTP server with an endpoint taking a productId and returning a JSON object. Instead of a database use a static array with objects:

| Id  | Name    | Price |
| --- | ------- | ----- |
| 0   | Coffee  | 5     |
| 1   | Monster | 20    |
| 2   | Mokai   | 20    |

## Exercise 2: Frontend
Make an HTML page that calls the backend using AJAX and displays the information.

## Exercise 3: Adding Information
Add tables (arrays) for user, location, and locationStock to the backend. Add a userId parameter to the backend. Using the userId fetch the preferredLocationId, and then fetch the stock for that location, and add stock to the returned result.

| Id  | Location |
| --- | -------- |
| 0   | Odense   |
| 1   | Aarhus   |
| 2   | Randers  |

| Id  | Username   | Preferred location id |
| --- | ---------- | --------------------- |
| 0   | coolguy69  | 0                     |
| 1   | noobslayer | 2                     |
| 2   | dakedak    | 1                     |

| Location id | Product id | Stock |
| ----------- | ---------- | ----- |
| 0           | 0          | 6     |
| 0           | 1          | 4     |
| 0           | 2          | 2     |
| 1           | 0          | 10    |
| 1           | 1          | 5     |
| 1           | 2          | 1     |
| 2           | 0          | 0     |
| 2           | 1          | 15    |
| 2           | 2          | 1000  |

## Exercise 4: Color the Components
Color each like as either "product" or "location." Split it into two classes (which call each other).

## Exercise 5: Make Adapters
Isolate product and location by:
* Typescript: put them in another file and don't export them.
* Java: put them in separate packages and make methods package private.
Then add an adapter class in each component where you expose only what is necessary to fix compiler errors.

Once you have solve this exercise: Make a branch!
```
git branch method-2
```

## Exercise 6: Add REST
Duplicate the whole backend project.
1. in one version delete the product component and change the location adapter to a HTTP server, methods become endpoints.
2. in the other version delete the location component and change the product adapter to a HTTP server, methods become endpoints.
3. in the location service change the product adapter to make HTTP calls to the other service
4. in the product service change the location adapter to make HTTP calls to the other service
Remember: the HTTP servers need to run on different ports.
Also careful with Git now that we have copied everything.

## Exercise 7: Split in Twine
Split the location service into "user" and "stock" services.

Can we do it without changing the product service?

## Exercise 8: Push Based
Travel back in time to right after we added adapters.
```
git checkout method-2
```

Make all methods return void.

## Exercise 9: Add Queue
Make an Observable interface with a notify method. Also add a Singleton Queue, having an array of observers, and a method "void push(String event, Object body)" which iterates through observers and calls their notify-method. 

Add Observable classes for both components. In the notify method check if event equals a public method name, if so call the method (Hint: hardcode them).

Make the adapter methods package private. Fix compile errors by pushing events to the queue.

## Exercise 10: Split in Twine
Split the location component into "user" and "stock" components.

Can we do it without changing the product component?

## Exercise 11: Deploy (deploy Info + User + Stock)
Install mist-cloud tools: https://mist-cloud.notion.site/Installing-mist-cli-Novice-c2c5662922094f57af8a9eac5204f919

Setup a project:
```
> mist login
> mist org [some name]
> cd [same name]
> cd Default
> mist service product
> mist service user
> mist service stock
```

Using the following template, in the "product" folder put the product component. Same for user and stock. Instead of pushing to Queue make a Rapid call. In each folder, modify the config.json file to reflect what the Observer classes said.

<details>
  <summary>Java template</summary>
  
```java
package org.example;

import java.io.IOException;
import java.net.URI;
import java.net.http.HttpClient;
import java.net.http.HttpRequest;
import java.net.http.HttpResponse;
import org.json.JSONObject;

public class Main {

    private static void pushToRapid(String event, String body){
        var client = HttpClient.newHttpClient();
        var request = HttpRequest
                .newBuilder(URI.create(System.getenv("RAPID") + "/" + event))
                .header("Content-Type", "application/json")
                .POST(HttpRequest.BodyPublishers.ofString(body))
                .build();
        var response = client.send(request, HttpResponse.BodyHandlers.ofString());
    }

    public static void main(String[] args) throws IOException, InterruptedException {
        String action = args[args.length-2];
        JSONObject envelope = new JSONObject(args[args.length-1]);

        if ("greet".equals(action)) {
          // Put your code here

          pushToRapid("reply", "{\"msg\": \"Hello from java, "+envelope.getJSONObject("payload").getString("name")+"\"}")
        }
    }
}
```
</details>
<details>
  <summary>Typescript template</summary>
  First call

```
> npm install @mistware/http-lib
```

```js
const urlReq = require("@mistware/http-lib").urlReq;

function pushToRapid(event, body) {
  urlReq(process.env.RAPID + "/" + event, "POST", body);
}

const action = process.argv[process.argv.length - 2];
const envelope = JSON.parse(process.argv[process.argv.length - 1]);

if (action === "greet") {
  // Put your code here

  pushToRapid("reply", {
    msg: `Hello from nodejs, ${envelope.payload.name}`,
  });
}
```
</details>

Run your system locally:
```
> mist run
```

## Bonus: Add Recommendation
In both pull and push based, try adding a recommendation service. Which is easier? How many services can run continuously?



