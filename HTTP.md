# HTTP GET/POST

## GET
The HTTP GET method requests a representation of the specified resource. Requests using GET should only be used to request data (they shouldn't include data).

## POST
The HTTP POST method sends data to the server. The type of the body of the request is indicated by the Content-Type header.
A POST request is typically sent via an HTML form and results in a change on the server. In this case, the content type is selected by putting the adequate string in the enctype attribute of the `<form>` element or the formenctype attribute of the `<input>` or `<button>` elements

## Starting a server
The Server can be started with JavaScript and PHP

If node.js is installed and the express library is available for use, you can start an express server with the following script:
```javascript
// app.js
const express = require('express')
const app = express()
const port = process.env.PORT || 3003
app.use(express.urlencoded({ extended: true }))
app.use(express.json())

const server = app.listen(port, () => {
    console.log(`Server Listening on port ${port}`)
})

app.get("/", (req,res) => {
    res.status(200).send("This is the mock server. You sent a GET Request")
})

app.post("/", (req, res) => {
    res.status(200).send(`This is the mock server. You sent a POST Request`)
})
```

If XAMPP is installed, you can start the server with the following PHP script:
```php
// post.php
<?php
    if($_SERVER['REQUEST_METHOD'] == "POST") {
        header("HTTP/1.1 200 OK");
        echo "This is a post response";
    }
?>
```

```php
// get.php
<?php
    if($_SERVER['REQUEST_METHOD'] == "GET") {
        header("HTTP/1.1 200 OK");
        echo "This is a get response";
    }
?>
```



If the server is running we can start implementing the Java Program

## Implement GET / POST
There is no real difference in the implementation of this program. The only difference is the string you pass it to `con.setRequestMethod("GET");` or `con.setRequestMethod("POST");`

Basically, this is how this stuff works:
- Implement a chat server
- Client sends the URL to the Server
- Server accesses the URL with the specified HTTP method
- Server prints the response in the console

- Change the HTTP Method `private static String method = "POST";` to send a POST request
- Change the HTTP Method `private static String method = "GET";` to send a GET request

```java
// GetServer.java
import java.io.*;
import java.net.*;

class GetServer {
    private static final String USER_AGENT = "Mozilla/5.0 (platform; rv:geckoversion) Gecko/geckotrail Firefox/firefoxversion";
    private static ServerSocket ss;
    private static String method = "GET"; // Change method to POST

    static String sendPOST(String POST_URL) throws IOException {
        URL obj = new URL(POST_URL);
        HttpURLConnection con = (HttpURLConnection) obj.openConnection();
        con.setRequestMethod(method);
        con.setRequestProperty("User-Agent", USER_AGENT);
        int responseCode = con.getResponseCode();
        System.out.println(method + " Response Code :: " + responseCode);
        if (responseCode == HttpURLConnection.HTTP_OK) {
            BufferedReader in = new BufferedReader(new InputStreamReader(con.getInputStream()));
            String inputLine;
            StringBuffer response = new StringBuffer();
            while ((inputLine = in.readLine()) != null) {
                response.append(inputLine);
            }
            in.close();
            System.out.println(response.toString());
            return (response.toString());
        } else {
            System.out.println(method + " request not worked");
            return (null);
        }
    }

    public static void main(String a[]) throws Exception {
        ss = new ServerSocket(6789);
        try {
            while (true) {
                Socket consoc = ss.accept();
                BufferedReader ifc = new BufferedReader(new InputStreamReader(consoc.getInputStream()));
                DataOutputStream otc = new DataOutputStream(consoc.getOutputStream());
                String ps = ifc.readLine() + '\n';
                System.out.println("RECEIVED : " + ps);
                String POST_URL = ps;
                otc.writeBytes(sendPOST(POST_URL) + '\n');
                System.out.println(method + " DONE");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}

```

```java
// GetClient.java
import java.io.*;
import java.net.*;
class GetClient
{
    public static void main(String a[]) throws Exception {
        try {
            BufferedReader ifu = new BufferedReader(new InputStreamReader(System.in));
            Socket clientSocket = new Socket("localhost", 6789);
            DataOutputStream ots = new DataOutputStream(clientSocket.getOutputStream());
            BufferedReader ifs = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));

            System.out.println("\nGET url : ");
            String sentence = ifu.readLine();
            ots.writeBytes(sentence + '\n');
            String ms = ifs.readLine();

            clientSocket.close();
            ms.trim();
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```