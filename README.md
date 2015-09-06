# Web Security Basics

### SSL/TLS

#### General

Transport Layer Security (TLS) and its predecessor, Secure Sockets Layer (SSL), are cryptographic protocols designed to provide communications security over a computer network.

SSL (and its successor, TLS) is a protocol that operates directly on top of TCP.  This way, protocols on higher layers (such as HTTP) can be left unchanged while still providing a secure connection. Underneath the SSL layer, HTTP is identical to HTTPS.

When using SSL/TLS correctly, all an attacker can see on the cable is which IP and port you are connected to, roughly how much data you are sending, and what encryption and compression is used. He can also terminate the connection, but both sides will know that the connection has been interrupted by a third party.

In typical use, the attacker will also be able to figure out which host name you're connecting to (but not the rest of the URL): although HTTPS itself does not expose the host name, your browser will usually need to make a DNS request first to find out what IP address to send the request to.

TLS is based on specifications developed by Netscape Communications’ SSL protocol, which is the predecessor of TLS. TLS and SSL are not interoperable, i.e. TLS cannot be implemented as SSL.

#### High-level description of the protocol

After building a TCP connection, the **SSL handshake** is started by the client. The client sends a number of specifications: which version of SSL/TLS it is running, what ciphersuites it wants to use, and what compression methods it wants to use. 
The server checks what the highest SSL/TLS version is that is supported by them both, picks a ciphersuite from one of the client's options (if it supports one), and optionally picks a compression method.

After this the basic setup is done, the **server sends its certificate**. This certificate must be trusted by either the client itself or a party that the client trusts. 
For example if the client trusts GeoTrust, then the client can trust the certificate from Google.com, because GeoTrust cryptographically signed Google's certificate.

Having verified the certificate and being certain this server really is who he claims to be (and not a man in the middle), **a key is exchanged**. This can be a public key, a "PreMasterSecret" or simply nothing, depending on the chosen ciphersuite. 
Both the server and the client can now compute the key for the symmetric encryption. The client tells the server that from now on, all communication will be encrypted, and sends an encrypted and authenticated message to the server.

The **server verifies that the MAC** (Message Authentication Code) used for authentication is correct, and that the message can be correctly decrypted. It then returns a message, which the client verifies as well.
The **handshake is now finished**, and the two hosts can communicate securely.

**To close the connection, a close_notify 'alert' is used**. If an attacker tries to terminate the connection by finishing the TCP connection (injecting a FIN packet), both sides will know the connection was improperly terminated. 
The connection cannot be compromised by this though, merely interrupted.

#### An SSL Connection

Next, we’re going to look into a very basic outline of the process of establishing an SSL connection.

* Browser requests a HTTPS webpage

* Web Server sends public key and certificate

* Browser examines the SSL Certificate

* Browser creates a symmetric key and sends it to server

* Web server decrypts symmetric key with its private key

* Web server sends browser the page with symmetric key

* Browser decrypts the data and displays page

#### How to crack SSL

There is no simple and straight-forward way; SSL is secure when done correctly. An attacker can try **if the user ignores certificate warnings** though, which would break the security instantly. When a user does this, the attacker doesn't need a private key from a CA to forge a certificate, he merely has to send a certificate of his own.

Another way would be by a flaw in the application (server- or client-side). An easy example is in websites: **if one of the resources used by the website (such as an image or a script) is loaded over HTTP**, the confidentiality cannot be guaranteed anymore. 
Even though browsers do not send the HTTP Referer header when requesting non-secure resources from a secure page (source), it is still possible for someone eavesdropping on traffic to guess where you're visiting from; for example, if they know images X, Y, and Z are used on one page, they can guess you are visiting that page when they see your browser request those three images at once. 
Additionally, when loading Javascript, the entire page can be compromised. An attacker can execute any script on the page, modifying for example to whom the bank transaction will go. When this happens (a resource being loaded over HTTP), the browser gives a mixed-content warning: Chrome, Firefox, Internet Explorer 9

Another trick for HTTP is when the login page is not secured, and it submits to an https page. "Great," the developer probably thought, "now I save server load and the password is still sent encrypted!" The problem is sslstrip, a tool that modifies the insecure login page so that it submits somewhere so that the attacker can read it.

Last but not least, you can resort to other methods to obtain the info that SSL denies you to obtain. If you can already see and tamper with the user's connection, it might not be that hard to replace one of his/her .exe downloads with a keylogger, or simply to physically attack that person. 
Cryptography may be rather secure, but humans and human error is still a weak factor.



### Reference:

[How does SSL/TLS work?](http://security.stackexchange.com/questions/20803/how-does-ssl-tls-work)

[Diffie Helman Exchange](https://www.youtube.com/watch?v=YEBfamv-_do)



