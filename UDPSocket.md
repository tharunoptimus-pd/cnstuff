# Java DatagramSocket and DatagramPacket

Java DatagramSocket and DatagramPacket classes are used for connection-less socket programming using the UDP instead of TCP.

## Datagram

Datagrams are collection of information sent from one device to another device via the established network. When the datagram is sent to the targeted device, there is no assurance that it will reach to the target device safely and completely. It may get damaged or lost in between. Likewise, the receiving device also never know if the datagram received is damaged or not. The UDP protocol is used to implement the datagrams in Java.

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