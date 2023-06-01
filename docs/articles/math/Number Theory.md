# Number Theory

### Mod
- **Basic properties**
```
a ≡ a(mod n)
a ≡ b(mod n)  <==>  b ≡ a(mod n)
a ≡ b(mod n) & b ≡ c(mod n)  =>  a ≡ c(mod n) 
```

- **Basic operations**
```
(a+b) mod n = ((a mod n) + (b mod n)) mod n
(a-b) mod n = ((a mod n) - (b mod n)) mod n
(a*b) mod n = ((a mod n) * (b mod n)) mod n
```

- **Advanced operations**
```
    if a ≡ b(mod n) & c ≡ d(mod n):
        ac ≡ bd(mod n)
```


### Prime number

A prime number is a natural number greater than 1 that has no positive divisors other than 1 and itself. In other words, a prime number is a number that cannot be formed by multiplying two smaller natural numbers. The first few prime numbers are 2, 3, 5, 7, 11, and 13.

Prime numbers are fundamentally important in number theory due to the Fundamental Theorem of Arithmetic, which states that every natural number greater than 1 is either a prime number itself or can be factored as a unique product of prime numbers, disregarding the order.

### Greatest common divisor

Two numbers are relatively prime if they have no common factors except 1. Written as ```gcd(a, n) = 1```.
A prime number is relatively prime to any number other than its multiple.

The easiest way to calculating gcd of two numbers is the Euclid's algorithm.

```
func gcd(x, y uint) uint {
	g := y
	for x > 0 {
		g = x
		x = y % x
		y = g
	}
	return g
}
```

We can easily infer that a gcd array of n numbers.
```
func multiple_gcd(a []uint) uint {
	g := a[0]
	for i := 1; i < len(a); i++ {
		g = gcd(g, a[i])
		if g == 1 {
			return g
		}
	}
	return g
}
```

### Modular inverse

In mathematics, the modular multiplicative inverse of an integer "a" is an integer "b" such that the product "ab" is congruent to 1 with respect to the modulus "m". Here, "b" is said to be the modular multiplicative inverse of "a" modulo "m".

The modular inverse of "a" modulo "m" exists if and only if "a" and "m" are coprime (i.e., the greatest common divisor of "a" and "m" is 1). If the modular multiplicative inverse of "a" modulo "m" exists, the operation of division by "a" modulo "m" can be defined as multiplication by the inverse.

Notation-wise, this is often written as ```b ≡ a⁻¹ (mod m)```, or sometimes as ```a*b ≡ 1 (mod m)```.

Here's how you can compute it:

Extended Euclidean Algorithm: This algorithm can be used to find the greatest common divisor of "a" and "m", and to express this greatest common divisor as a linear combination of "a" and "m". If "a" and "m" are coprime, then the coefficient of "a" in this linear combination is the modular multiplicative inverse of "a" modulo "m".

Fermat's Little Theorem: This theorem states that if "p" is a prime number and "a" is an integer that is not divisible by "p", then ```a^(p-1) ≡ 1 (mod p)```. If we multiply both sides of this congruence by "a^(-1)", we get ```a^(p-2) ≡ a^(-1) (mod p)```, which gives us the modular multiplicative inverse of "a" modulo "p".

### Fermat's little theorem

Fermat's Little Theorem is a fundamental theorem in number theory that was first proposed by Pierre de Fermat in 1640. The theorem is especially important in the field of cryptography and the testing of large prime numbers.

Fermat's Little Theorem states that if p is a prime number and a is an integer not divisible by p, then:

```a^(p-1) ≡ 1 (mod p)```

In other words, a^(p-1) leaves a remainder of 1 when divided by p.

There is an extended form of the theorem which states that for any integer a and prime p:

```a^p ≡ a (mod p)```

This means that if you raise a to the power of p, the result is equivalent to a modulo p.

Fermat's Little Theorem is used in the RSA algorithm (a widely used public key cryptography algorithm), in the proof of the AKS primality testing algorithm, and in the creation of pseudorandom number generators. It's also a fundamental result in the study of number theory.

### Euler totient function

### Chiness remainder theorem

### Quadratic residue

### Legendra symbol

### Jacobi symbol

### Blum integer

### Generator

### Galosi field

### factorization
- **Number Field Sieve(NFS)**
- **Quadratic Sieve(QS)**
- **Elliptic Curve Method(ECM)**
- **Pollard's Monte Carlo algorithm**
- **continued faction algorithm**
- **trial division**

## Generation of prime numbers

- ### Solovag-Strassen
- ### Lehmann
- ### Rabin-Miller
- ### Engineering Implement

### Strong Prime

### Discrete logarithms over finite fields
