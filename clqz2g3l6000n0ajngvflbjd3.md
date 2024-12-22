---
title: "Circuit Satisfiability to Quadratic Arithmetic Program"
datePublished: Thu Jan 04 2024 10:30:13 GMT+0000 (Coordinated Universal Time)
cuid: clqz2g3l6000n0ajngvflbjd3
slug: circuit-satisfiability-to-quadratic-arithmetic-program
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1704174695792/8e7a561b-ed3d-4d8b-8c88-16c793019494.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1704193554452/db3f0180-d17f-4370-8e52-9b868d2edc9c.jpeg
tags: blockchain, zero-knowledge-proofs, quadtratic-arithmetic, r1cs

---

In this article, we will explore how the following topics:

* R1CS
    
* Transcript
    
* Selector Polynomial
    
* Vanishing Polynomial
    
* Quadratic Arithmetic Program
    

---

### R1CS

The term "R1CS" stands for "Rank-1 Constraint System." It's a method used in computer science, particularly in the field of cryptography and zero-knowledge proofs. The Rank-1 Constraint System is a way to express a set of arithmetic constraints. It's commonly used in the construction of zero-knowledge proofs, particularly in systems like zk-SNARKs (Zero-Knowledge Succinct Non-Interactive Argument of Knowledge).

An R1CS typically consists of three matrices: A, B, and C. Each row in these matrices represents a single constraint, and the system is satisfied if there exists a vector of variables \\(\vec{x}\\) such that for every row \\( \vec{i}\\), the following equation holds:

$$\vec{a}_i \cdot \vec{x} \times \vec{b}_i \cdot \vec{x} = \vec{c}_i \cdot \vec{x}$$

### Transcript

Trace just means Prover is just going to evaluate this entire circuit and take the values on all the wires. In Interactive proofs, the value of every gate is traced, in Plonk, the trace is defined as the left input, right input, and output of every gate.

QAP: input + output of every **multiplication gate.**

e.g.:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704175742715/75437c93-13e0-4b25-8e79-9d2572d53df7.png align="center")

( image credits to Yupeng Zhang )

### Selector Polynomial

Let's assume a polynomial \\(l_i(x)\\) which means is \\(c_i\\) the left input of gate j, for j = 1,2,3, where \\(c_i\\) is the value in the i'th position in the transcript ( as in the above image ).

e.g.

1. \\(l_1(x) : (1,0,0)\\).  
    The polynomial interpolation at a known set \\(\Omega\\) : \\(l_1(w) = 1, l_1(w^2) = 0, l_1(w^3) = 0\\).
    
2. \\(l_x(x) : (0, 0 ,1)\\)
    
    Polynomial interpolation: \\(l_3(w) = 0, l_3(w^2) = 0, l_w(w^3) = 0\\) since value 1 is the input of the addition gate which in turn is the left input of the multiplication gate ( remember we are taking trace over multiplication gate in case of QAP )
    

**Properties**:

* \\(L(x) = \sum_{i=1}^9 c_i * l_i(x)\\). If you write the trace for the entire transcript in the above example: \\(L(w) = c_1 = 3\\), \\(L(w^3) = c_3 + c_4\\). The values are the 1s that are present at the respective input column in the entire transpose.
    
* Similarly, we calculate \\(r(x)\\) and create a transcript then using that a selector polynomial \\(R(x)\\).
    
* Similarly, we have \\(O(x)\\) which represents the gate outputs.
    

Now we have:

$$p(x) = L(x)R(x) - O(x)$$

* Claim is \\(p(w^j) = 0 , for \ j = 1,2,3..\\)
    

### Vanishing Polynomial

* We define \\(p(x) = V(x)q(x)\\) , where \\(V(x) = (x - w )(x-w^2)(x-w^3)\\) is the vanishing polynomial of the set \\(\Omega = \{ w, w^2, w^3 \}\\)
    

---

Finally:

P claims to know a w such that \\(C(x,w) = y\\) \\(<==>\\)P claims to know that a vector c such that \\(p(x) = V(x)q(x)\\).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704177568238/adcb6845-3242-4456-a19c-811f595338a6.png align="center")

Where the table is a piece of public information, and generated during preprocessing phase.

Now, instead of checking the constraints in the R1CS individually, we can now check *all of the constraints at the same time* by doing the dot product check *on the polynomials*.

---

Lastly, follow me on social media: [Twitter](https://twitter.com/privacy_prophet), and [LinkedIn](https://www.linkedin.com/in/rachit-anand-srivastava/) for new updates in the cryptography space.

---

References:

* [https://risencrypto.github.io/R1CSQAP/#:~:text=The%20next%20step%20is%20taking,vector%20%26%20the%20number%20of%20gates](https://risencrypto.github.io/R1CSQAP/#:~:text=The%20next%20step%20is%20taking,vector%20%26%20the%20number%20of%20gates).
    
* [https://medium.com/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649](https://medium.com/@VitalikButerin/quadratic-arithmetic-programs-from-zero-to-hero-f6d558cea649)
    
* SNARK lecture on linear PCP by Yupeng Zhang [Link](https://www.youtube.com/watch?v=I7TXIHXamwM&list=PLS01nW3Rtgor_yJmQsGBZAg5XM4TSGpPs&index=9&ab_channel=Blockchain-Web3MOOCs)