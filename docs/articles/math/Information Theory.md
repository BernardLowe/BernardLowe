# Information Theory

### Amount of information
Definition: The minimum number of bits required to encode all possible values in a message, assuming all messages are equally probable.
For example we can assume:
```
000: Sunday
001: Monday
010: Tuesday
011: Wednesday
100: Thursday
101: Friday
110: Saturday
111  NOT USED
```
Therefore the message can be encoded with 3 bits.

### Entropy
The amount of information in a message M can be measured by its entropy, expressed as H(M). A message representing the gender has 1 bit of entropy, and a message representing the day of the week has slightly less than three bits of entropy.
In general, the entropy of a message is Log2(n), where n is all possible values of the message. It is assumed here that each value is equally likely.

### Uncertainty
The entropy of a message also expresses its uncertainty, that is, when the message is encrypted into ciphertext, in order to obtain the plaintext, the number of bits of the plaintext that needs to be decrypted.
For example, if the ciphertext "QHP*5M" is either "Male" or "Female", then the uncertainty of this message is 1. To recover the message, the cryptanalyst needs to choose only 1 bit.

### Rate of language
For a given language, its rate of language is ```r=H(M)/N```, where N is the length of the message.
The absolute rate is defined as: Assuming each string is equally likely, the maximum number of bits that can be encoded for each letter. If there are L letters in a language, its absolute rate is: ```R=Log2(L)```. This is the maximum entropy of a single letter.
English has 26 letters, and its absolute information rate is log2(26)=4.7 bit per letter.

### Redundancy
In fact, the actual information rate of English is much lower than its absolute information rate, and the redundancy of this language becomes D, which is defined as: ```D = R-r```.

### Security of Cryptographic Systems
Shannon defined an exact mathematical model for secure cryptosystems. The cryptanalyst's goal is to obtain either the key K or the plaintext P, or both. Of course they are also happy to get some statistics about P.

There is a way to achieve a completely secret cryptosystem. In such a system, the ciphertext does not give any information about the plaintext, except possibly its length. Shannon proved theoretically that perfect secrecy is possible only if the number of possible keys is at least as large as the number of possible messages. In other words, only the one-time pad cipher system can obtain complete secrecy.

From the point of view of complete secrecy, it is inevitable that the ciphertext gives some information about its corresponding plaintext. A good cryptographic algorithm minimizes the information that a good cryptanalyst can use to determine the plaintext.

Cryptanalysts exploit the redundancy of natural language to reduce the number of possible plaintexts. The greater the redundancy of language, the easier it is to attack. This is why many cryptographic devices in use use a compression program to reduce the plaintext size before encrypting the plaintext. Encryption and decryption require compression to reduce message redundancy.

The entropy of a cryptosystem can be measured by the size of the key space K: ```H(K) = log2(K)```.
The entropy of a cryptosystem with a 64-bit key is 64, and the entropy of a 56-bit key system is 56. Generally speaking, the greater the entropy of a cryptosystem, the more difficult it is to decipher it.

### unidty distance
For a message of length n, to decrypt a ciphertext message into some meaningful plaintext, the number of different keys is given by: ```2^(H(k)-nD)-1```.

Shannon defines the unidty distance U as: The asymptotic amount of ciphertext that makes the sum of the actual information (entropy) of the corresponding plaintext and the entropy of the encryption key equal to the number of ciphertext bits used.

A ciphertext longer than this distance can reasonably determine the only meaningful decryption text; a ciphertext shorter than this distance may have multiple equally valid decryption texts, because it is difficult to select the correct one. Hence security is gained.

For most symmetric cryptosystems, the unique solution distance is defined as the entropy of the cryptosystem divided by the redundancy of the language: ```U = H(K)/D```.

Shannon defined a cryptosystem whose unique solution distance is infinite as ideal secrecy. It is worth noting that while a perfectly secure cryptosystem must be an ideally secure cryptosystem, an ideally secure cryptosystem is not necessarily a perfectly secure cryptosystem.
If a cryptosystem has perfect secrecy, even a successful cryptanalyst cannot be sure whether the plaintext of the solution is the real plaintext.

### Applications of Information Theory
- Confusion：Confusion is used to obscure the relationship between plaintext and ciphertext.
- Diffusion: Diffusion spreads out the plaintext redundancy by spreading it into the ciphertext.
