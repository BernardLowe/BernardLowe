### Verifiable shuffles

There are two lists of committed values: ```x1,...,xn``` and ```y1,...,yn```. The goal is to prove that the second list is a permutation of the first. This problem is called a verifiable shuffle. 

Efficient zero-knowledge argument for the correctness of a shuffle is an implementation based on the discrete logarithm problem. It involves the following steps:

Input parameters: An array m containing the original messages, an array m' containing the randomly shuffled messages, and a public parameter G (typically a group related to the discrete logarithm problem).

Establish an interactive proof system with a Prover and a Verifier.

The Prover generates a random number r and computes a commitment value C as C = g^r * h^s, where g and h are generators in the group G, and s is a random number.

The Prover computes a proof value, a vector t containing polynomial coefficients, and sends it to the Verifier.

The Verifier checks if the received t satisfies certain constraints, such as the sum of coefficients being zero and the number of coefficients matching the lengths of m and m'.

If the verification passes, the Verifier sends a random challenge value e to the Prover.

The Prover computes a response value z based on e and sends it to the Verifier.

The Verifier checks if the received z satisfies certain constraints, such as its length matching the length of t.

If the verification passes, the Verifier accepts the proof; otherwise, it rejects it.

The Prover and Verifier repeat the challenge and response process until the Verifier fully accepts the proof.

Finally, the Verifier uses the commitment value C, the challenge value e, and the response value z provided by the Prover to compute a verification value V. It checks if V equals g^t(m) * h^t(m').

If V is equal to g^t(m) * h^t(m'), the Verifier accepts the proof; otherwise, it rejects it.

In summary, the efficient zero-knowledge argument for the correctness of a shuffle allows the Prover to prove knowledge of a permutation that transforms the original message array m into the shuffled message array m'. The proof is interactive, ensuring that the Prover does not reveal any information about the permutation. The Verifier can verify the correctness of the shuffle using the proof provided by the Prover.