# TCP Socket Programming

The client in socket programming must know two information:
- IP Address of Server, and
- Port number.

## Socket class

A socket is simply an endpoint for communications between the machines. The Socket class can be used to create a socket.

### Important Methods

| Method                                  | Description                                         |
| --------------------------------------- | --------------------------------------------------- |
| `public InputStream getInputStream()`   | returns the InputStream attached with this socket.  |
| `public OutputStream getOutputStream()` | returns the OutputStream attached with this socket. |
| `public synchronized void close()`      | closes the socket.                                  |

## ServerSocket class

The ServerSocket class can be used to create a server socket. This object is used to establish communication with the clients.

### Important Methods

| Method                             | Description                                                              |
| ---------------------------------- | ------------------------------------------------------------------------ |
| `public Socket accept()`           | returns the socket and establish a connection between server and client. |
| `public synchronized void close()` | closes the socket                                                        |

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