# Simple Mail Transfer Protocol
SMTP is a set of communication guidelines that allow software to transmit an electronic mail over the internet is called Simple Mail Transfer Protocol. It is a program used for sending messages to other computer users based on e-mail addresses. It can send a single message to one or more recipients.

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