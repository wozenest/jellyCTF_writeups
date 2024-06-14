# Dizzy Fishman
`jellyCTF{SOS_stuck_in_warehouse} `
## Synopsis
A Diffie-Hellman key exchange algorithm is used to exchange a shared secret key, but the server is not validating the generator `g` to be a primitive root modulo `p`. We can inject a generator `g` that is not a primitive root modulo `p` to reduce the security of the Diffie-Hellman key exchange algorithm and reveal the shared secret key `s`.
## Description
Sakana is sending some suspicious looking messages to Dizzy - looks like they're exchanging a shared secret key to encrypt the messages.

Alice has hacked into their key exchange system but needs more help with the exploit. Can you find a way to reveal their secret key and decrypt the message?

10 point hint: Algorithm/Area to focus on
20 point hint: Example that could work if it wasn't validated by the challenge

Author: Sheepiroo

`` nc chals.jellyc.tf 4000 ``
## Attachments

## Breakdown
First, let's connect to the server and see what it's about.
```bash
$ nc chals.jellyc.tf 4000
Intercepting communications...
Randomly selected prime p =  69528476269091555143940840904683624299477177167967753329321975768265476608983
Inject a generator g:
```
The server is asking us to inject a generator `g` for the prime `p` that it has selected. This implies that the server is using the Diffie-Hellman key exchange algorithm. The Diffie-Hellman key exchange algorithm is a method for securely exchanging cryptographic keys over a public channel. The algorithm is as follows:
1. Alice and Bob agree on a prime number `p` and a generator `g` for the prime `p`. (The requirements for `g` are that it must be a primitive root modulo `p`, which we will discuss later.)
2. Alice generates a private key `a` and calculates `A = g^a mod p`.
3. Bob generates a private key `b` and calculates `B = g^b mod p`.
4. Alice and Bob exchange `A` and `B` over the public channel.
5. Alice calculates the shared secret key `s = B^a mod p`.
6. Bob calculates the shared secret key `s = A^b mod p`.
7. Alice and Bob now have the same shared secret key `s`.

The security of this algorithm relies on the fact that the discrete logarithm problem is hard. The discrete logarithm problem is the problem of finding `x` given `g`, `p`, and `g^x mod p`. If the discrete logarithm problem is easy, then an attacker can easily find the shared secret key `s` by calculating `s = g^(ab) mod p` using the values of `A` and `B` that are exchanged over the public channel.

However, if we are able to inject a generator `g` that is not a primitive root modulo `p`, then it can greatly reduce the security of the Diffie-Hellman key exchange algorithm.
## Attack

An obvious choice for the generator `g` is `0`. If `g = 0`, then `A = B = 0`, and the shared secret key `s = 0`. This is because `0^x mod p = 0` for all `x`. Therefore, the shared secret key `s` will always be `0` if `g = 0`.

However, if we try using `g=0`, we get the error message
```bash
ALERT: Invalid g detected, requires 1 < g < p. Communications terminated!
```
This means that the server is validating the generator `g`, in fact, by inspecting the source code, we see that the server is checking `1 < g < p`. However, notice that the server is not checking if `g` is a primitive root modulo `p`. This means that we can inject a generator `g` that is not a primitive root modulo `p` to reduce the security of the Diffie-Hellman key exchange algorithm.

Consider `g = p-1`. If `g = p-1`, then `g^a mod p = ±1` for all `a`. This is because `(-1)^x mod p = 1` if `x` is even and `-1` if `x` is odd. Therefore, the shared secret key `s` will always be `1` or `-1` if `g = p-1`.

Let's try injecting `g = p-1` and see if we can get the shared secret key `s`.
```bash
Intercepting communications...
Randomly selected prime p =  88620289345998942324333284188938991880860352555312247272296799242597365605227
Inject a generator g: 88620289345998942324333284188938991880860352555312247272296799242597365605226
Dizzy's public key (integer) :  1
Sakana's public key (integer):  88620289345998942324333284188938991880860352555312247272296799242597365605226
Dizzy and Sakana are calculating their secret keys...
Encrypting flag with AES-256 using shared secret key
Encrypted flag received:  2c6783bc372fbf601a4159080bf295e439c30e16fecde63dc7066abb40825383b1d8b2267d641fc17fd54d8bb0a60203b1d8b2267d641fc17fd54d8bb0a60203
```
Bingo! We now have all the information we need to find the secret key `s`.

Notice that Dizzy's public key is `1` and Sakana's public key is `p-1`. This implies that `a` is even and `b` is odd. Therefore, the shared secret key `s` will be `1`. Note that the only case where the shared secret key `s` will be `-1` is when both `a` and `b` are odd.

Now, we can decrypt the flag using the shared secret key by inspecting the source code. The flag is encrypted using AES-256 EBC mode with the shared secret key `s` as the key. We can decrypt the flag using the following Python script.
```python
from Crypto.Cipher import AES
from Crypto.Util.Padding import pad
encrypted_flag = bytes.fromhex("2c6783bc372fbf601a4159080bf295e439c30e16fecde63dc7066abb40825383b1d8b2267d641fc17fd54d8bb0a60203b1d8b2267d641fc17fd54d8bb0a60203")
secret = 1
encoded_key = secret.to_bytes(32, byteorder='big')
cipher = AES.new(encoded_key, AES.MODE_EBC)
decrypted_flag = cipher.decrypt(encrypted_flag)
print(decrypted_flag)
```
Running the script, we get the flag:
`jellyCTF{SOS_stuck_in_warehouse} `