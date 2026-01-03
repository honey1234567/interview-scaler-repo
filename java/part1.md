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

