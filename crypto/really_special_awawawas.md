# really_special_awawawas
`jellyCTF{awawas_4_every1}`
## Synopsis
A very basic RSA challenge, where we are given the public key, ciphertext, and the modulus. The modulus is not a product of two primes, and we can thus factorize it to get phi(n) and the private key. We can then decrypt the ciphertext to get the flag.
## Challenge Text
A specially chosen modulus for a really special awatistic musical princess.

dCode couldn't crack it so I'm sure it's secure... right?

Hint: General steps to solution + link to a post

Author: Sheepiroo

Attachment: vals.txt
## Attachments
### vals.txt
```
n = 40095322948381328531315369020145890848992927830000776301309425505
e = 65537
c = 35622053067320123838840878683947610930876835359945867019927573838
```
## Breakdown
The RSA algorithm can be found [here](https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

For a brief summary, the RSA algorithm uses a public key `(e, n)` and a private key `(d, n)` to encrypt and decrypt messages. The public key is used to encrypt the message, and the private key is used to decrypt the message. The security of the RSA algorithm relies on the difficulty of factoring the modulus `n` into its prime factors `p` and `q` is extremely difficult.

The relationship between the public key `(e, n)` and the private key `(d, n)` is as follows:
```
d = e^-1 mod phi(n)
```
where `phi(n) = (p-1)(q-1)`. The private key `d` is the modular multiplicative inverse of the public key `e` modulo `phi(n)`.
## Attack
It is immediately apparent that the modulus `n` is not a product of two primes. First, we find that `n` ends with 5, thus we can factorize `n` using SageMath to get
```python
n = 40095322948381328531315369020145890848992927830000776301309425505
n.factor()
```
This gives us the factors of `n` as
`
5 * 23 * 460465412038271581 * 757179525420813109550252454787205779901919127`
We can then calculate `phi(n)` as each of the prime factors minus 1, and then calculate the modular multiplicative inverse of `e` modulo `phi(n)` to get the private key `d`. Finally, we can decrypt the ciphertext `c` using the private key `d` to get the flag.
```python
phi = 4 * 22 * 460465412038271580 * 757179525420813109550252454787205779901919126
```
Using SageMath, we can calculate the modular multiplicative inverse of `e` modulo `phi(n)` as
```python
e = 65537
d = inverse_mod(e, phi)
print(d)
```
`d=20102683281001902465824818287116957581497290427382915258401878273`
Finally, we can decrypt the ciphertext `c` using the private key `d` to get the flag.
```python
c = 35622053067320123838840878683947610930876835359945867019927573838
m = pow(c, d, n)
bytes.fromhex(hex(m)[2:]).decode()
```
The flag is `jellyCTF{awawas_4_every1}`
