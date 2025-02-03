---
title: "Bilinear Pairing: Deep Dive Part 3"
seoTitle: "Bilinear Pairing: Deep Dive"
seoDescription: "Deep dive into how Bilinear Pairings work under the hood."
datePublished: Sun Feb 02 2025 08:30:23 GMT+0000 (Coordinated Universal Time)
cuid: cm6nd3hlq000b09kvhm7v62ok
slug: bilinear-pairing-deep-dive-part-3
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1738441138446/aaf3a141-801a-40d3-bd0f-9b03edb39272.webp
tags: cryptography, kzg-commitment, bilinear-pairings

---

So far we covered:

* Group Law
    
* Fields and how field operations works
    
* Divisors
    

Now in this part we will explore:

* Pairing Groups
    
* Expand on r-torsian groups
    
* Pairing types
    
* Twisted Curves
    
* Weil and Tate Pairings
    

## Pairing groups

Let’s define bilinear map first:

$$M*M \rightarrow R$$

where bilinearity property of the map means:

$$( x+y, z) = (x,z) * (y,z )$$

$$(x,y+z)= (x,y)*(x,z)$$

The most common notion for the same is \\(e: G_1 * G_2 \rightarrow G_T\\)

\\(G_1\\) and \\(G_2\\) are defined in \\(E(F_{q^k})\\) and target group \\(G_T\\) is defined in multiplicative group \\(F^*_{q^k}\\), so we write \\(G_1\\) and \\(G_2\\) additively and \\(G_T\\) multiplicatively. Thus if \\(P,P' \in G_1\\) and \\(Q, Q' \in G_2\\) the bilinearity of e means:

$$e(P+P', Q) = e(P,Q).e(P',Q)$$

$$e(P, Q+Q') = e(P,Q).e(P,Q')$$

from which it follows, for scalers \\(a, b \in Z\\):

$$e([a]P,[b]Q) = e(P,[b]Q)^a = e([a]P, Q)^b = e(P,Q)^{ab} = e([b]P,[a]Q)$$

**Non-Degeneracy**: Non-degeneracy is a fundamental property of cryptographic pairings. It ensures that if \\(P \neq O\\) and \\(Q \neq O\\), then the pairing \\(e(P,Q) \neq 1\\). This means that the pairing does not collapse information by mapping all non-trivial inputs to the identity element. In a cryptographic setting, non-degeneracy guarantees that pairings can be used effectively in protocols like identity-based encryption and signature schemes. Essentially, it ensures that the bilinear mapping retains meaningful structure and does not trivialize computations. This \\(e(P,Q) \neq 1 \\) guarantees non-trivial pairings for non-trivial arguments.

### The r-torsion

The points \\(P\\) and \\(Q\\) come from disjoin cyclic subgroups of the same prime order \\(r\\). We obtain two distinct order-r subgroups in general: we find the smallest extension \\(F_{q^k} \space of \space F_q\\) such that \\(E(F_{q^k})\\) captures more points of order \\(r\\). The integer \\(k \geq 1\\) that achieves this is called embedding degree and plays a crucial role in paring computation.

**Trace Map:** The trace map of point \\(P = (x,y) \in E(F_{q^k})\\) is defined as:

$$Tr(P) = \sum_{\sigma\in Gal(F_{q^k} / F_q)} \sigma(P) = \sum^{k-1}{i=0} \pi^i(P) = \sum^{k-1}{i=0}(x^{q^i}, y^{q^i})$$

Galois theory: \\(Tr: E(F_{q^k}) \rightarrow E(F_q)\\) so when \\(r || Order E(F_q)\\), this map, which is actually a group homomorphism, sends all torsion points into one subgroup of the r-torsion i.e Tr will send every other element in the torsion into \\(E(F_q)[r]\\).

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738438516581/7945a2fa-81a9-4af1-83fe-43ee0694978f.png align="center")

* All points other than the upper left torsion group one, will get mapped to the upper left group. eg. \\(R = (u^{423}, u^{840})\\), we have \\(Tr(R) = (10, 7)\\).
    
* There is one other peculiar subgroup above in E\[7\], the upper right group in the above case, where the trace map sends every element to \\(O\\). eg. \\(T = (u^{1315}, u^{1150})\\) i.e. \\(Tr(T) = O\\). This is the **trace zero** group.
    
* We can also map any \\(P \in E[r]\\) to the trace zero subgroup ( \\(G_2)\\) via the anti-trace map \\(aTr: P \rightarrow P' = [k]P - Tr(P)\\).
    
    To following diagram ( from Pairings for beginners book ) summarize the above quite well:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738438821140/24f82754-bce8-47d5-b33c-cef355bb9208.png align="center")
    
    * Lastly, we have *supersingular* curve E if \\(Order E(F_q) = q+1\\). These curves come with a **distortion map** \\(\phi\\): a non-\\(F_q\\)\-rational map that takes a point in \\(E(F_q)\\) to a point in \\(E(F_{q^k})\\).
        
        For \\(E/ F_q = y^2 = x^3 +1 \\) with embedding degree \\(k=2\\) we get:
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738439284060/c463140e-5fe1-4453-bd0b-213c59834b08.png align="center")

And that covers the different torsion groups and mappings between them !iv

---

## Pairing Types

Pairing-based cryptography leverages structured subgroups of elliptic curves to facilitate secure and efficient protocols, with four distinct pairing types offering varied trade-offs between functionality, efficiency, and security. These classifications—Type 1 through Type 4—arise from how subgroups like the base-field group \\(G_1\\) and trace-zero subgroup \\(G_2\\) are defined, along with their interplay in operations such as hashing, isomorphism mapping, and pairing computations.

**Type 1 pairings** are built on supersingular elliptic curves, where \\(G_1\\)and \\(G_2\\) are set to the same subgroup. This symmetry simplifies hashing into \\(G_1\\) via cofactor multiplication and ensures a straightforward isomorphism \\(\psi\\) between subgroups. However, the reliance on supersingular curves imposes significant limitations on optimizing pairing computations, making Type 1 less practical for modern applications despite its simplicity.

**Type 2 pairings** use ordinary curves, with \\(G_2\\) defined as a subgroup outside \\(G_1\\) or the trace-zero group. While the trace map \\(Tr: G_2 \rightarrow G_1\\) enables certain efficiency gains, these pairings struggle with a critical drawback: the inability to hash or randomly sample elements directly into \\(G_2\\). This limits their utility in protocols requiring dynamic element generation, as elements must instead be derived from a predefined generator.

**Type 3 pairings**, also on ordinary curves, address this by setting \\(G_2\\) as the trace-zero subgroup. This allows efficient hashing into \\(G_2\\)using the anti-trace map \\(aTr\\), making them a popular choice for modern protocols like BLS signatures. The trade-off, however, is the lack of a computationally feasible isomorphism \\(\psi: G_2 \rightarrow G_1\\), complicating security proofs that depend on such mappings unless stronger assumptions are made.

**Type 4 pairings** expand \\(G_2\\) to the full r-torsion group \\(E[r]\\), theoretically enabling hashing but at the cost of practicality. The non-cyclic structure of \\(E[r]\\) and the negligible probability of hashing into desired subgroups render this type largely theoretical, with limited real-world applications.

In practice, Type 3 pairings dominate modern use cases due to their balance of efficiency and flexibility, while Type 1’s constraints and Type 2/4’s impracticalities relegate them to niche roles. Developers must weigh factors like curve type (supersingular vs. ordinary), isomorphism availability, and hashing capabilities when selecting a pairing type, as these choices directly impact protocol security, performance, and ease of implementation. Ultimately, understanding these trade-offs is key to designing robust cryptographic systems tailored to specific needs.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1738439739994/4434abf4-d08c-4321-9a79-ca4fa2793f45.png align="center")

---

## Twisted Curves

Twisted curves play a pivotal role in modern elliptic curve cryptography (ECC) and pairing-based protocols, offering enhanced efficiency, security, and flexibility. These curves—such as **twisted Edwards curves** and **twisted Montgomery curves**—are algebraic variants of standard elliptic curves, engineered to optimize cryptographic operations while mitigating vulnerabilities. Below, we break down their significance, mechanics, and applications in cryptography.

---

**What Are Twisted Curves?**

A **twist** of an elliptic curve is a related curve that is *isomorphic* (structurally similar) over an extension field but distinct over the base field. For example:

* **Twisted Edwards curves** are defined by the equation \\(ax^2 + y^2 = 1 + dx^2y^2\\), where parameters *a* and \\(d\\) introduce flexibility in curve design.
    
* **Montgomery curves** like \\(y^2 = x^3 + Ax^2 + x\\) (e.g., Curve25519) are often birationally equivalent to twisted Edwards curves, enabling efficient computations.
    

Twists retain the core security properties of their parent curves but enable optimizations in arithmetic operations. For instance, twisted Edwards curves support **complete** and **exception-free** addition formulas, eliminating edge cases that could lead to side-channel attacks.

---

**Why Use Twisted Curves?**

1. **Efficiency**:  
    Twisted curves streamline critical operations like point addition and scalar multiplication. For example, twisted Edwards curves achieve point addition in 8M (8 field multiplications), significantly faster than Weierstrass curves. This speed is leveraged in schemes like **EdDSA**, a high-performance digital signature protocol.
    
    * Curve25519 (a Montgomery curve) and its twisted Edwards counterpart, **edwards25519**, underpin secure communication protocols like Signal and TLS 1.3.
        
2. **Pairing Optimization**:  
    In pairing-based cryptography, twists enable computations over smaller extension fields. For example, certain twists of pairing-friendly curves (e.g., BN curves) allow pairings to be computed in \\(\mathbb{F}_{q^k}\\) with reduced *k*, slashing computational overhead. This is critical for protocols like BLS signatures and identity-based encryption.
    
3. **Security**:  
    Twists must be **twist-secure**—meaning both the original curve and its twist resist attacks like the discrete logarithm problem. If a protocol fails to validate input points, an attacker could force computations on a weaker twist, compromising security. For example, Curve25519’s twist, Curve448, is designed to match its parent’s security.
    

---

### **Twists in Pairing-Based Cryptography**

Pairing protocols (e.g., Type 3 pairings) often use twists to optimize subgroup structures:

* **Trace-Zero Subgroups**: Twists can map elements to trace-zero subgroups \\(G_2\\), enabling efficient hashing and cofactor multiplication.
    
* **Field Extensions**: By leveraging twists, pairings like the Tate or Ate pairing reduce the embedding degree *k*, accelerating operations in \\(\mathbb{F}_{q^k} \\) .
    

For instance, the **anti-trace map** \\(\text{aTr} \\) on twisted Edwards curves allows direct hashing into \\(G_2\\), a key advantage in Type 3 pairings.

---

## Weil and Tate Pairings

**Weil Pairings:** Let \\(P,Q \in E(F_{q^k})[r] \\) and let \\(D_P\\) and \\(D_Q\\) be degree zero divisors with disjoin support such that \\(D_P \sim (P) - (O) \\) and \\(D_Q \sim (Q) - (O)\\). There exist functions \\(f\\) and \\(g\\) such that \\((f) = rD_P \\) and \\((g) = rD_Q\\). The Weil pairings \\(w_r\\) is a map:

$$w_r: E(F_{q^k})[r] * E(F_{q^k})[r] \rightarrow \mu_{r }$$

defined as:

$$w_r(P,Q) = f(D_{Q}) / g(D_P)$$

**Tate Pairings:** Let \\(P  \in E(F_{q^k})[r]\\) from which it follows that there is a function \\(f\\) whose divisor is \\((f) = r(P) - r(O) \\) . Let \\(Q \in E(F_{q^k})\\) be any representative in any equivalance class in \\(E(F_{q^k}) / rE(F_{q^k})\\)and let \\(D_Q\\) be a degree zero divisor defined over \\(F_{q^k}\\) that is equivalent to \\((Q) - (O)\\), but whose support is disjoint to that of \\((f)\\). The Tate pairing \\(t_r\\) is a map:

$$t_r : E(F_{q^k})[r] E(F_{q^k}) / rE(F_{q^k}) \rightarrow F^{q^k} / (F^*{q^k})^r$$

defined as:

$$t_r(P,Q) = f(D_Q)$$

---

### Conclusion

From group laws defining elliptic curves to the nuanced interplay of pairing types and twisted geometries, pairing-based cryptography bridges theoretical depth and practical innovation. As zero-knowledge systems and decentralized finance demand stronger privacy guarantees, these mathematical tools will remain indispensable—provided developers prioritize twist-secure parameters, optimal subgroup choices, and rigorous hardness assumptions. The future lies in balancing efficiency gains with quantum resilience, ensuring pairings evolve alongside cryptographic frontiers.

For more updates, insights, and discussions, connect with me on [**Twitter**](https://x.com/privacy_prophet) and [**LinkedIn**](https://www.linkedin.com/in/rachit-anand-srivastava/).

## References

* [Parings for beginners](https://static1.squarespace.com/static/5fdbb09f31d71c1227082339/t/5ff394720493bd28278889c6/1609798774687/PairingsForBeginners.pdf)