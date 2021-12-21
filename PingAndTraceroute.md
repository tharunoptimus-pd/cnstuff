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