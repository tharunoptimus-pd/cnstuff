# DNS

## Summary from [Microsoft Docs](https://docs.microsoft.com/en-us/troubleshoot/windows-server/networking/dns-works-on-tcp-and-udp)
DNS and some other services work on both the protocols. We'll take an example of DNS Service. Two protocols are different from each other. TCP is a connection-oriented protocol and it requires data to be consistent at the destination and UDP is connection-less protocol and doesn't require data to be consistent or don't need a connection to be established with host for consistency of data.

UDP packets are smaller in size. UDP packets can't be greater than 512 bytes. So any application needs data to be transferred greater than 512 bytes require TCP in place. For example, DNS uses both TCP and UDP for valid reasons described below. UDP messages aren't larger than 512 Bytes and are truncated when greater than this size. DNS uses TCP for Zone transfer and UDP for name, and queries either regular (primary) or reverse. UDP can be used to exchange small information whereas TCP must be used to exchange information larger than 512 bytes. If a client doesn't get response from DNS, it must retransmit the data using TCP after 3-5 seconds of interval.

There should be consistency in DNS Zone database. To make this, DNS always transfers Zone data using TCP because TCP is reliable and make sure zone data is consistent by transferring the full zone to other DNS servers who has requested the data.

The problem occurs when Windows 2000 server and Advanced Server products uses Dynamic ports for all above 1023. In this case, your DNS server should not be internet facing that is, doing all standard queries for client machines on the network. The router (ACL) must permitted all UDP inbound traffic to access any high UDP ports for it to work.

LDAP always uses TCP - this is true and why not UDP because a secure connection is established between client and server to send the data and this can be done only using TCP not UDP. UDP is only used when finding a domain controller (Kerberos) for authentication. For example, a domain client finding a domain controller using DNS.

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