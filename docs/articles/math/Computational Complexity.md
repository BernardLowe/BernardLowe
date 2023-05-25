# Computational Complexity

### Algorithmic complexity
The complexity of an algorithm is the computing power required to run it. The computational complexity of an algorithm is often measured in terms of two variables: **T**(**time complexity**) and **S**(**space complexity**). T and S are usually expressed as functions of n, where n is the size of the input.

Typically, algorithms are categorized by their time and space complexity. If the complexity of an algorithm does not depend on n, that is O(1), then it is **constant**. If its time complexity is O(n), then it is **linear**. Some algorithms can also be divided into **quadratic**, **cubic** and so on. All these algorithms are **polynomial**, and the complexity can be expressed as O(n^m), where m is a constant. Algorithms with polynomial time complexity are called polynomial time algorithms.

An algorithm is exponential if its complexity is O(t^f(n)), where t is a constant greater than 1 and f(n) is a polynomial function of n. A subset of exponential algorithms with a complexity of O(c^f(n)), called hyperpolynomial algorithms, where c is a constant and f(n) is a function that is greater than a constant but less than linear.

In theory, cipher designers expect any attack algorithm on their cipher to have exponential time complexity. In fact, according to the current state of computational complexity theory, "all known attack algorithms have superpolynomial time complexity". That is to say, for these practical ciphers, the attack algorithms we know are hyperpolynomial time complexity, but it cannot be proved that non-polynomial time cocomputation algorithms will never be found.

### Complexity of the problem
Problems that can be solved by a polynomial time algorithm are said to be **tractable** because they can be solved in a reasonable amount of time with a modest input size. Problems that cannot be solved in polynomial time are called **intractable**, and intractable problems are also called **hard**.

At a low level, problems of class P include all problems that can be solved in polynomial time. The NP class includes all problems that can be solved in polynomial time on non-deterministic Turing machines. A non-deterministic Turing machine is a variant of a standard Turing machine that can make guesses. The machine guesses all possible solutions by "lucky guessing" or trying in parallel, and then checks its guesses in polynomial time.

### NP-complete problem
- **The Traveling Salesman Problem (TSP)**: Given a list of cities and the distances between each pair of cities, the task is to find the shortest possible route that visits each city exactly once and returns to the starting city.
- **The Knapsack Problem**: Given a set of items, each with a weight and a value, and a knapsack with a maximum weight capacity, the problem is to determine the most valuable combination of items that can be included in the knapsack without exceeding its capacity.
- **The Graph Coloring Problem**: Given an undirected graph, the task is to assign colors to its vertices such that no two adjacent vertices have the same color and the minimum number of colors is used.
