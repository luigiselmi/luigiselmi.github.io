---
layout: post
title:  "Public-key cryptography and digital signature using OpenSSL"
date:   2018-12-27
categories: security, cryptography
---
The purpose of this post is to explain how to communicate privately over the Internet using public-key cryptography and how to digitally sign a document.

## Introduction
Being able to communicate privately is a civil right and often a business need. As we can not allow anyone to eavesdrop our communications, we have also the right to avoid surveillance by companies or governments. There are many tools and protocols, many being open source and free, that can be used to enhance the security of our communications over the Internet. The aim of this post is to provide a very high level description of the ideas behind these tools and protocols and practical guidance on how to use one of them, [OpenSSL](https://www.openssl.org/), which is open source, free and used to secure most of the communications over the Internet. In particular in this post we will show

1. How to avoid being eavesdropped while sending files to our friends or collaborators over the internet
2. How to digitally sign a document

It is supposed that you are using a Linux distribution or a Mac with OpenSSL version 1.0.2 installed. In case you use Windows you might want to install [Cygwin](https://www.cygwin.com/) with openssl. It is assumed that you know how to use the command line.
## Alice and Bob
We will set up a context for the secure communication problem using two characters, Alice and Bob. We will simulate the transmission of encrypted messages between Alice and Bob by copying files from Alice's folder to Bob's and vice-versa on our local file system. This simulation is meant for you to easily check what happens on both sides when they send or receive messages using OpenSSL, but it must be kept in mind that it bypasses the core business of encryption that is about sending messages over an insecure channel such as the Internet where other parties could eavesdrop or interfere with Alice's and Bob's communication. With this warning in mind, let's start our simulation by creating a folder for Alice's messages and one for Bob's
{% highlight text %}
$ mkdir alice

$ mkdir bob
{% endhighlight %}
Let's imagine that Bob can't remember his bank account details and asks Alice to send them to him by email. Alice is aware that sending the data as plain text over the Internet is risky so she wonders how to send the data to Bob in such a way that nobody else but he can read and use the data. After some investigation, Alice decides that the solution to their problem is public-key cryptography and the OpenSSL tools.

## Public-key cryptography
Public-key cryptography consists of creating a key pair, namely a private key and a public key, to encrypt and decrypt messages. The private key is kept secret and is never shared with anyone. Alice uses Bob's public key to encrypt the messages being sent to him. Bob uses his private key to decrypt the messages encrypted with his public key. The public key can even be published on the Internet for that matter. Only the owner of the private key can decrypt a message encrypted with his or her public key. There are different ways of creating a key pair but all are based on defining mathematical problems that are very difficult to solve in a short time scale, such as factorizing a number that is the product of two big prime numbers. This class of problems is used in the Rivest-Shamir-Adleman (RSA) cryptosystem. The idea is to find two prime numbers big enough, e.g. with more than 150 digits, so that it would be very difficult even for a cluster of computers to find them out in decades while it is very easy to compute their product. In RSA, the public key is the product of two prime numbers and the private key is the set of the two prime numbers themselves. An eavesdropper who wants to decrypt a message would need to extract the private key, i.e. the two prime numbers, from their product. In other words, the eavesdropper must be able to factorize a number that is the product of two big prime numbers, which in itself is an hard enough problem. Using RSA we can be confident that nobody will be able to decrypt our messages. The algorithm used for the encryption is well known and publicly available. The only thing that is not public, and known only to the owner of the key pair, is the private key. Let's see what Alice and Bob have to do to keep their communication private:

1. Alice and Bob create their own private and public keys.
2. Bob sends Alice his public key.
3. Alice encrypts her message using Bob’s public key and sends it to Bob.
4. Bob decrypts Alice’s message using his private key.

So, first of all, both Alice and Bob need a key pair.

### 1. Alice and Bob create their own private and public keys.
Alice doesn’t yet have a key pair, so she needs to create it. As an example she may use the RSA cryptosystem. Her private key will be stored in a file, e.g. alice_rsa. The size of the private key will be 2048 bit. Let's move into Alice's folder and execute the command
{% highlight bash %}
$ openssl genpkey -algorithm RSA -out alice_rsa -pkeyopt rsa_keygen_bits:2048
{% endhighlight %}
The private key in alice_rsa is saved in the Privacy-Enhanced Mail (PEM) format and looks like the following
{% highlight text %}
$ cat alice_rsa
-----BEGIN PRIVATE KEY-----
MIIEvgIBADANBgkqhkiG9w0BAQEFAASCBKgwggSkAgEAAoIBAQC68nDsjtWepLcM
pF4zVaMdFsVdg692M5Mj9v/vGvgyyPHpHmH/QKolOB9KtlUZcth6d7fwmFgyaa/m
XN1HjORKrpzm0rysPpFXJymUmIGy9XLzvPP4phJS3oGsnjsQJ6O017uWt8kqgz5U
U+hYc1/AOUCA9vEP1AN+6fT6O11zZQ75aeJOK6aESV/++7ZaM4M8maVrCKFhonBU
L9ByHNDkgQMWIu2iGazb7FZ5xDHWq+wBpfJZe8/3LNjS7VpUNeqoCsBuUE4fWHYo
TSPK3CmhpWHtk9EnMxyho/rpt6/ETYPOQ3QV6Uxz4G9tDLpJzgL2Q4VHVKwqJSnT
BLrvVOnxAgMBAAECggEBAK0VbDHIqMVh0Ux2HfU/U27KN182Xcx9Qbzpodm5yZQT
cc4Y4DhYoW8mP+qHV9DhAMaacwXhtr6uFTqePg1Rx8fRVNlswVxj7WKYkqnObT7I
e25pQiSzdYGeGsc8FIkHek0j870+WZTvwFSI/zRtVXh+SVddyqCR9c6aQ8MuFX6Q
u9LzGNcYTg6Dmv8qsrXlctkJRvLOuKajaAG3AHT6f5GTXRDDhk9/Ab4h9Dorkgen
Z0fg9yLfvlltO6z3z99VHIMlWX4TZ67kGP7L+AqPpzN9Qj9G15h2Blb6InlB5J/q
pgKUVGGVC0TTILLjXLUT3xEvrWvpqHIAj80Xejuz0XUCgYEA5CAIZdKyqTrfNKTg
B7ZxIX23R/YoqU6SGYDJ/8mz8Z+0PdlCrFb/fTvb2e8aQWSfYTccwhpPH9ZGQ2zL
hXImNxPeVPymVYojZ8XQwwsd2KoK1jkuLr7uzcs393P5PB0YoCzB4kvsnlozcyrm
+lTz913eHd6BsXoRx8GeRPs3nx8CgYEA0cpRr8+EBYgWCgJ71cpB8BLZ+kZErz5W
p9GO5CD/wqaJ+Ljzrvr+XmbCFzDaf/KPTcYeFD7bz9aYq3SSavvnNSQQsHhjuphb
CE40eR/fLwubyYhjOdHXjdYrxsI2gF7FyOO25PWx2OLoCqZITBYlaOdgxefQNtlM
1boATrYZJO8CgYBCuX/bUIqDZz3cJxGED//9HMlcGgsAooOnQ/1RfMzOMrlEkeSn
hfbKyZRfpUkXsXfQto8J0yorlMAOfqb0zFOTLpOMZi28vV/nvXt3YSwEsI/k4uq4
L46n0PX4wgo3ZAdM6mp3Z1+5XYbI+9Z9iBWn1+Pc9rUWlS7YL7C8WoKFXwKBgCmI
w7lp/TpXIf3jVf8SpxFPuiYpqUmErwVUoNSbj+dKr4A1pdEb0iaAc6bBvlCchjCg
q63YcA5q7xjq4F4b9z93H3LAswXrSgKP8SWV4Mrgonw462Q0HlfvcgVMyBuMJ95I
7xnPZuGIsuYA28lsjQWC4Y7tATUKuoKJ66ups7qzAoGBAJyKVY2ZqpkEHlzMixnk
BBKZA9sccokOYWtVtnCxWZYnnG7ElOBvojuLtf+/stvIadnCVe7km6f6J50QcqtH
1g6eTMfEoqkXG5plBlcEbjEv+wAGO9RXCiyYNquUuwjMrgv8dqUpHGXdw6XxxGi6
LTf0HIwHOpMNVVyptpRZoCH/
-----END PRIVATE KEY-----
{% endhighlight %}

The public key can be created from the private one, and saved in e.g. alice_rsa.pub, with the command
{% highlight bash %}
$ openssl rsa -in alice_rsa -pubout -out alice_rsa.pub
{% endhighlight %}
Alice's public key will look like
{% highlight text %}
$ cat alice_rsa.pub
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAuvJw7I7VnqS3DKReM1Wj
HRbFXYOvdjOTI/b/7xr4Msjx6R5h/0CqJTgfSrZVGXLYene38JhYMmmv5lzdR4zk
Sq6c5tK8rD6RVycplJiBsvVy87zz+KYSUt6BrJ47ECejtNe7lrfJKoM+VFPoWHNf
wDlAgPbxD9QDfun0+jtdc2UO+WniTiumhElf/vu2WjODPJmlawihYaJwVC/QchzQ
5IEDFiLtohms2+xWecQx1qvsAaXyWXvP9yzY0u1aVDXqqArAblBOH1h2KE0jytwp
oaVh7ZPRJzMcoaP66bevxE2DzkN0FelMc+BvbQy6Sc4C9kOFR1SsKiUp0wS671Tp
8QIDAQAB
-----END PUBLIC KEY-----
{% endhighlight %}
Now we have Alice's key pair in her folder. Let's do the same for Bob. We move into Bob's folder and create his key pair, stored in e.g. bob_rsa and bob_rsa.pub, as we did for Alice. After Alice and Bob have their key pair we are done with the 1st step of the procedure.

### 2. Bob sends Alice his public key.
Let's move to the 2nd step: Bob must send his public key to Alice so she will be able to send him her message encrypted. We simulate this by copying Bob's public key file, bob_rsa.pub, in Alice's folder. From Bob's folder
{% highlight bash %}
$ cp bob_rsa.pub ../alice/
{% endhighlight %}
As soon as a copy of Bob's public key is in Alice's folder, the 2nd step of the procedure is complete and we can move to the 3rd: Alice will encrypt her message using Bob's public key and will send it to Bob.

### 3. Alice encrypts her message using Bob’s public key and sends it to Bob.
Bob's public key can now be used by Alice with OpenSSL to encrypt her message stored in a file, e.g. data.txt, containing sensitive information
{% highlight bash %}
Bob Bank Account
userid: 123456
password: 276f8%2=0as}
pin: 4657
{% endhighlight %}
In our example the size of the file is only 65 bytes. Alice encrypts the file using OpenSSL and Bob’s public key that she has received from him, e.g. by email, which we have simulated by simply copying the file from Bob's folder to Alice's. From Alice's folder
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub -in data.txt -out data.txt.enc
{% endhighlight %}
Now Alice can send her encrypted message, data.txt.enc. The encrypted message is a binary file whose content doesn't make any sense and can be decrypted only by Bob using his private key. The RSA encryption algorithm is randomized, and executing again the same command will result in a different ciphertext but when they are decrypted the output will be exactly the same message. If Alice were a real person she would be able to send it to Bob by email. We will once again simulate the sending of the encrypted message by copying it in Bob's folder. From Alice's folder
{% highlight bash %}
$ cp data.txt.enc ../bob/
{% endhighlight %}
As soon as the encrypted message has been received by Bob, in our simulation when it has been copied in Bob's folder, the 3rd step is complete. We can move to the 4th and last step.

### 4. Bob decrypts Alice’s message using his private key.
From Bob's folder
{% highlight bash %}
$ openssl rsautl -decrypt -inkey bob_rsa -in data.txt.enc -out data.txt
{% endhighlight %}
Bob can open the file data.txt containing the original message in plain text that Alice wanted to send to him. We can easily verify that Bob's decrypted message and Alice's original message are exactly the same. From the root folder
{% highlight bash %}
$ diff -s alice/data.txt bob/data.txt
Files alice/data.txt and bob/data.txt are identical
{% endhighlight %}
The procedure that Alice chose to send her message to Bob, without risking anyone else reading it, is complete. In this example Alice did not use her private or public key. In case Bob wanted to send her feedback, he could use Alice's public key to encrypt his message, so that only she would be able to decrypt it, using her private key. Both Alice and Bob must keep their private keys in a very safe place. The private key we have just created for them can be used by anyone who has access to it. One way to protect the private key is to encrypt it using an algorithm, e.g. AES-256, with a password so that only the person who knows the password can decrypt the private key and use it. For example, Alice could have made her private key safer by creating it with the following command
{% highlight bash %}
$ openssl genpkey -algorithm RSA -out alice_rsa -pkeyopt rsa_keygen_bits:2048 -aes-256-cbc -pass pass:wT16pB9y
{% endhighlight %}
where wT16pB9y would be Alice's password. Currently OpenSSL supports only alphanumeric characters for passwords.
## Hybrid cryptosystem
Alice has successfully solved Bob's problem. She has been able to send him his bank account details in a secure way. Now she wants to send Bob a file, e.g. a jpeg picture that she doesn't want anyone else to see, and whose size is some KB. Let's try to encrypt the image on behalf of Alice
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub -in alice.jpg -out alice.jpg.enc
{% endhighlight %}
This time OpenSSL will raise an error
{% highlight text %}
RSA operation error
4294956672:error:0406D06E:,rsa routines:RSA_padding_add_PKCS1_type_2:data too large for key size:rsa_pk1.c:174:
{% endhighlight %}
The problem is that the RSA algorithm can be used only to encrypt messages whose size is smaller than the size of the private key that corresponds to the public key used for the encryption. Since Bob's private key is 2048 bit long, or 256 bytes, his public key cannot be used to encrypt messages that are bigger than 256 bytes. The best option to solve this issue is to use a symmetric algorithm. A symmetric algorithm can use only one key, called a symmetric key, for encryption and decryption. Once a message has been encrypted with the symmetric key, it can be sent, with the symmetric key encrypted using the public key of the recipient, so he or she will be able to decrypt the message. One more reason to use a symmetric algorithm to encrypt a message is that they are three orders of magnitude faster than asymmetric ones. The algorithms used in the symmetric key encryption are different from those used in public-key encryption. The symmetric key algorithms use a key that is based on a pseudo-random value taken from a huge range of possible values. The key is shared only by the two communicating parties. The strength of the algorithm rests in the difficulty of finding the key within a huge key space. The way in which the symmetric key must be created depends on the cryptographic algorithm, also called cipher. One of the most robust ciphers is AES-256, that we have already used to encrypt Alice's private key. OpenSSL creates the symmetric key, to be used with the AES-256 cipher, from a secret string, in short secret, that can be created and stored in a file. Alice defines a new protocol in which she will create the secret that she will use to encrypt her picture and that she will share with Bob. The system that she is going to use is called a hybrid cryptosystem because it uses public-key and symmetric cryptography together.

1. Alice creates the secret.
2. Alice encrypts the data using the AES-256 cipher and the secret.
3. Alice encrypts the secret using Bob’s public key.
4. Alice sends the encrypted data and the encrypted secret to Bob.
5. Bob decrypts the secret using his private key.
6. Bob decrypts the data using the AES-256 cipher and the secret.

Let's implement these steps on behalf of Alice and Bob using OpenSSL.

### 1. Alice creates the secret.
First, Alice creates a secret, e.g. a sequence of 32 random bytes, using a pseudo-random bytes generator provided by OpenSSL. The longer is the sequence of random bytes the more difficult is for an eavesdropper to figure it out. It should never be so short that it could be found simply by brute force. For example a sequence of only 2 bytes (16 bits) can be found in just 2^16 = 65536 attempts. From Alice's folder
{% highlight bash %}
$ openssl rand 32 -out secret
{% endhighlight %}

### 2. Alice encrypts the data using the AES-256 cipher and the secret.
The command will encrypt the image
{% highlight bash %}
$ openssl enc -e -aes-256-cbc -in alice.jpg -out alice.jpg.enc -pass file:secret -p
{% endhighlight %}
and will print the key created by OpenSSL from the secret
{% highlight bash %}
salt=469950DBF6FA435A
key=E94C0C70A8BC662DB270C57B642C010910B65118C97DD37088E84F6DC3627225
iv =B83AB9A6A80D67DFA6B3572EB850EE0D
{% endhighlight %}
The AES-256 cipher is a block cipher that encrypts a fixed block of 128 bits of the message at a time with a 256 bits long key. The mode of operation used in the example is Cipher Block Chaining (CBC). The CBC operation mode is a scheme that allows the use of a block cipher to encode strings longer than the block size (16 bytes). The key, created by OpenSSL from the secret, is shown as a result of the encryption with other random parameters, salt and iv. The salt parameter is created by OpenSSL to be added as a suffix to the secret to mitigate directory attacks, when the secret has not been chosen wisely and it could easily be found simply by brute force attack. The iv parameter is the initialization vector used as the content of the first block. It ensures that no information can be extracted by an attacker from messages that may start with some common header. All the parameters, key, salt and iv, are recreated newly every time the command is executed, even if the file and the secret are the same.    
### 3. Alice encrypts the secret using Bob’s public key.
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub -in secret -out secret.enc
{% endhighlight %}

### 4. Alice sends the encrypted data and the encrypted secret to Bob.
We can simulate the sending of the encrypted data and secret by copying them from Alice's folder to Bob's.
{% highlight bash %}
$ cp alice.jpg.enc secret.enc ../bob
{% endhighlight %}

### 5. Bob decrypts the secret using his private key.
From Bob's folder
{% highlight bash %}
$ openssl rsautl -decrypt -inkey bob_rsa -in secret.enc -out secret
{% endhighlight %}

### 6. Bob decrypts the data using the AES-256 cipher and the secret.
{% highlight bash %}
$ openssl enc -d -aes-256-cbc -in alice.jpg.enc -out alice.jpg -pass file:secret -p
salt=469950DBF6FA435A
key=E94C0C70A8BC662DB270C57B642C010910B65118C97DD37088E84F6DC3627225
iv =B83AB9A6A80D67DFA6B3572EB850EE0D
{% endhighlight %}
You can verify that the image in Bob's folder is exactly the same as the image in Alice's folder by looking at them or by using the following command from the root folder
{% highlight bash %}
$ diff -s alice/alice.jpg bob/alice.jpg
Files alice/alice.jpg and bob/alice.jpg are identical
{% endhighlight %}
It can also be verified that the key, created by OpenSSL from the secret for the decryption, is the same as the key created for the encryption. In case a non valid secret is used, the decryption will fail.  
This 2nd protocol enables Alice and Bob to send each other files of any size allowed by the channel, encrypted. Unfortunately it is subject to the man-in-the-middle attack. This is because a message sent over the Internet goes through different routers where a 3rd party, called Mallory in cryptography, can impersonate both Alice and Bob by sending them his public key instead of Bob's and Alice's respectively. Alice and Bob can solve this issue by publishing their public keys on a trusted website or by using certificates where their public keys are signed by a trusted 3rd party. The creation of certificates, even if possible with OpenSSL, requires the definition of a certificate authority and is beyond the scope of this post.
## Message integrity
Encrypting a message is almost never enough. What we usually need is also to be sure that nobody can tamper our communications by intercepting our messages, dropping some of them or modifying them even when they are encrypted, without being detected. We need message integrity. The way in which the integrity of a message can be provided is by computing a value using a hash function that takes a message as input and outputs a short string, the digest or fingerprint. If the input message is modified, a hash function will output a complete different value in an unpredictable way. Even if the message space can be much larger than the digest space, the chances of a collision, in which a hash function outputs the same digest from two different messages, are practically negligible. Hash functions are used to support integrity in protocols such as TLS, SSH, IPsec. OpenSSL provides many hash functions such as SHA256, a standard function that hashes long messages into 256-bit digests. In some common use cases, encryption is not needed at all while integrity can be a strong security requirement. As an example, the integrity of a software package downloaded from the Internet can be checked comparing its fingerprint, provided on the website, with the fingerprint computed locally after the package has been downloaded and before it is executed. If the two fingerprints are the same we are assured that the software package has not been modified by an attacker during the transfer. Hash functions are also used to compute the fingerprint of public keys. For example, Bob can provide his public key's fingerprint to Alice so that it will be easier for her to verify whether her copy of Bob's public key is the right one. From Bob's folder
{% highlight bash %}
$ openssl dgst -sha256 -hex -c bob_rsa.pub > bob.fingerprint
{% endhighlight %}
The fingerprint can be verified more easily than the full public key
{% highlight bash %}
$ cat bob.fingerprint
SHA256(bob_rsa.pub)= 7f:98:0e:4f:a7:e4:5d:5f:bb:fb:f5:80:3a:32:b8:7e:2a:23:22:44:c4:da:8c:4d:eb:95:fa:f8:9c:5f:d9:24   
{% endhighlight %}
In the following section we will address another important use case in which a hash function is used to digitally sign a file.
## Digital signature
Alice is a journalist and wants to send Bob an article, e.g. a pdf file, being sure than no one else can claim to be the author. Once again she comes up with a protocol that can solve her problem.

1. Alice creates a one-way hash of a document, Alice's digest.
2. Alice encrypts the digest with her private key, thereby signing the document.
3. Alice sends the document, her public key and the signed digest to Bob.
4. Bob decrypts Alice's digest with her public key.
5. Bob creates a one-way hash of the document that Alice has sent, Bob's digest.
6. Bob compares his digest with Alice's to find out if they match

If the signed hash matches the hash he generated, the signature is valid. Let’s say Alice wants to send a file, e.g. article.pdf, with her digital signature to Bob.
### 1. Alice creates a one-way hash of a document, Alice's digest.
Alice can sign the message choosing one hash function, e.g. SHA-256 . She can create the one-way hash of the message, also known as the digest,  with
{% highlight bash %}
$ openssl dgst -sha256 article.pdf > alice.dgst
{% endhighlight %}
The content of the digest will be similar to
{% highlight text %}
SHA256(article.pdf)= cb686d3838cba15e5e603b8fa5191759a46227230884e20325abd19fb997f064
{% endhighlight %}
### 2. Alice encrypts the digest with her private key, thereby signing the document.
The next step is to encrypt the digest of the hash function, data.dgst, with her private key
{% highlight bash %}
$ openssl rsautl -sign -inkey alice_rsa -keyform PEM -in alice.dgst > alice.sign
{% endhighlight %}

### 3. Alice sends the document and the signed digest to Bob.
Alice sends the document, article.pdf, with her signature, alice.sign and her public key, to Bob. Bob can verify Alice’s signature of the document using her public key. Again we will simulate the sending of the files by copying them from Alice's folder to Bob's.
{% highlight bash %}
$ cp article.pdf alice.sign alice_rsa.pub ../bob
{% endhighlight %}

### 4. Bob decrypts Alice's digest with her public key.
From Bob's folder
{% highlight bash %}
$ openssl rsautl -verify -inkey alice_rsa.pub -pubin -keyform PEM -in alice.sign -out alice.dgst
{% endhighlight %}
The output, alice.dgst, is Alice's digest of the document, extracted from her signature of the document.

### 5. Bob creates a one-way hash of the document that Alice has sent, Bob's digest.
Bob can again compute the hash of the document data.txt using the same hash function SHA-256 that has been used by Alice
{% highlight bash %}
$ openssl dgst -sha256 article.pdf > bob.dgst
{% endhighlight %}

### 6. Bob compares his digest with Alice's to find out if they match
{% highlight bash %}
$ diff -s alice.dgst bob.dgst
{% endhighlight %}
The result of the comparison is
{% highlight text %}
Files alice.dgst and bob.dgst are identical
{% endhighlight %}
proving that Alice has signed the document. The signature can not be repudiated and the document can not be changed without compromising the validity of the signature.
## Conclusion
We have seen how to use OpenSSL to add some level of security to our communications with the public-key cryptography and the symmetric encryption. As previously cautioned, the protocols we have shown are not completely secure, but they will certainly limit the number of eavesdroppers capable of figuring out the content of your digital assets sent over the Internet. You can get more information on cryptography, algorithms and how protocols can be improved to enhance the security of the communications, by consulting the resources in the references.
## Acknowledgments
Thanks to Eurydice Prentoulis for proof-reading the text.
## References
1. [Bruce Schneier - Applied Cryptography, 2nd Edition](https://www.schneier.com/books/applied_cryptography/)
2. [William Stein - Elementary Number Theory: Primes, Congruences, and Secrets](https://wstein.org/ent/)
3. [Dan Boneh, Victor Shoup - A Graduate Course in Applied Cryptography](https://crypto.stanford.edu/~dabo/cryptobook/)
4. [Alfred J. Menezes, Paul C. van Oorschot, Scott A. Vanstone - Handbook of Applied Cryptography](http://cacr.uwaterloo.ca/hac/)
5. [Dan Boneh - Cryptography I (Coursera)](https://www.coursera.org/learn/crypto)
6. [Christoph Paar, Jan Pelzl - Understanding Cryptography](http://www.crypto-textbook.com/)
