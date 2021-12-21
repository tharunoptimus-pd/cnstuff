# Post Office Protocol 3 (POP3)
POP3 is a one-way client-server protocol in which email is received and held on the email server. The "3" refers to the third version of the original POP protocol. A recipient or their email client can download mail periodically from the server using POP3.

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
const { Client } = require('yapople');
const client = new Client({
    host: 'pop.mail.ru',
    port:  995,
    tls: true,
    mailparser: true,
    username: 'username',
    password: 'password',
    options: {
            secureContext: {
            passphrase: "passphrase"
        }
    }
});
(async () => {
    await client.connect();
    const messages = await client.retrieveAll();
    messages.forEach((message) => {
        console.log(message.subject);
    });
    await client.quit();
})().catch(console.error);
```