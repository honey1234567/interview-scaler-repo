## splititerator
https://javaconceptoftheday.com/differences-between-iterator-vs-spliterator-in-java-8/


## strong,weak and phantom reference
<img width="705" height="414" alt="image" src="https://github.com/user-attachments/assets/0464ac94-4b53-43a0-9cf6-67d039a69082" />
https://medium.com/@davoud.badamchi/explain-strong-weak-soft-and-phantom-references-in-java-their-roles-in-garbage-collection-and-af856ec9257f

## Final to AL
https://stackoverflow.com/questions/10750791/what-is-the-sense-of-final-arraylist
So a final array means that the array variable which is actually a reference to an object, cannot be changed to refer to anything else, but the members of the array can be modified.

## HTTPS in Spring boot
Enabling HTTPS in a Spring Boot application involves configuring the embedded server (typically Tomcat by default) to use SSL/TLS for secure communication. This ensures data encryption and integrity between the client and the server.
Key Steps to Enable HTTPS:
Obtain an SSL/TLS Certificate:
Self-Signed Certificate: For development or local testing, you can generate a self-signed certificate using Java's keytool utility. This creates a keystore file (e.g., JKS format) containing the certificate and private key.
Certificate from a Certificate Authority (CA): For production environments, it is highly recommended to obtain a certificate from a trusted CA (e.g., Let's Encrypt, DigiCert, GlobalSign).
Configure Spring Boot Application Properties:
Place the generated keystore file (e.g., keystore.jks) in your Spring Boot application's src/main/resources folder.
Modify your application.properties or application.yml file to include the SSL configuration:
Code

    server.port=8443 # Or 443 for standard HTTPS port
    server.ssl.key-store=classpath:keystore.jks
    server.ssl.key-store-password=your_keystore_password
    server.ssl.key-store-type=JKS
    server.ssl.key-alias=your_key_alias
Replace your_keystore_password and your_key_alias with the actual values used during certificate generation.
Optional: Redirect HTTP to HTTPS:
You can configure Spring Boot to automatically redirect all incoming HTTP traffic to HTTPS. This is typically done by adding a ServletWebServerFactoryCustomizer bean to your configuration, which sets up a connector for the HTTP port and redirects to the HTTPS port.
Example of keytool command for self-signed certificate:
Code

keytool -genkeypair -alias your_key_alias -keyalg RSA -keysize 2048 -storetype JKS -keystore keystore.jks -validity 3650
After these steps, when you run your Spring Boot application, it will listen on the configured HTTPS port (e.g., 8443 or 443), and communication will be secured using SSL/TLS. If using a self-signed certificate, browsers might initially display a warning, which can be resolved by manually trusting the certificate in your browser or operating system.

To understand how an SSL/TLS certificate works, you have to look at two different sides: the Server side (who puts it there) and the Client side (how the browser shows it to you).

Here is the breakdown of the process:

1. Who "uploads" the certificate?

No one "uploads" a certificate directly to your browser. Instead, the Website Administrator (the person who owns the site) installs it on their Web Server.

The Process for the Admin:

Generate a CSR: The admin generates a Certificate Signing Request (CSR) on their server. This creates a Private Key (kept secret) and a Public Key (to be shared).

Request from a CA: The admin sends the CSR to a Certificate Authority (CA) like Let’s Encrypt, DigiCert, or Sectigo.

Validation: The CA verifies that the admin actually owns the domain (e.g., they ask the admin to add a specific DNS record).

Issuance & Installation: Once verified, the CA sends a digital file (the SSL Certificate). The admin "uploads" or installs this file onto their web server software (like Nginx, Apache, or a cloud provider like AWS).

2. How does the browser get the certificate?

When you type a URL (like https://google.com) into your browser, a TLS Handshake occurs:

Your browser says: "Hello, I want a secure connection."

The server responds by sending a copy of its SSL certificate to your browser.

The browser does not just "display" it; it verifies it first. It checks if the certificate is expired and if it was signed by a CA that the browser trusts (using a pre-installed list called a "Root Store").

3. How the browser displays it to you

If the verification is successful, the browser shows the "Padlock" icon. Here is how you can view the details yourself in 2024–2026 browsers (Chrome, Edge, Brave):

Click the Icon: Look at the left side of the address bar. You will see a "tune" icon (two lines with circles) or a padlock.

Connection Security: Click "Connection is secure."

Certificate Details: Click "Certificate is valid."

The Pop-up: A window opens showing:

Issued to: The domain name (Common Name).

Issued by: Which CA signed it (e.g., "R3" for Let's Encrypt).

Validity Period: When it expires.

Details/Fingerprints: Technical data like the Public Key and the Thumbprint.

4. Summary Table
Feature	The Website Administrator	The Browser (User Side)
Action	Installs the certificate on the server.	Receives and verifies the certificate.
Location	The Web Server (Cloud/Hosting).	The Address Bar.
Visibility	Hidden in configuration files.	Visualized via the Padlock icon.
Role	Proving identity and enabling encryption.	Protecting the user from fake websites.

In short: The website owner gets the certificate from a trusted authority and puts it on their server. Your browser asks for that certificate every time you visit, checks if it's legit, and then displays the "Secure" status to you.
In the context of SSL/TLS, the "Admin" and the "Network (NTW) call" refer to two different parts of the security chain: the setup phase (human) and the execution phase (machine).

1. Who is the "Admin" above?

The "Admin" is the Website Administrator or System Administrator (SysAdmin). They are the person or team responsible for managing the web server where a website lives.

In a small business: This is often the web developer or the person who bought the domain name.

In a large company: This is usually a specialized DevOps Engineer or IT Security Administrator.

Their Job: They don't just "upload" a file; they perform a "handshake" with a Certificate Authority (like DigiCert or Let's Encrypt). They generate a secret Private Key on the server and receive a Public Certificate. They then configure the server software (like Nginx, Apache, or IIS) to use these files.

2. Does the server send the certificate using an "NTW call"?

Yes, but in networking terms, we call this the TLS Handshake.[1] It is not a standard "API call" (like a GET or POST request), but a low-level network packet exchange that happens before any website data is even sent.

Here is exactly how that "Network Call" happens:

The Trigger: You type https://google.com. Your browser makes a network connection to the server's IP address on Port 443.

Client Hello: Your browser sends a packet saying, "I want to talk securely.[2][3] Here are the versions of TLS I support."

Server Hello & Certificate Transfer: The server responds. In this specific network packet, the server sends the actual SSL Certificate file to your browser.

Note: The server doesn't "upload" it to the internet generally; it "serves" a copy of the certificate to every single person who connects to the site.

Verification: Your browser receives this "blob" of data (the certificate), looks at the digital signature, and checks its internal list of trusted authorities to see if it’s real.[3]

Why is it done this way?

If the server didn't send the certificate via a network call every time, your browser would have no way of knowing:

Who it is talking to (Identity).[4]

How to encrypt the data (it needs the Public Key inside that certificate).

Summary: The Admin puts the certificate on the server once. The Server then "calls" your browser (via the TLS Handshake) to hand over that certificate every time a new visitor arrives.

To understand Public and Private keys, think of them as a mathematical pair that works like a high-tech Lock and Key.

In SSL/TLS, this is called Asymmetric Encryption. Here is the concept and the flow.

1. The Concept: The "Mailbox" Analogy

The Public Key (The Mailbox Slot): Anyone can walk up to a mailbox and drop a letter through the slot. The slot is "Public"—it’s open to the world. However, once the letter is inside, the person who dropped it in cannot get it back out.

The Private Key (The Mailbox Key): Only the Mailman (the Server Owner) has the physical key to open the back of the box and read the letters. Even if a thief sees you put the letter in, they can't get it out without that specific Private Key.

The Golden Rule:

Data encrypted with the Public Key can only be decrypted by the Private Key.

They are mathematically linked, but you cannot "guess" the Private Key even if you have the Public Key.

2. The Flow: How it works during a Website Visit

Here is the step-by-step flow of how these keys are used when you visit a secure site (HTTPS).

Step 1: The Admin Generates the Pair

Before the website goes live, the Admin generates both keys on the server.

The Private Key stays hidden on the server (it never leaves).

The Public Key is put inside the SSL Certificate.

Step 2: The "Handover" (The Network Call)

When you go to https://website.com, the server sends your browser the SSL Certificate (which contains the Public Key). Now, your browser has the "Mailbox Slot."

Step 3: The Browser Creates a "Secret Code"

Your browser wants to send data, but Asymmetric encryption (using Public/Private keys) is mathematically "heavy" and slow for big files.

So, the browser creates a temporary, one-time-use "Session Key" (this is Symmetric encryption—it's fast).

Step 4: The "Secret" is Locked

The browser takes that Session Key and encrypts it using the Server’s Public Key.

Result: Now, that Session Key is in a "digital box" that only the server can open.

Step 5: The Browser Sends the Box

The browser sends that encrypted package over the internet to the server. If a hacker intercepts it, it’s useless to them because they don't have the Private Key to open it.

Step 6: The Server Decrypts

The server receives the package and uses its Private Key to unlock it. Now, the server has the Session Key.

Step 7: Secure Communication Begins

Now, both the browser and the server have the same Session Key. They use this to encrypt all the actual data (HTML, images, passwords) for the rest of your visit.

Summary Table: Key Differences
Feature	Public Key	Private Key
Who has it?	The whole world (sent to the browser).	Only the Web Server (the Admin).
Function	Encrypts data / Verifies signatures.	Decrypts data / Creates signatures.
Security	Doesn't matter if it's stolen.	If stolen, the website's security is dead.
Analogy	The "Padlock" sent to you.	The "Physical Key" kept by the owner.
Why do we do all this?

We use the Public/Private keys to safely exchange a Session Key without anyone in the middle being able to see it. It’s like using a massive, heavy armored truck to deliver a small house key, and then using that small house key to open the door for the rest of the day.

Once the Session Key has been safely exchanged (using the Public/Private keys we discussed earlier), the communication shifts from Asymmetric to Symmetric Encryption.

In Symmetric Encryption, the same key is used to both lock (encrypt) and unlock (decrypt) the data.

Here is how that process works in detail:

1. The Analogy: The "Shared Secret"

Think of two friends who have agreed on a secret code (the Session Key).

If the code is "Shift every letter 3 places forward," then the word "HELLO" becomes "KHOOR".

Because both friends know the code is "3," the second friend can easily shift the letters back to read "HELLO."

In SSL/TLS, the Session Key is not a simple number; it is a massive, random string of bits (like 0110101...), but the logic remains the same.

2. The Step-by-Step Data Flow

Once the handshake is over and both the Browser and the Server have the Session Key in their memory:

Step A: Browser Sends Data (Encryption)

You type your password into a login box and hit "Submit."

The browser takes that "Plaintext" data (your password).

It runs a mathematical algorithm (usually AES - Advanced Encryption Standard) using the Session Key.

The Result: The password turns into "Ciphertext" (a garbled mess of random characters).

The browser sends this ciphertext across the internet. Even if a hacker intercepts it, they can't read it because they don't have the Session Key.

Step B: Server Receives Data (Decryption)

The Server receives the garbled Ciphertext.

It uses its copy of the exact same Session Key.

It runs the AES algorithm in reverse.

The Result: The garbled mess turns back into your "Plaintext" password so the server can verify it.

The same thing happens in reverse when the server sends data (like your bank balance) back to your browser.

3. Why use a Session Key instead of just staying with Public/Private Keys?

You might wonder: "If we have the Public and Private keys, why do we bother making a Session Key?"

There are two main reasons:

Speed (Performance): Asymmetric encryption (Public/Private) involves incredibly complex math. It is 100 to 1,000 times slower than Symmetric encryption. If we used Public/Private keys for every image and text on a website, the site would take minutes to load.

Security (Forward Secrecy): The Session Key is temporary. It only lives in the computer's RAM for as long as you have that tab open.

If you close the browser and come back 5 minutes later, a new session key is created.

If a hacker somehow steals a Session Key today, they can't use it to read your traffic tomorrow.

4. Summary of the whole "Key" Hierarchy
Key Type	Role	Lifespan	Analogy
Public Key	To safely deliver the Session Key.	Long-term (Years)	The Mailbox Slot.
Private Key	To unlock the Session Key when it arrives.	Long-term (Years)	The Mailman's Key.
Session Key	To encrypt/decrypt the actual data (HTML, images).	Short-term (Minutes/Hours)	A "Secret Handshake" between two people.
The Final Result

When you see the Padlock icon in your browser, it means:

The Public/Private keys verified the website's identity.

The Session Key is currently working in the background, encrypting every click and keystroke you make using high-speed symmetric encryption.

## idempotaent
HTTP methods like GET, HEAD, PUT, DELETE, OPTIONS, and TRACE are idempotent, while POST and PATCH are generally non-idempotent. Understanding and leveraging the idempotent nature of HTTP methods helps create more consistent, reliable, and predictable web applications and APIs

## java heap size
https://medium.com/@maheshwar.ramkrushna/understanding-heap-size-and-its-impact-on-java-application-performance-d4c312bbd13c

## default and static method
In Java, both default and static methods can exist within interfaces, a feature introduced in Java 8. They serve distinct purposes:
1. Default Methods:
Purpose:
Default methods allow interfaces to include method implementations directly. This was primarily introduced to enable backward compatibility for existing interfaces, allowing new methods to be added without forcing all implementing classes to immediately provide an implementation.
Keyword:
They are defined using the default keyword.
Accessibility:
Default methods are accessible through objects of the classes that implement the interface. Implementing classes can choose to override the default implementation or use the one provided by the interface.
Usage:
Useful for providing a common, optional implementation for a method that implementing classes can either use or specialize.
Example:
Java

interface MyInterface {
    void abstractMethod(); 

    default void defaultMethod() {
        System.out.println("This is a default method implementation.");
    }
}

class MyClass implements MyInterface {
    @Override
    public void abstractMethod() {
        System.out.println("Abstract method implemented.");
    }
}

public class Main {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.abstractMethod(); 
        obj.defaultMethod(); // Calls the default implementation
    }
}
2. Static Methods in Interfaces:
Purpose:
Static methods in interfaces are associated with the interface itself, not with any specific instance of a class implementing that interface. They are primarily used for utility or helper methods that are related to the interface but do not require an object's state.
Keyword:
They are defined using the static keyword.
Accessibility:
Static methods in interfaces are called directly on the interface name. They cannot be overridden by implementing classes. 
Usage:
Ideal for providing utility functions or factory methods that are logically grouped with the interface but don't depend on instance data.
Example:
Java

interface MyInterface {
    void abstractMethod();

    static void staticMethod() {
        System.out.println("This is a static method in the interface.");
    }
}

class MyClass implements MyInterface {
    @Override
    public void abstractMethod() {
        System.out.println("Abstract method implemented.");
    }
}

## Lazy string
content is not retreived or encoded immediately
In Java, the concept of a "lazy string" typically refers to deferring the creation or computation of a String object until it is actually needed. This is a form of lazy evaluation and can be beneficial for performance optimization, especially when dealing with expensive string operations or when a string might not always be used.
Here's how lazy strings can be implemented or observed in Java: Lazy Initialization with Supplier.
You can use a java.util.function.Supplier<String> to encapsulate the logic for creating the string. The get() method of the Supplier will only be called when the string is actually requested. This is a common pattern for lazy loading any object, including strings.
Java

    import java.util.function.Supplier;

    public class LazyStringExample {
        private Supplier<String> lazyStringSupplier;

        public LazyStringExample() {
            // The string "expensive calculation" is not created yet
            this.lazyStringSupplier = () -> {
                System.out.println("Performing expensive string calculation...");
                return "Result of expensive calculation";
            };
        }

        public String getMyString() {
            // The string is created only when get() is called
            return lazyStringSupplier.get();
        }

        public static void main(String[] args) {
            LazyStringExample example = new LazyStringExample();
            System.out.println("Object created, but string not yet calculated.");
            String myString = example.getMyString(); // Calculation happens here
            System.out.println("Retrieved string: " + myString);
        }
    }
    
Protocol Buffers' LazyStringList.
Google's Protocol Buffers library for Java provides an interface called com.google.protobuf.LazyStringList. This interface extends List<String> and allows for lazy conversion of byte arrays (received over the wire) to String objects, improving efficiency by only converting when necessary. Lazy Evaluation in Streams.

Java Streams are inherently lazy. Operations like map, filter, and flatMap are intermediate operations and do not execute immediately. The actual processing of the stream elements, including the creation of new String objects, only occurs when a terminal operation (like collect, forEach, or count) is invoked. Conditional String Creation.



The primary benefit of using lazy strings is to avoid unnecessary computations or memory allocations when a string's value might not be required in all execution paths.

