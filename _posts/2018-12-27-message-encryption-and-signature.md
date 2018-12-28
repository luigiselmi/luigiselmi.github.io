---
layout: post
title:  "Public-key cryptography and digital signature using openssl"
date:   2018-12-27
categories: security
---
The purpose of this post is to explain how to communicate privately over the Internet using public-key cryptography and how to digitally sign a document.

## Introduction
The discussion is based on par. 2.5 and 2.6 of Schneier's book, [Applied Cryptography](https://www.schneier.com/books/applied_cryptography/). It is supposed you are using a Linux distribution. In case you use Window you might want to install [Cygwin](https://www.cygwin.com/) with openssl. You have by default openssh installed that allows you to login to a remote server using the ssh command. It is assumed that you know how to move a file from one folder to another one and how to copy a file. Openssh comes also with other commands that can be used to create a key pair to securely connect to a remote server using the key pair instead of the username and password. Openssh relies on Openssl that offers other tools to encrypt a file and create an hash of a document to sign it. We will set up a context for the secure communication problem using two characters, Alice and Bob. We will store their keys and messages in separate folders on our local file system
{% highlight text %}
$ mkdir alice

$ mkdir bob
{% endhighlight %}
## Alice and Bob
Bob can't remember his bank account details and asks Alice to send them to him by email. Alice is aware that sending the data as plain text over the Internet is risky so she wonders how to send the data to Bob in such a way that nobody else but him can read and use the data. After some investigation Alice has come up with the idea that the solution to their problem is public-key cryptography and the openssl tools.

## Public-key cryptography
This is what she and Bob have to do

1. Alice and Bob create their own private and public keys.
2. Bob sends Alice his public key.
3. Alice encrypts her message using Bob’s public key and sends it to Bob.
4. Bob decrypts Alice’s message using his private key.

So, first of all both Alice and Bob need a key pair.

### 1. Alice and Bob create their own private and public keys.
Alice doesn’t have yet a key pair so she needs to create it, as an example using the RSA cryptosystem with the name of the file that will contain the private key set to alice_rsa and the private key’s size of 2048 bit. Let's move into Alice's folder and execute the command
{% highlight bash %}
$ openssl genpkey -algorithm RSA -out alice_rsa -pkeyopt rsa_keygen_bits:2048
{% endhighlight %}
The private key in alice_rsa is saved in the Privacy-Enhanced Mail (PEM) format and looks like the following
{% highlight text %}
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
Now we have Alice's key pair in her folder. Let's do the same for Bob. We move into Bob's folder and create his key pair bob_rsa and bob_rsa.pub as we did for Alice. After Alice and Bob have their key pair we are done with the 1st step of the procedure.

### 2. Bob sends to Alice his public key.
Let's move to the 2nd step: Bob must send his public key to Alice so she will be able to send him her message. We simulate this by copying Bob's public key file, bob_rsa.pub in Alice's folder. From Bob's folder
{% highlight bash %}
$ cp bob_rsa.pub ../alice/
{% endhighlight %}
As soon as a copy of Bob's public key is in Alice's folder the 2nd step of the procedure is completed and we can move to the 3rd: Alice will encrypt her message using Bob's public key and will send it to Bob.

### 3. Alice encrypts her message using Bob’s public key and sends it to Bob.
Bob's public key can now be used by Alice with openssl to encrypt her message stored in a file, e.g. data.txt, containing sensitive information
{% highlight bash %}
Bob Bank Account
userid: 123456
password: 276f8%2=0as}
pin: 4657
{% endhighlight %}
Alice encrypts the file using openssl and Bob’s public key that she has received from him, e.g. by email, and that we have simulated by simply copying the file from Bob's folder to Alice's. From Alice's folder
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub -in data.txt -out data.txt.enc
{% endhighlight %}
Now Alice can send her encrypted message, data.txt.enc. The encrypted message is a binary file whose content doesn't make any sense and can be decrypted only by Bob using his private key. If Alice was a real person she could send it to Bob by email. We will simulate the sending of the encrypted message by copying it in Bob's folder. From Alice's folder's
{% highlight bash %}
$ cp data.txt.enc ../bob/
{% endhighlight %}
After the encrypted message has been received by Bob, in our simulation after it is in Bob's folder, the 3rd step is completed and we can move to the 4th and last step.

### 4. Bob decrypts Alice’s message using his private key.
From Bob's folder
{% highlight bash %}
$ openssl rsautl -decrypt -inkey bob_rsa -in data.txt.enc -out data.txt
{% endhighlight %}
Bob can open the file data.txt containing the original message in plain text that Alice wanted to send to him. We can easily check the Bob's decrypted message and Alice's original message are exactly the same. From the root folder
{% highlight bash %}
diff -s alice/data.txt bob/data.txt
{% endhighlight %}
The procedure that Alice chose to send her message to Bob, without risking anyone else could read it, is completed. In this example Alice didn't use her private or public key. In case Bob wanted to send a feedback to her he could use Alice's public key to encrypt his message so that only her could decrypt it using her private key. Both Alice and Bob must keep their private keys in a very safe place. The private key we have just created for them can be used by anyone who has access to it. One way to protect the private key is to encrypt it using an algorithm, e.g. AES-128, with a password so that only who knows the password can decrypt the private key and use it to read the messages sent by others and encrypted using the public key. For example Alice could have made her private key safer by creating it with the following command
{% highlight bash %}
$ openssl genpkey -algorithm RSA -out alice_rsa -aes-128-cbc -pass pass:$wT(16p
{% endhighlight %}
where $wT(16p would be Alice's password.

## Hybrid cryptosystem
Alice has successfully solved Bob's problem. She's been able to send to him his bank account details in a secure way. Now she wants to send to Bob a file, e.g. a jpeg picture she doesn't want anyone else to see and whose size is some KB
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub -in alice.jpg -out alice.jpg.enc
{% endhighlight %}
This time openssl will raise an error
{% highlight text %}
RSA operation error
4294956672:error:0406D06E:rsa routines:RSA_padding_add_PKCS1_type_2:data too large for key size:rsa_pk1.c:174:
{% endhighlight %}
The problem is that the RSA algorithm can be used only to encrypt messages whose size is smaller than the size of the private key that corresponds to the public key used for the encryption. Since Bob's private key is 2048 bit long, or 256 bytes, his public key cannot be used to encrypt messages that are bigger than 256 bytes. The best option to solve the problem is to use a symmetric key to encrypt the file and then send the encrypted file and the symmetric key encrypted using the public key of the recipient. The system is called hybrid because it uses public-key and symmetric cryptography together. Alice defines a new protocol in which she will create a symmetric key that will share with Bob

1. Alice creates a symmetric key.
2. Alice encrypts the data using the symmetric key.
3. Alice encrypts the symmetric key using Bob’s public key.
4. Alice sends the encrypted data and the encrypted symmetric key to Bob.
5. Bob decrypts the symmetric key using his private key.
6. Bob decrypts the data using the symmetric key.

Let's implement these steps on behalf of Alice and Bob using openssl.

### 1. Alice creates a symmetric key.
First, Alice creates a symmetric key. From Alice's folder
{% highlight bash %}
$ openssl rand 32 -out symmetric.key
{% endhighlight %}

### 2. Alice encrypts the data using the symmetric key.
{% highlight bash %}
 $ openssl enc -e -in alice.jpg -out alice.jpg.enc -K symmetric.key
{% endhighlight %}

### 3. Alice encrypts the symmetric key using Bob’s public key.
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub -in symmetric.key -out symmetric.key.enc
{% endhighlight %}

### 4. Alice sends the encrypted data and the encrypted symmetric key to Bob.
We can simulate the sending of the encrypted data and symmetric key by copying them from Alice's folder to Bob's.
{% highlight bash %}
$ cp alice.jpg.enc symmetric.key.enc ../bob
{% endhighlight %}

### 5. Bob decrypts the symmetric key using his private key.
From Bob's folder
{% highlight bash %}
$ openssl rsautl -decrypt -inkey bob_rsa -in symmetric.key.enc -out symmetric.key
{% endhighlight %}

### 6. Bob decrypts the data using the symmetric key.
{% highlight bash %}
$ openssl enc -d -in alice.jpg.enc -out alice.jpg -K symmetric.key
{% endhighlight %}

## Digital signature
Alice is a journalist and wants to send to Bob an article, e.g. a pdf file, being sure than no one else can claim to be the author. Once again she comes up with a protocol that can solve her problem.

1. Alice creates a one-way hash of a document, Alice's digest.
2. Alice encrypts the digest with her private key, thereby signing the document.
3. Alice sends the document, her public key and the signed digest to Bob.
4. Bob decrypts Alice's digest with her public key.
5. Bob creates a one-way hash of the document that Alice sent, Bob's digest.
6. Bob compares his digest with Alice's to find out if they match

If the signed hash matches the hash he generated, the signature is valid. Let’s say Alice wants to send a file, e.g. data.txt, with her digital signature to Bob.
### 1. Alice creates a one-way hash of a document, Alice's digest.
Alice can sign the message using openssl, choosing one hash function, e.g. SHA-256 . She can create the one-way hash of the message, also known as the digest,  with
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
The output, alice.dgst, is Alice's digest of the document extracted from her signature of the document.

### 5. Bob creates a one-way hash of the document that Alice has sent, Bob's digest.
Bob can compute again the hash of the document data.txt using the same hash function SHA-256 that has been used by Alice
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
proving that Alice signed the document. The signature cannot be repudiated and the document cannot be changed without compromising the validity of the signature.
