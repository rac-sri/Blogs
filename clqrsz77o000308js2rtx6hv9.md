---
title: "Demystifying KZG Poly-commit Scheme"
seoTitle: "Demystifying KZG Poly-commitment Scheme"
seoDescription: "Understand the working of the famous KZG scheme used widely in the field of Zero Knowledge Cryptography."
datePublished: Sat Dec 30 2023 08:30:45 GMT+0000 (Coordinated Universal Time)
cuid: clqrsz77o000308js2rtx6hv9
slug: demystifying-kzg-poly-commit-scheme
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1703919453646/e91190d2-eb2b-4bb6-b33b-a732b2639bbf.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1703924992617/9f88a1dc-95a7-4fe4-809f-3880387502aa.jpeg
tags: privacy, blockchain, zero-knowledge-proofs, commitment

---

---

A KZG polynomial commitment scheme allows a prover to commit to a polynomial so that they can later reveal and prove properties about the polynomial (like its value at certain points) without revealing the entire polynomial. This is particularly useful in scenarios where the polynomial represents some private data or computation.

Prequistics:

* How c[yclic groups](https://www.di.ens.fr/~fouque/CoursBCS3.pdf) work in Numbers Theory
    
* Discrete Log + [Diffie-Hellman](https://www.nku.edu/~christensen/092mat483%20DH%20key%20exchange.pdf)
    
* Understanding of [Bilinear Pairing](https://twitter.com/RacSri25/status/1738503612094148718)
    

---

### **KZG Poly-commit scheme**

Before we start, let’s see the elements we have:

* Bilinear Group p, G, g, Gₜ, e.
    
* Univariate polynomials: \\(F = F_p^{<=d}[X]\\)
    

**Step 1: Keygen**

* Sample random τ∈Fp
    
* \\(gp = (g, g^{\tau}, g^{\tau^2}, \ldots, g^{\tau^d})\\)
    
* delete \\(\tau\\) ( trusted setup )
    

**Step 2: Commit**

* commit (gp, f) -&gt; \\(com_f\\)
    
* \\(f(x) = f_0 + f_1x + f_2x ^2 + \ldots + f_dx^d\\)
    
* \\(com_f = g^{f(\tau)}\\)  
    \\(= g ^ {f_0 + f_1\tau + f_2\tau^2+ \ldots + f_d^{\tau ^ d}}\\)
    
    \\(= (g)^{f_0}. (g^\tau)^{f_1}. (g^{\tau^2})^{f_2}. \ldots (g^{\tau^d})^{f_d}\\)
    

**Step 3: Proof**

* \\(eval(gp, f, u)  -> v, \pi\\): where \\(u\\) is a random point the verifier chooses.
    
    * \\(f(x) - f(u) = (x - u)q(x)\\) as u is a root of \\(f(x) - f(u)\\)
        
    * Compute \\(q(x) \\) and \\(\pi = g^{q(\tau)}\\) computed using gp elements.
        

**Step 4: Verify**

* \\(verify(gp, com_f, u, v, \pi )\\):
    
    * where \\(v = f(u)\\)
        
    * The idea is to check the equation at point \\(\tau\\): \\(g ^ {f(\tau) - f(u)} = g ^ {(\tau - u)q(\tau)}\\)
        
    * Challenge: only know \\(g ^ {\tau - u}\\) and \\(g ^ {q(\tau)}\\)
        
    * Solution: pairings - \\(e(com_f / g^v, g) = e(g^{\tau - u}, \pi)\\)  
        \\(=e(g,g)^{f(\tau) - f(u)} = e(g,g)^{({\tau - u}). q(\tau)}\\)
        

---

The key features of KZG polynomial commitments include:

1. **Commitment**: The prover commits to a polynomial by computing a commitment, a concise representation of the polynomial. This commitment is computed using a bilinear pairing over elliptic curves, which forms the cryptographic backbone of the scheme.
    
2. **Evaluation Proofs**: The prover can provide proofs that a committed polynomial evaluates to a certain value at a specific point. These proofs are small in size and can be verified quickly.
    
3. **Hiding Property**: The commitment does not reveal the polynomial itself, preserving the privacy of the data.
    
4. **Efficiency**: KZG commitments are highly efficient in terms of both computation and communication overhead, making them suitable for use in blockchain networks where resources are limited.
    
5. **Applications**: They are widely used in the construction of zk-SNARKs (zero-knowledge Succinct Non-interactive ARguments of Knowledge) and other zero-knowledge proof systems. In blockchain, they enable scalable and private transactions.
    

---

### Achieving Zero-Knowledge

The plain KZG is not ZK. Eg. \\(com_f = g ^{f(\tau)}\\) is deterministic.

To achieve Zero Knowledge, we mask the commitment with randomizers.

* Commit: \\(com_f = g^{f(\tau) + r\eta}\\)
    
* Evaluate: ( where \\(r\\) and \\(r^{'}\\) are both randomizers )
    
    $$f(x) + ry - f(u) = (x - u)(q(x) + r^{'}y) + y(r - r^{'}(x - u))$$
    

$$\pi = g ^ {q(\tau) + r^{'}\eta} , g^{r-r^{'} (\tau - u)}$$

---

**Other Variants Of KZG**

1. **Batch opening: Single Polynomial:**
    

* * Prover wants to prove \\(f\\) at \\(u_1,\ldots, u_m \\) for m&lt;d.
        
        * Steps:
            
            * Extrapolate \\(f(u_1),\ldots,f(u_m)\\) to get \\(h(x)\\)
                
            * \\(f(x) - h(x) = \prod_{i=1}^m(x-u_i)q(x)\\). Why? Check the roots of polynomials on LHS.
                
            * \\(\pi = g ^ {q(\tau)}\\)
                
            * \\(e ( com_f / g^ {h(\tau)}, g) = e(g^{\prod_{i=1}^m(\tau - u_i)}, \pi)\\) where \\(g^{h(r)}\\) verifier can calculate using the global params.
                

1. **Batch opening: Multiple Polynomials**
    
    * Prover wants to prove \\(f_i(u_{i,j}) = v_{i,j}\\)
        
    * Steps:
        
        * Extrapolate \\(f_i(u_1),\ldots,f(u_m)\\) to get \\(h_i(x)\\) for \\(i \in [n]\\)
            
        * \\(f_i(x) - h_i(x) = \prod_{i=1}^m(x - u_m)q_i(m)\\)
            
        * Combine all \\(q_i(x)\\) via a random linear combination.
            

---

And that's it !!!

Lastly, follow me on social media: [Twitter](https://twitter.com/privacy_prophet), and [LinkedIn](https://www.linkedin.com/in/rachit-anand-srivastava/) for new updates in the cryptography space.