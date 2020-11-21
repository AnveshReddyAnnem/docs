# Base64
In order to prevent data corruption during transmission between different storage media, Base64 is the most commonly used binary-to-text algorithm used to convert data to plain text. Moreover, it is also used in text documents such as HTML, CSS, JavaScript, or XML to embed binary data. Base64 is well known due to the algorithm's high popularity, is endorsed by different programming languages, has made many updates, and of course, has its own specification.

By Engineers in late 80's Seven-bit, six-bit, and even three-bit bytes have been used. "Many systems used old encodings by the time the eight-bit encoding was approved as a standard and did not support the new standard". This led to the fact that during the transfer between the new and the old systems, some data was simply lost. For instance, when sending emails, a mail server may discard the eighth bit. In addition, mail servers had another problem: they could only send text, but not binary data (such as images, videos, archives). So to solve these problems, engineers created algorithms. Other binary-to-text encodings were of course, developed over time, but Base64 became the most popular and was used almost everywhere thanks to simplicity, efficiency and portability.

#### Base64 Characters
Base64 is a binary to ASCII encoding scheme. It is designed as a way to transfer binary data reliably across channels that have limited support for different content types.
A base64 encoded string looks like this:
```
V2hhdCBoYXBwZW5zIHdoZW4geW91IGJhc2U2NCgpPw==
```
Base64 characters only use the same 64 characters that are present in most character sets. They are:
* Upper case alphabet characters A-Z.
* Lower case alphabet characters a-z.
* Number characters 0–9.
* And finally, characters + and /.
* The = character is used for padding.

These characters are generally implemented by most character sets and are not often used as controlled characters in Internet protocols. So when you encode content with base64, you can be fairly confident that your data is going to arrive uncorrupted.

Whereas when you move the data to their original "bits and bytes" status, the data can be messed up due to protocols that confuse special characters.

#### What is it used for?
The original use case for base64 was simply as a safe way to transmit data across machines. Overtime, base64 has been integrated into the implementation of certain core Internet technologies such as encryption and file embedding.

**Data transmission:** Base64 can easily be used as a means of transmitting and storing data without the possibility of data manipulation. It is also used to transmit a user's JSON information and cookie information.

**File embedding:** In order to avoid relying on external files, Base64 can be used to embed files within scripts and webpages. Also, email attachments are sent this way sometimes.

**Data obfuscation:** As the resulting text is not human-readable, Base64 can be used to obfuscate data. This should not, however, be seen as a security measure as the encoding is readily reversible.

**Data hashing:** Data hashing systems such as SHA and MD5 often yield outcomes that can not be read or transmitted. Hashes are also almost always encoded with base64 so that they can be displayed and then used easily for file integrity checks.

**Cryptography:** Similarly, encrypted information also contains byte sequences that are not easily transmitted or processed. Base64 is often used if encrypted data needs to be stored in a database or sent over the Internet. Public key certificates and other encryption keys are often usually stored in the base64 format in addition to cyphertext.
