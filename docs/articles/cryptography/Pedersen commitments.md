### Pedersen commitments

Pedersen commitments are a cryptographic tool used to commit to a value without revealing it. A Pedersen commitment is a mathematical function that takes two inputs: a value to be committed to (denoted as "m") and a randomly generated number (denoted as "r"). The output of the function is a commitment, denoted as "C", which is a number that can be made public without revealing m or r.

The Pedersen commitment scheme has the following properties:

* Hiding property: Given a Pedersen commitment C, it is computationally infeasible to determine the values m and r that were used to generate the commitment.

* Binding property: It is computationally infeasible to find two different values of m and r that produce the same Pedersen commitment C.

Pedersen commitments are commonly used in blockchain and cryptocurrency systems to provide privacy and security for transactions. When a user wants to send cryptocurrency to another user, they can use a Pedersen commitment to hide the amount of cryptocurrency being sent while still proving that they have sufficient funds to make the transaction.

Pedersen commitment function can be expressed using mathematical notation as:

C = g^m * h^r mod p

where:

- g and h are public generators of a large prime order group (i.e., they are publicly known constants)
- p is a large prime number
- m is the value being committed to
- r is a randomly generated number
To verify the commitment, one can check that the following equation holds:

g^m * h^r ≡ C mod p

where ≡ means "is congruent to".

To see that this commitment scheme has the hiding property, note that given C it is difficult to determine m and r because this would require solving the discrete logarithm problem, which is computationally infeasible for large prime numbers.

To see that the scheme has the binding property, suppose someone knows two pairs (m1, r1) and (m2, r2) such that m1 ≠ m2 and C = g^m1 * h^r1 = g^m2 * h^r2. Then, we can construct an equation that equates the two pairs:

g^(m1-m2) * h^(r1-r2) ≡ 1 mod p

Since g and h are generators of a large prime order group, they are primitive elements modulo p, which means that the only solution to this equation is for both exponents to be zero. But this is impossible since m1 ≠ m2. Therefore, the binding property is satisfied.

Here is an example that generates commitments:
```

package pedersen_commitments

import (
	"crypto/rand"
	"fmt"
	"math/big"
)

var (
	g    = big.NewInt(2) // generator
	h    = big.NewInt(3) // generator
	p, _ = new(big.Int).SetString("6277101735386680763835789423207666416083908700390324961279", 10)
)

type Commitments struct {
	m *big.Int
	r *big.Int
}

func GenerateCommitments(m *big.Int) *Commitments {
	// Generate a random r value
	r, _ := rand.Int(rand.Reader, p)

	// Compute the commitment C = g^m * h^r mod p
	gm := new(big.Int).Exp(g, m, p)
	hr := new(big.Int).Exp(h, r, p)
	C := new(big.Int).Mul(gm, hr)
	C.Mod(C, p)

	// Print the commitment C and the random value r
	fmt.Println("Commitment C: ", C)
	fmt.Println("Random value r: ", r)

	return &Commitments{C, r}
}

```


### Homomorphic property 

Notably, the Pedersen commitment is homomorphic. In fact, the Pedersen commitment is an example of a homomorphic commitment scheme.

In the Pedersen commitment, the commitment for a value m with random value r is calculated as:

```
C = g^m * h^r mod p
```

where g and h are arbitrary generators of a group of prime order p.

The homomorphic property of the Pedersen commitment can be demonstrated as follows:

```

C1 = g^m1 * h^r1 mod p
C2 = g^m2 * h^r2 mod p

C1 * C2 = (g^m1 * h^r1) * (g^m2 * h^r2) mod p
       = g^(m1+m2) * h^(r1+r2) mod p

Commitment(m1+m2, r1+r2) = g^(m1+m2) * h^(r1+r2) mod p

Therefore, Commitment(m1+m2, r1+r2) = C1 * C2

```

This shows that the Pedersen commitment is homomorphic. By computing the product of two commitments, it is possible to obtain the commitment to the sum of the original two values. Because the homomorphic property holds, it is also possible to manipulate the commitment value without revealing any information about the commitment or the committed values.


Here is an example of the homomorphic property:

```

package main

import (
	"crypto/rand"
	"fmt"
	"math/big"
)

var (
	g    = big.NewInt(2)
	h    = big.NewInt(3)
	p, _ = new(big.Int).SetString("6277101735386680763835789423207666416083908700390324961279", 10)
)

type Commitments struct {
	m *big.Int
	r *big.Int
}

func GenerateCommitments(values []*big.Int) []*Commitments {
	numParticipants := len(values)

	r := make([]*big.Int, numParticipants)
	for i := 0; i < numParticipants; i++ {
		r[i], _ = rand.Int(rand.Reader, p)
	}

	cs := make([]*big.Int, numParticipants)
	for i := 0; i < numParticipants; i++ {
		gm := new(big.Int).Exp(g, values[i], p)
		hr := new(big.Int).Exp(h, r[i], p)
		C := new(big.Int).Mul(gm, hr)
		C.Mod(C, p)
		cs[i] = C
	}

	commitments := make([]*Commitments, numParticipants)
	for i := 0; i < numParticipants; i++ {
		commitments[i] = &Commitments{cs[i], r[i]}
	}

	return commitments
}

func Verify(values []*big.Int, commitments []*Commitments) bool {
	combinedValue := big.NewInt(0)
	for _, value := range values {
		combinedValue = new(big.Int).Add(combinedValue, value)
	}

	gm := new(big.Int).Exp(g, combinedValue, p)
	product := big.NewInt(1)
	hr := big.NewInt(0)
	for _, commitment := range commitments {
		hr = new(big.Int).Add(hr, commitment.r)
		product = new(big.Int).Mul(product, commitment.m)
		product.Mod(product, p)
	}

	C := new(big.Int).Mul(gm, new(big.Int).Exp(h, hr, p))
	C.Mod(C, p)

	return C.Cmp(product) == 0
}

func main() {
	values := []*big.Int{big.NewInt(10), big.NewInt(20), big.NewInt(30)}

	commitments := GenerateCommitments(values)

	fmt.Println(Verify(values, commitments))
}


```