# FTP (File Transfer Protocol)

-   FTP stands for File transfer protocol.
-   FTP is a standard internet protocol provided by TCP/IP used for transmitting the files from one host to another.
-   It is mainly used for transferring the web page files from their creator to the computer that acts as a server for other computers on the internet.
-   It is also used for downloading the files to computer from other servers.

## Objectives of FTP:

-   It provides the sharing of files.
-   It is used to encourage the use of remote computers.
-   It transfers the data more reliably and efficiently.

## What is the purpose?

Although transferring files from one system to another is very simple and straightforward, but sometimes it can cause problems. For example, two systems may have different file conventions. Two systems may have different ways to represent text and data. Two systems may have different directory structures. FTP protocol overcomes these problems by establishing two connections between hosts. One connection is used for data transfer, and another connection is used for the control connection.

|                                                                  Advantages of FTP                                                                   |                                                                                                                         Disadvantages of FTP                                                                                                                          |
| -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Speed: One of the biggest advantages of FTP is speed. The FTP is one of the fastest way to transfer the files from one computer to another computer. | The standard requirement of the industry is that all the FTP transmissions should be encrypted. However, not all the FTP providers are equal and not all the providers offer encryption. So, we will have to look out for the FTP providers that provides encryption. |
|                       Efficient: It is more efficient as we do not need to complete all the operations to get the entire file.                       |                                                                                         It is not efficient as we need to complete all the operations to get the entire file.                                                                                         |
|         Security: To access the FTP server, we need to login with the username and password. Therefore, we can say that FTP is more secure.          |                               Passwords and file contents are sent in clear text that allows unwanted eavesdropping. So, it is quite possible that attackers can carry out the brute force attack by trying to guess the FTP password.                                |
| Back & forth movement: FTP allows us to transfer the files back and forth. Suppose you are a manager of the company, you send some information to all the employees, and they all send information back on the same server. | It is not compatible with every system. |
| Reliability: The FTP is reliable. The FTP is used for transferring the files from one computer to another computer. |  |


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