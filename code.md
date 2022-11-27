# TCP Socket Programming

## Simple Socket Program which connects to the server and sends a pre defined message

```java
// MyServer.java
import java.io.*;
import java.net.*;
public class MyServer {
    public static void main(String[] args){
        try{
            ServerSocket ss=new ServerSocket(6666);
            Socket s=ss.accept();//establishes connection
            DataInputStream dis=new DataInputStream(s.getInputStream());
            String  str=(String)dis.readUTF();
            System.out.println("message= "+str);
            ss.close();
        } catch(Exception e){
            System.out.println(e);
        }
    }
}
```

```java
// MyClient.java
import java.io.*;
import java.net.*;
public class MyClient {
    public static void main(String[] args) {
        try{
            Socket s=new Socket("localhost",6666);
            DataOutputStream dout=new DataOutputStream(s.getOutputStream());
            dout.writeUTF("Hello Server");
            dout.flush();
            dout.close();
            s.close();
        } catch(Exception e){
            System.out.println(e);
        }
    }
}
```

## Example of Java Socket Programming (Read-Write both side) CHAT Application
In this example, client will write first to the server then server will receive and print the text. Then server will write to the client and client will receive and print the text. The step goes on.

```java
// MyServer.java
import java.net.*;  
import java.io.*;  
class MyServer{  
    public static void main(String args[])throws Exception{  
        ServerSocket ss=new ServerSocket(3333);  
        Socket s=ss.accept();  
        DataInputStream din=new DataInputStream(s.getInputStream());  
        DataOutputStream dout=new DataOutputStream(s.getOutputStream());  
        BufferedReader br=new BufferedReader(new InputStreamReader(System.in));  
        
        String str="",str2="";  
        while(!str.equals("stop")){  
            str=din.readUTF();  
            System.out.println("client says: "+str);  
            str2=br.readLine();  
            dout.writeUTF(str2);  
            dout.flush();  
        }  
        // For Echo Application do these
        // while(!str.equals("stop")){  
        //     str=din.readUTF();  
        //     System.out.println("server got: "+str);  
        //     dout.writeUTF(str);  
        //     dout.flush();
        // }  
        din.close();  
        s.close();  
        ss.close();  
    }
}  
```

```java
// MyClient.java
import java.net.*;  
import java.io.*;  
class MyClient{  
    public static void main(String args[]) throws Exception{  
        Socket s=new Socket("localhost",3333);  
        DataInputStream din=new DataInputStream(s.getInputStream());  
        DataOutputStream dout=new DataOutputStream(s.getOutputStream());  
        BufferedReader br=new BufferedReader(new InputStreamReader(System.in));  
        
        String str="",str2="";  
        while(!str.equals("stop")){  
            str=br.readLine();  
            dout.writeUTF(str);  
            dout.flush();  
            str2=din.readUTF();  
            System.out.println("Server says: "+str2);  
        }  
        
        dout.close();  
        s.close();  
    }
}  
```

# UDP SOCKETS - Java DatagramSocket and DatagramPacket

## Simple Sender Receiver Program
```java
//DSender.java  
import java.net.*;  
public class DSender{  
    public static void main(String[] args) throws Exception {  
        DatagramSocket ds = new DatagramSocket();  
        String str = "Welcome java";  
        InetAddress ip = InetAddress.getByName("127.0.0.1");  
        
        DatagramPacket dp = new DatagramPacket(str.getBytes(), str.length(), ip, 3000);  
        ds.send(dp);  
        ds.close();  
    }  
} 
```

```java
//DReceiver.java
import java.net.*;  
public class DReceiver{  
    public static void main(String[] args) throws Exception {  
        DatagramSocket ds = new DatagramSocket(3000);  
        byte[] buf = new byte[1024];  
        DatagramPacket dp = new DatagramPacket(buf, 1024);  
        ds.receive(dp);  
        String str = new String(dp.getData(), 0, dp.getLength());  
        System.out.println(str);  
        ds.close();  
    }  
} 
```

## UDP CHAT Server and Client
```java
//UDPChatServer.java
import java.io.*;
import java.net.*;

class UDPChatServer {
    public static DatagramSocket serversocket;
    public static DatagramPacket dp;
    public static BufferedReader dis;
    public static InetAddress ia;
    public static byte buf[] = new byte[1024];
    public static int cport = 789, sport = 790;

    public static void main(String[] a) throws IOException {
        serversocket = new DatagramSocket(sport);
        dp = new DatagramPacket(buf, buf.length);
        dis = new BufferedReader(new InputStreamReader(System.in));
        ia = InetAddress.getLocalHost();
        System.out.println("Server is Running...");

        while (true) {
            serversocket.receive(dp);
            String str = new String(dp.getData(), 0, dp.getLength());
            if (str.equals("STOP")) {
                System.out.println("Terminated...");
                break;
            }
            System.out.println("Client: " + str);
            String str1 = new String(dis.readLine());
            buf = str1.getBytes();
            serversocket.send(new DatagramPacket(buf, str1.length(), ia, cport));
        }

        // For Echo server
        // while (true) {
        //     serversocket.receive(dp);
        //     String str = new String(dp.getData(), 0, dp.getLength());
        //     if (str.equals("STOP")) {
        //         System.out.println("Terminated...");
        //         break;
        //     }
        //     System.out.println("Server Got: " + str);
        //     buf = str.getBytes();
        //     serversocket.send(new DatagramPacket(buf, str.length(), ia, cport));
        // }
    }
}
```

```java
//UDPChatClient.java
import java.io.*;
import java.net.*;

class UDPChatClient {
    public static DatagramSocket clientsocket;
    public static DatagramPacket dp;
    public static BufferedReader dis;
    public static InetAddress ia;
    public static byte buf[] = new byte[1024];
    public static int cport = 789, sport = 790;

    public static void main(String[] a) throws IOException {
        clientsocket = new DatagramSocket(cport);
        dp = new DatagramPacket(buf, buf.length);
        dis = new BufferedReader(new InputStreamReader(System.in));
        ia = InetAddress.getLocalHost();
        System.out.println("Client is Running... Type 'STOP' to Quit");

        while (true) {
            String str = new String(dis.readLine());
            buf = str.getBytes();
            if (str.equals("STOP")) {
                System.out.println("Terminated...");
                clientsocket.send(new DatagramPacket(buf, str.length(), ia, sport));
                break;
            }
            clientsocket.send(new DatagramPacket(buf, str.length(), ia, sport));
            clientsocket.receive(dp);
            String str2 = new String(dp.getData(), 0, dp.getLength());
            System.out.println("Server: " + str2);
        }
    }
}
```

# HTTP GET/POST

- Install nodejs from nodejs.org
- npm i express yapople

If node.js is installed and the express library is available for use, you can start an express server with the following script:
```javascript
// app.js
const app = require('express')()
app.listen(3003)
app.get("/", (req,res) => {
    res.status(200).send("This is the mock server. You sent a GET Request")
})
app.post("/", (req, res) => {
    res.status(200).send(`This is the mock server. You sent a POST Request`)
})
```

To Run the NODEJS Server use the following command:
node server.js

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


# FTP (File Transfer Protocol)

## Simple FTP Server and Client Program
- Make sure that there is a file with the name mentioned at the specified directory 
- Use Absolute path

```java
//FTPServer.java
import java.net.*;
import java.io.*;
public class FTPServer {
    public static void main(String[] args) {
       try {
            ServerSocket ss=new ServerSocket(489);
            Socket server=ss.accept();
            FileInputStream Finput=new FileInputStream("C:/users/tharu/test.txt");
            OutputStream op=server.getOutputStream();
            int siz=1000;
            byte size[];
            size=new byte[siz];
            Finput.read(size,0,size.length); 
            op.write(size,0,size.length);
            System.out.println("Sending file ... ");
            op.flush();
            System.out.println("File Sent Successfully!...");
            server.close();
            ss.close();
            Finput.close();
            
        } catch(Exception e){
            System.out.println(e);
        }
    }
}
```

```java
//FTPClient.java
import java.net.*;
import java.io.*;

public class FTPClient {
    public static void main(String[] args) {
        try {
            Socket client = new Socket("localhost", 489);
            byte size[];
            InputStream Finput = client.getInputStream();
            FileOutputStream Foutput = new FileOutputStream("C:/users/tharu/newfile.txt");
            size = new byte[1000];
            Finput.read(size, 0, size.length);
            Foutput.write(size, 0, size.length);
            Foutput.flush();
            System.out.println("File Saved Successfully!....");
            client.close();
            Foutput.close();
        } catch (Exception e) {
            System.out.println(e);
        }
    }
}
```


# DNS

## Implementation of DNS Service

```java
// userver.java
import java.io.*;
import java.net.*;

public class userver {
    private static int indexOf(String[] array, String str) {
        str = str.trim();
        for (int i = 0; i < array.length; i++) {
            if (array[i].equals(str))
                return i;
        }
        return -1;
    }

    public static void main(String arg[]) throws IOException {
        // Initialize Arrays of Strings containing the domain name or hostname
        String[] hosts = { "zoho.com", "gmail.com", "google.com", "facebook.com" };

        // Initialize Arrays of Strings containing the IP address
        String[] ip = { "172.28.251.59", "172.217.11.5", "172.217.11.14", "31.13.71.36" };
        
        DatagramPacket recvpack = new DatagramPacket(receivedata, receivedata.length);
        serversocket.receive(recvpack);
        String sen = new String(recvpack.getData());
        InetAddress ipaddress = recvpack.getAddress();
        int port = recvpack.getPort();

        System.out.println("Press Ctrl + C to Quit");
        while (true) {
            DatagramSocket serversocket = new DatagramSocket(1362);
            byte[] senddata = new byte[1021];
            byte[] receivedata = new byte[1021];
            
            String capsent;
            System.out.println("Request for host " + sen);
            if (indexOf(hosts, sen) != -1) {
                capsent = ip[indexOf(hosts, sen)];
            } else {
                capsent = "Host Not Found";
            }
            senddata = capsent.getBytes();
            DatagramPacket pack = new DatagramPacket(senddata, senddata.length, ipaddress, port);
            serversocket.send(pack);
            serversocket.close();
        }
    }
}
```
```java
// uclient.java
import java.io.*;
import java.net.*;

public class uclient {
    public static void main(String args[]) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        DatagramSocket clientsocket = new DatagramSocket();
        InetAddress ipaddress = InetAddress.getLocalHost();
        byte[] senddata = new byte[1024];
        byte[] receivedata = new byte[1024];
        int portaddr = 1362;

        System.out.print("Enter the hostname : ");
        String sentence = br.readLine();
        senddata = sentence.getBytes();
        DatagramPacket pack = new DatagramPacket(senddata, senddata.length, ipaddress, portaddr);
        clientsocket.send(pack);

        DatagramPacket recvpack = new DatagramPacket(receivedata, receivedata.length);
        clientsocket.receive(recvpack);

        String receivedIPAddress = new String(recvpack.getData());
        System.out.println("IP Address: " + receivedIPAddress);
        clientsocket.close();
    }
}
```


# Simple Mail Transfer Protocol

## Java Implementation
We need two jar files to be able to use the Java Mail API. The first is called `mail.jar` and the second is called `activation.jar`.

```java
// SendNewMail.java
import java.util.Properties;
import javax.mail.Message;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

public class SendNewMail {
    public static void sendMail(String recepient) {
        System.out.println("Preparing to send email");

        Properties properties = new Properties();
        properties.put("mail.smtp.auth", "true");
        properties.put("mail.smtp.starttls.enable", "true");
        properties.put("mail.smtp.host", "smtp.gmail.com");
        properties.put("mail.smtp.port", "587");

        String username = "worldisfullofmeow@gmail.com";
        String password = "mypassword";
        
        Session session = Session.getInstance(properties, new javax.mail.Authenticator() {
            protected javax.mail.PasswordAuthentication getPasswordAuthentication() {
                return new javax.mail.PasswordAuthentication(username, password);
            }
        });

        Message message = prepareMessage(session, username, recepient);

        try {
            Transport.send(message);
            System.out.println("Email sent successfully" + recepient);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }

    private static Message prepareMessage(Session session, String username, String recepient) {
        Message message = new MimeMessage(session);

        try {
            message.setFrom(new InternetAddress(username));
            message.setRecipients(Message.RecipientType.TO, InternetAddress.parse(recepient));
            message.setSubject("Hello from Java");
            message.setText("Hello, this is a test email sent from Java to test SMTP Protocol");
        } catch (Exception e) {
            e.printStackTrace();
        }

        return message;        
    }

    public static void main(String[] args) {
        SendNewMail.sendMail("tharunoptimus@outlook.com");
    }

}

```

## JavaScript Implementation
We need to install the `npm` package `nodemailer`.
`npm install nodemailer` or `yarn add nodemailer`

```javascript
// app.js
// create transporter object with smtp server details
const transporter = nodemailer.createTransport({
    host: 'smtp.gmail.com',
    port: 587,
    auth: {
        user: '[USERNAME]',
        pass: '[PASSWORD]'
    }
});

// send email
await transporter.sendMail({
    from: 'from_address@example.com',
    to: 'to_address@example.com',
    subject: 'Test Email Subject',
    html: '<h1>Example HTML Message Body</h1>'
});
```

# Post Office Protocol 3 (POP3)

## Java Implementation
We need two jar files to be able to use the Java Mail API. The first is called `mail.jar` and the second is called `activation.jar`.

```java
import java.util.Properties;
import javax.mail.Folder;
import javax.mail.Message;
import javax.mail.Session;
import javax.mail.Store;

public class App {
    public static void check(String host, String storeType, String user, String password) {
        try {
            Properties prop = new Properties();

            prop.put("mail.pop3.host", host);
            prop.put("mail.pop3.port", "995");
            prop.put("mail.pop3.starttls.enable", "true");

            Session emailSession = Session.getDefaultInstance(prop);

            // create the POP3 store object and connect with the pop server
            Store store = emailSession.getStore("pop3s");

            store.connect(host, user, password);

            // create the folder object and open it
            Folder emailFolder = store.getFolder("INBOX");
            emailFolder.open(Folder.READ_ONLY);

            Message messages[] = emailFolder.getMessages();
            Message message = messages[1];

            System.out.println("messages.length---" + messages.length);
            System.out.println("The Latest Message is: ");

            System.out.println("---------------------------------");
            System.out.println("Email Number " + (1));
            System.out.println("Subject: " + message.getSubject());
            System.out.println("From: " + message.getFrom()[0]);
            System.out.println("Text: " + message.getContent().toString());

            // close the store and folder objects
            emailFolder.close(true);
            store.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {

        String host = "pop.gmail.com";
        String mailStoreType = "pop3";

        String username = "your_mail_id@gmail.com";
        String password = "your_password";

        check(host, mailStoreType, username, password);
    }
}

```


## JavaScript Implementation
Need to install the `npm` package `yapople`.
`npm install yapople` or `yarn add yapople`

```javascript
// app.js
const { Client } = require('yapople')

const client = new Client({
    host: 'outlook.office365.com',
    port : 995,
    tls: true,
    mailparser: true,
    username : 'mylifeasneha@outlook.com',
    password : 'TrustYourMom',
});

async function main(){
    await client.connect()
    let message = await client.retrieve(1)
    console.log(message)
    await client.quit()
}

main()
```

# Packet Inter Network Groper (Ping)

A ping (Packet Internet or Inter-Network Groper) is a basic Internet program that allows a user to test and verify if a particular destination IP address exists and can accept requests in computer network administration. 

## Java Implementation
```java
// Ping.java
import java.io.*;
import java.util.Scanner;

public class Ping {
    public static void main (String args[]) {
        int times = 5;
        String host = "www.google.com";

        Scanner sc = new Scanner(System.in);

        System.out.print("Enter host name: ");
        host = sc.nextLine();

        System.out.print("Enter number of times to ping: ");
        times = sc.nextInt();

        System.out.println("Pinging " + host + " " + times + " times");
        try {
            Process p = Runtime.getRuntime().exec("ping -n " + times + " " + host);
            BufferedReader stdInput = new BufferedReader(new InputStreamReader(p.getInputStream()));
            String s;
            while ((s = stdInput.readLine()) != null) {
                System.out.println(s);
            }
        } catch (Exception e) {
            e.getStackTrace();
        }
        sc.close();
    }
}
```

# TraceRoute
Traceroute is a utility that records the Internet route (gateway computers at each hop) between your computer and a specified destination computer. It also calculates and displays the amount of time for each hop. This utility helps you find where high transfer times are occurring in your internal network and the Internet. Before using Traceroute, we can use the Ping utility to identify whether a host is present on the network. 

## Java Implementation

```java
// Trace.java
import java.util.Scanner;
import java.io.*;

public class Trace {
    public static void main(String [] args) {
        String host = "google.com";
        Scanner sc = new Scanner(System.in);

        System.out.print("Enter Hostname: ");
        host = sc.nextLine();

        try {
            Process p = Runtime.getRuntime().exec("tracert " + host);
            BufferedReader buffer = new BufferedReader(new InputStreamReader(p.getInputStream()));
            String s;
            while((s = buffer.readLine()) != null) {
                System.out.println(s);
            }
        } catch (Exception e) {
            System.out.println("Error: " + e);
        }

        sc.close();
    }
}
```