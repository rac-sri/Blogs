---
title: "FRI Polynomial Commitment Scheme"
seoTitle: "FRI Polynomial Commitment"
seoDescription: "The FRI (Fast Reed-Solomon Interactive Oracle) commitment scheme"
datePublished: Tue Jan 02 2024 10:30:10 GMT+0000 (Coordinated Universal Time)
cuid: clqw7kbre000208l36nkpezh3
slug: fri-polynomial-commitment-scheme
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1704043643535/cd86fe3e-2fcb-4c60-8bb6-a6bc57a579e9.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1704049701786/92c90a9f-0169-42a8-b6b1-ac772b753325.jpeg
tags: blockchain, cryptography, zero-knowledge-proofs, snarks

---

The FRI (Fast Reed-Solomon Interactive Oracle) commitment scheme is a cryptographic protocol that plays a crucial role in various zero-knowledge proof systems. It's designed to efficiently prove the correctness of computations without revealing any underlying data. The FRI scheme is particularly important in the context of zk-STARKs (Zero-Knowledge Scalable Transparent Arguments of Knowledge), which are a type of non-interactive cryptographic proof that's scalable and doesn't require a trusted setup.

### Problem addressed using FRI:

1. **Want P time linear in degree, not field size**:
    
    * Let's say we use a Merkle tree for commitment to values in a field f. But this is very inefficient since if field f is big, then the number of leaves, and consequently the proof length grows significantly. In FRI, rather than P merkle-commiting to all (p-1) evaluations of q, P merkle-commits to evaluations of q(x) for \\(x \in \Omega\\) of \\(F_p\\).
        
    * \\(\Omega\\) has size \\(\rho ^ {-1} k\\) where \\(\rho < 1/2\\) constant and k is the degree of q.
        
    * Proof length will be about \\(\lambda / log(\rho ^ {-1}). log^2(k)\\) hash values where \\(\lambda\\) is the security parameter, aka. " \\(\lambda\\) bits of security ".
        
    * Let \\(\omega \in F_p\\), n is the smallest integer such that \\(\omega ^ n = 1\\), then \\(\Omega = \{ 1, \omega, \omega ^2, \ldots, \omega ^ {n-1} \}\\)
        
    * From group theory, \\(\Omega\\) has size n if and only if n divides p-1.
        
        * Hence, FRI based SNARKs work over fields like \\(F_p\\) with \\(p = 2^{64}   - 2^{32} + 1\\) , p-1 is divisible by \\(2^{32}\\). Running FRI over the field can support any power of 2 value of n up to \\(2^{32}\\).
            
    * For eg. FRI commitment to univariate polynomial \\(q(x)\\) in \\(F_{41}[X]\\) when \\(8 = \rho^{-1}.k\\), where roots of unity = \\(\{ 1, -1, 9, -9, 3, -3, 14, -14 \}\\) becomes the root of the committed merkle tree.
        

> To visualize roots of unity try this tool -&gt; [https://www.geogebra.org/m/sZFwAZfs](https://www.geogebra.org/m/sZFwAZfs)

1. **V needs to know the committed vector at all evaluations over domain \\(\Omega\\) of some (k-1) degree polynomial.**
    
    * V "inspects" a few entries of the vector to "get a sense" of whether its low-degree. This will add a Merkle authentication path ( log(n) hash values ) to the proof.
        
    * This is impractical. FRI's test will be interactive. We use a "folding phase" followed by a "query phase". The folding phase is log(k) rounds. The query phase is one round.
        

---

### Folding Phase ( Interactive )

#### **Step 1: Starting with a High-Degree Polynomial**

* The process begins with the prover having a high-degree polynomial. This polynomial is a representation of the computation or data they intend to prove something about.
    

#### **Step 2: Interpolation and Evaluation**

* The polynomial is evaluated at various points. These points typically lie in a domain related to a specific subgroup of a finite field.
    

#### **Step 3: The Folding Process**

* The prover reduces the degree of the polynomial. They select a subset of the evaluation points and merge the values of the polynomial at these points using a linear or affine transformation.
    
* V picks up a random field element r, and r is used to "randomly combine" every two paired-up entries.
    
* q(x) is split into "even and odd components" -&gt; \\(q(x) = q_e(X^2) + Xq_o(X^2)\\)
    
* The prescribed "folding" q is \\(q_{fold}(Z) = q_e(Z) + rq_o(Z)\\) where degree of \\(q_{fold}\\) is half of the degree of q.
    

#### **Step 4: Recursive Application**

* **What Happens**: After folding, the polynomial's degree is lowered. The prover then commits to this new, reduced-degree polynomial. Folding is repeated until the degree falls to 0.
    
* **Why It Matters**: This recursive process is essential for gradually simplifying the polynomial.
    

**Step 5: End**

* The length of the folded vector after the degree has fallen to 0 is still \\(\rho ^ {-1} >= 2\\). Since the degree should be 0, P can specify the folder vector with a single field element.
    

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1704046170064/d04decfe-23c9-4354-8056-73d631ca8f88.png align="center")

( image credit: Justin Thaler lecture ( link in references ).

> * The calculation in the picture uses the fact that if x and -x are n'th roots of unity and z = \\(x^2\\). Then \\(q_{fold}(z) = \frac{(r+x)}{2x} .q(x) + \frac{(r-x)}{-2x} .q(-x) \\) ( derivation not covered in this post ).
>     
> * FRI heavily uses the fact that the map \\(x -> x^2\\) is 2 to 1 on \\(\Omega\\), ensuring that the relevant domain halves in size with each fold.
>     

---

### Query Phase ( Interactive )

* P may have "lied":
    
    * sending a vector that is not the prescribed folding of the previous vector
        
    * To "artificially" reduce the degree of the claimed folded vector.
        
* The Query phase is where the verifier tries to detect inconsistencies.
    
* V picks about \\(\lambda / log ( \rho ^ {-1}) \\) entries of each folded vector and confirming each is the prescribed linear combination of the relevant two entries of the previous vector.
    
* Proof length ( and V time ): roughly \\((\lambda / log(\rho ^ {-1})_. log(k)^2\\) hash evaluations. Each of the folded vectors ( log(k) ) is queried at \\(\lambda / log ( \rho ^ {-1})\\)
    

---

> * For security analysis, refer the links in references. This article focuses on the working only.
>     
> * There is a known attack where prover folders a polynomial s rather than q, where s agrees on a set T = \\(\rho n\\) elements of \\(\Omega\\).
>     

---

### Polynomial Commitment using FRI

* Problems with FRI
    
    * P has only the Merkle-committed to evaluations of q over domain \\(\Omega\\), not the whole field.
        
    * V only knows that q is "not too far" from low-degree, not exactly low-degree.
        
* Solution
    
    * We know \\(q(X) - v = w(X)(X-r)\\) is equivalent to \\(q(r) = v\\) ( refer [here](https://hashnode.com/edit/clqrsz77o000308js2rtx6hv9)). w is a polynomial of degree at most d.
        
    * So to confirm \\(q(r) = v\\) V applies FRI's fold + query procedure to the functions \\((q(X) - v)(X-r)^{-1}\\) using degree bound d-1. Whenever the FRI verifier queries this function at \\(\Omega\\), evaluation can be obtained with one query to q at the same point.
        
    * V doesn't know that q is exactly a low degree, but to pass V's check, \\(v\\) has to equal \\(h(r)\\) where h is the degree d polynomial that is closest to q.
        

> Each FRI verifies query brings &lt;1 bit of security to the commitment scheme. FRI today is used as a weaker primitive tha a polynomial commitment ( list polynomial commitment scheme ), which suffices for SNARK security. P is bound to a "small set" of low-degree polynomials rather than to a single one.

---

**Conclusion:** The FRI commitment scheme represents a significant advancement in the field of cryptographic proofs, enabling more secure and efficient verification of computations in various applications, including blockchain technology and privacy-preserving computations.

Lastly, follow me on social media: [Twitter](https://twitter.com/privacy_prophet), and [LinkedIn](https://www.linkedin.com/in/rachit-anand-srivastava/) for new updates in the cryptography space.

---

**References**:

1. [https://blog.lambdaclass.com/how-to-code-fri-from-scratch/](https://blog.lambdaclass.com/how-to-code-fri-from-scratch/)
    
2. Lecture by Justin Thaler [Link](https://www.youtube.com/watch?v=A3edAQDPnDY&list=PLS01nW3Rtgor_yJmQsGBZAg5XM4TSGpPs&index=8&ab_channel=Blockchain-Web3MOOCs)
    
3. [https://chat.openai.com/](https://chat.openai.com/)
    
4. [https://eprint.iacr.org/2019/1020.pdf](https://eprint.iacr.org/2019/1020.pdf)
    
5. [https://aszepieniec.github.io/stark-anatomy/fri.html](https://aszepieniec.github.io/stark-anatomy/fri.html)