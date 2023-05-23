## Information Theory

### amount of information
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