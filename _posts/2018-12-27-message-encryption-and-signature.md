---
layout: post
title:  "Message Encryption and Signature"
date:   2018-12-27
categories: security
---
## Introduction
Alice wants to send an encrypted message to Bob so that no one else can read it.
It is supposed you are using Linux Ubuntu. You have by default openssh installed that allows you to login to a remote server using the ssh command. Openssh comes also with other commands that can be used to create a keypair to securely connect to a remote server using the key pair instead of the username and password. Openssh relies on Openssl that offers other tools to encrypt a file and create an hash of a document to sign it.

## Creating a key pair
Bob doesn’t have yet a key pair so he needs to create one, as an example using the RSA cryptosystem with the private key’s size of 2048 bit and his email as a comment in the public key
{% highlight bash %}
$ ssh-keygen -t rsa -b 2048 -C "bob@bob.com"
{% endhighlight %}
The command creates two key files, by default id_rsa, the private key, and id_rsa.pub, the public key. The private key is in the Privacy-Enhanced Mail (PEM) format and begins with the line

-----BEGIN RSA PRIVATE KEY-----

and ends with

-----END RSA PRIVATE KEY-----

Keep the private key in a safe place. You can always recreate the public key from the private one
{% highlight bash %}
$ ssh-keygen -y -f id_rsa > id_rsa.pub
{% endhighlight %}
Let’s rename the key pair using the name of the recipient of Alice’s message, Bob, bob_rsa and bob_rsa.pub.

The public key created with ssh-keygen cannot be used directly to encrypt a file, it must be converted in a format that can be used by openssl
{% highlight bash %}
$ ssh-keygen -f bob_rsa.pub -e -m PKCS8 > bob_rsa.pub.pem
{% endhighlight %}
Bob can send any of the two public key file to Alice. In case he sends the key file created with ssh-keygen Alice will have to transform it in a format that can be used by openssl. Let’s suppose Bob sends the openssl formatted public key.

## Encrypt and decrypt a small message
The public key can now be used by openssl to encrypt short messages or a small file containing the confidential information, as an example “alice_data.txt” with the following content

Bank account=Alice
IBAN=DE127346902000034450000223
Bank account password=276f8%2=0as}

Alice encrypts the file using openssl and Bob’s public key
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub.pem -in alice_data.txt -out alice_data.txt.enc
{% endhighlight %}
Alice will send the encrypted file, alice_data.txt.enc, to Bob. Bob will decrypt the file using his private key with the command
{% highlight bash %}
$ openssl rsautl -decrypt -inkey bob_rsa -in alice_data.txt.enc
{% endhighlight %}

## Encrypt and decrypt a large message
If you need to encrypt a large file, even few KB, you need to use a symmetric key because the pub-private key pair cannot be used to encrypt large files. The best option is to use a symmetric key to encrypt the message or file and then send the encrypted message and the symmetric key encrypted using the public key of the recipient.

Bob sends Alice his public key.
Alice generates a random session key, K, encrypts it using Bob’s public key, and sends it to Bob.
Bob decrypts Alice’s message using his private key to recover the session key.
Both of them encrypt their communications using the same session key.


First, Alice creates her symmetric key
{% highlight bash %}
$ openssl rand 32 -out alice_symmetric.key
{% endhighlight %}
2nd, she encrypts the message with her symmetric key
{% highlight bash %}
 $ openssl enc -e -in large_message.txt -out large_message.txt.enc -K alice_symmetric.key
{% endhighlight %}
3rd, she encrypts her symmetric key using Bob’s public key
{% highlight bash %}
$ openssl rsautl -encrypt -pubin -inkey bob_rsa.pub.pem -in alice_symmetric.key -out alice_symmetric.key.enc
{% endhighlight %}
Last, she sends to Bob the symmetric encrypted message large_message.txt.enc and her symmetric key alice_symmetric.key.enc encrypted using the Bob’s public key.

Bob decrypts the symmetric key
{% highlight bash %}
$ openssl rsautl -decrypt -inkey bob_rsa -in alice_symmetric.key.enc
{% endhighlight %}
And then decrypts the message using Alice’s symmetric key
{% highlight bash %}
$ openssl enc -d -in large_message.txt.enc -out large_message.txt -K alice_symmetric.key
{% endhighlight %}

## Signing a message
Alice can prove to Bob that she created a message by encrypting the message with her private key. Bob will be able to decrypt the message using Alice’s public key, being sure that Alice created the message since only she could encrypt it. Unfortunately the private key cannot be used with openssl to encrypt a large message. Another protocol that uses the hash function of the document is the following

Alice produces a one-way hash of a document.
Alice encrypts the hash with her private key, thereby signing the document.
Alice sends the document and the signed hash to Bob.
Bob produces a one-way hash of the document that Alice sent. He then, using the digital signature algorithm, decrypts the signed hash with Alice’s public key. If the signed hash matches the hash he generated, the signature is valid.

Let’s say Alice wants to send a file, e.g. data.txt, with her digital signature to Bob. She can sign the message using openssl and choosing one hash function, e.g. SHA-256 . She can create the one-way hash of the message, also known as the digest,  with
{% highlight bash %}
$ openssl dgst -sha256 data.txt > data.dgst
{% endhighlight %}
The content of the digest will be similar to
SHA256(data.txt)= a698dfd41e3aa4eabc0b0b5e5ae3b615d06ef19929fc1770cb5b0c2aef90b647

The next step is to encrypt the digest of the hash function, data.dgst, with her private key
{% highlight bash %}
$ openssl rsautl -sign -inkey alice_rsa -keyform PEM -in data.dgst > alice.sign
{% endhighlight %}
Alice sends the document, data.txt, with her signature, alice.sign, to Bob. Bob can verify Alice’s signature of the document using her public key
{% highlight bash %}
$ openssl rsautl -verify -inkey alice_rsa.pub.pem -pubin -keyform PEM -in alice.sign -out data.dgst
{% endhighlight %}
The output, data.dgst, is the digest of the document. Bob can compute again the hash of the document data.txt using the same hash function SHA-256 that has been used by Alice
{% highlight bash %}
$ openssl dgst -sha256 data.txt > bob.dgst
{% endhighlight %}
to find out whether it matches with the output of the verification  
{% highlight bash %}
$ diff -s data.dgst bob.dgst
{% endhighlight %}
If the digests are the same it is a proof that Alice signed the document data.txt. The signature cannot be repudiated and the document cannot be changed without compromising the validity of the signature.
