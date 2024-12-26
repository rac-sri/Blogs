---
title: "Bilinear Parings: Deep Dive Part 1"
seoTitle: "Bilinear Pairings: Deep Dive"
seoDescription: "Deep dive into how bilinear pairing works in respect to Elliptic Curves."
datePublished: Thu Dec 26 2024 10:30:25 GMT+0000 (Coordinated Universal Time)
cuid: cm556nh7b001p09ky8au3e7vk
slug: bilinear-parings-deep-dive-part-1
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1735127129255/8561d502-4c8d-480a-946b-41181c3859dc.webp
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1735164432761/a933b1b7-9c25-498d-b1a7-4a194b450034.webp
tags: blockchain, cryptography, mathematics, kzg-commitment, bilinear-pairings

---

In this blog series we will be digging deep into the working of bilinear pairings in Elliptic curves over finite fields. The following series presumes you have a decent understanding Elliptic Curves:

* Group law - the chord and tangent rule.
    
* Fields and how field operations work.
    
* Identify Element
    
    > You can find resources to learn about the above topic here → [Cryptography 101](https://github.com/rac-sri/cryptography-101)
    

---

## Elliptic Curve Cryptography

### Introduction to Elliptic Curve Cryptography (ECC)

Elliptic Curve Cryptography (ECC) has revolutionised the field of cryptography by enabling secure communication with much smaller key sizes compared to traditional methods like RSA. Its foundation lies in the mathematical properties of elliptic curves, which provide both security and efficiency for modern cryptographic systems.

An elliptic curve is defined by an equation of the form:

$$y^2 =x^3 +ax+b$$

where a and b are constants that satisfy specific conditions to ensure the curve forms a smooth, non-singular shape. The points on this curve, along with a special "point at infinity," form a mathematical structure that can be used for cryptographic operations.

The core idea of ECC relies on the **elliptic curve group** and operations like **point addition** and **scalar multiplication**. These operations underpin the security of ECC-based systems, making it infeasible for attackers to reverse-engineer private keys from public ones due to the computational difficulty of the Elliptic Curve Discrete Logarithm Problem (ECDLP).

### Understanding Point Addition

One of the fundamental operations in ECC is **point addition**, which is the process of adding two points PP and QQ on an elliptic curve to produce another point RR. Depending on whether PP and QQ are distinct or identical, the operation involves different calculations:

* If \\(P != Q\\), a line passing through P and Q intersects the curve at a third point. Reflecting this point across the x-axis gives RR.
    
* If \\(P = Q\\), the operation is called **point doubling**. In this case, a tangent line to the curve at P is used to find the third point, followed by reflection.
    

These operations are not only geometrically intriguing but also computationally efficient, making them suitable for cryptographic systems.

**Bilinear pairings**, which extend the capabilities of ECC, point addition and other fundamental ECC operations play a critical role in setting up the necessary mathematical framework.

$$e(a, b) : G_1 \times G_2 \to G_T$$

### Introduction to Fields in Mathematics

In cryptography, **fields** play a foundational role as they provide the mathematical framework for operations on elliptic curves and other cryptographic structures. A **field** is a set of elements equipped with two operations: addition and multiplication, which satisfy certain properties. These properties ensure that every element (except zero for multiplication) has an inverse, making the operations well-defined and consistent.

Some key characteristics of a field include:

1. **Closure**: The result of adding or multiplying any two elements in the field is also in the field.
    
2. **Associativity and Commutativity**: Both addition and multiplication are associative and commutative.
    
3. **Identity Elements**: The field has an additive identity (0) and a multiplicative identity (1).
    
4. **Inverses**: Every element has an additive inverse, and every non-zero element has a multiplicative inverse.
    

The most common examples of fields are:

* The field of **real numbers**
    
* The field of **rational numbers**
    
* **Finite fields**, also known as Galois fields \\(F_p\\), which are critical in cryptography.
    

In the case of elliptic curve cryptography, we often work over finite fields, such as \\(F_p\\) These fields allow us to define elliptic curves and perform secure, efficient computations, paving the way for robust cryptographic systems.

---

### Let's dive into some in-depth design topics of ECC: *Torsion Points* and *Endomorphism*.

## Torsion Points

Let’s have a quick recall of what point of Infinity is and how it correlates to the order of a Point in ECC:  
The **order of a point** P on an elliptic curve is the smallest positive integer n such that:

\\(nP=P+P+⋯+P=P+P+⋯+P(n times)=O\\)

where O is the **point at infinity**, which acts as the identity element in the group of points on the curve.

* If \\(nP=O\\), then P is said to have an order of n.
    
* For any point P, the order n divides the total number of points on the curve (known as the **curve order**).
    

Now, the group order can be divides into smaller subgroups, based on Lagrange’s theorem.

For example, consider the curve ( \\(E/\mathbb{F}_{101} : y^2 = x^3 + x + 1\\) *).* The # \\(  E/{F}_{101} = 105 = 3 \cdot 5 \cdot 7\\). So we can get a point of order \\(r|105\\) by simply multiplying P by the appropriate cofactor, which is \\(h = Order / r\\). For example to get a point of order 3: \\([35]P\\). By definition a point is “killed” ( sent to O ) when multiplied by its order.

**Any point killed by r over the full closure** \\(E(\overline F_q)\\) **is said to be in the *r-torsion*. (** The **algebraic closure** of a finite field \\(F_q​, denoted \space \overline{{F}_q}\\)​​, is the set of all roots of all polynomials with coefficients in \\(F_q\\)​. It is the "largest possible extension field" of \\(F_q\\) that contains every solution to such polynomials. **)**

By **Hasse's theorem**, the curve order is approximately \\(q+1\\), with a margin of error bounded by \\(2\sqrt{q}​:\\)

$$q+1−2\sqrt{q}≤ Order{ } E(F_q)≤q+1+2\sqrt{q}$$

The offset between # \\(E(F_q)\\) and \\(q+1\\) is called the **trace of Frobenius** and is denoted by t:

$$Order E(F_q) = q+1-t, {\space } where |t| <= 2\sqrt{q}$$

## Endomorphism

### What Is the Endomorphism Ring?

The endomorphism ring \\(End(E)\\) is a collection of all "maps" (functions) that take points on an elliptic curve EE and send them to other points on the same curve, while preserving the group structure. These maps can be thought of as ways to transform the curve while keeping its essential properties intact.

In this ring:

* **Addition of maps** works point wise: \\((ψ_1+ψ_2)(P)=ψ_1(P)+ψ_2(P)\\).
    
* **Multiplication of maps** is done through composition: \\((ψ1∘ψ2)(P)=ψ1(ψ2(P))\\).
    

### Trivial Endomorphisms

Some endomorphisms are always present:

1. The **multiplication-by-m** map, denoted \\([m]\\), scales a point P by \\(m: [m](P)= P + P + \dots + P \space (m \space times)\\).
    
2. For elliptic curves defined over finite fields, there’s an additional map called the **Frobenius endomorphism** \\(\pi\\), which raises the coordinates of a point to the power q.
    
    If \\(E\\) is defined over \\(F_q\\), then the **Frobenius endomorphism** \\(\pi\\) is defined as:
    

$$\pi : E \mapsto E, \space (x,y) \mapsto ( x^q, y^q )$$

### Non-Trivial Endomorphisms

Sometimes, elliptic curves have additional, more interesting endomorphisms. Let’s look at an example:

* Consider the curve \\(E:y^2=x^3+b\\) over a finite field \\(F_q\\).
    
* A special map, \\(ξ\\) is defined as \\(ξ(x,y)=(ξ_3x,y)\\) where \\(ξ_3\\) is a cube root of 1 \\((ξ_3^3=1 \space but \space ξ_3≠1)\\).
    
* Whether \\(ξ\\) is a valid endomorphism depends on the field:
    
    * If \\(ξ_3∈Fq\\) then \\(ξ\\) is defined over \\(F_q\\).
        
    * Otherwise, \\(ξ_3\\) lies in an extended field (e.g., \\(F_{q^2}\\)), meaning \\(ξ\\) operates on a larger space.
        

The **Frobenius endomorphism** \\(\pi\\) is a special type of endomorphism on an elliptic curve E defined over a finite field \\(F_q\\)​. Essentially, the Frobenius endomorphism maps a point \\(P=(x,y)\\) on the elliptic curve E over \\(F_q\\)​ to a new point \\(π(P)=(x^q,y^q)\\). This means it raises the coordinates of the point to the power of q (the size of the finite field), which can be thought of as a sort of "Frobenius twist."

* **Effect on Points**: When the Frobenius map \\(\pi\\) is applied to a point \\(P=(x,y)\\) the new point \\(π(P)=(x^q,y^q)\\) lies on the same elliptic curve, but the point might be in a different field extension, depending on the situation.
    
* **Fixed Points**: One of the important properties of the Frobenius map is that it "fixes" certain points. That is, if P is a **fixed point** of the Frobenius map, then applying the map again doesn't change the point. This happens in particular for points that are in the field \\(F_q\\)​, where raising the coordinates to the power \\(q\\) leaves the point unchanged.
    
* **Mapping Across Extensions**: The Frobenius endomorphism π\\piπ also connects points from \\(E(F_q)\\) (the elliptic curve over \\(F_q\\)​) to points in higher field extensions, such as \\(E(F_{q^2})\\), depending on the specific case. This means that the Frobenius map can take points from \\(E(F_q) \space to \space E(F_{q^2})\\) and beyond, depending on how the curve is defined and the properties of the field.
    

**Example**: Cubic roots of unity will be defined in \\(F_q\\) if and only if \\(q = 1 \space mod \space 3\\)( this is because the multiplicative group of non zero elements in \\(F_q\\)​ is cyclic, and its order is \\(q-1\\). If \\(q≡1 \space (mod \space 3)\\) then \\(q-1\\) is divisible by 3, which ensures that there are elements in the field that satisfy \\(x^3 = 1\\). The **order** of an element \\(g^k\\) in the cyclic group is the smallest integer n such that \\(g^{kn} = 1\\). For \\(x = g^k\\) to satisfy \\(x^3 = 1\\), the order of \\(g^k\\) must divide 3. In a cyclic group, the order of any element divides the total order of the group ) . Let’s take curve equation : \\(y^2 = x^3 +5\\)

* Over \\(F_{19}, ξ_3=7\\) is a valid cube root of 1, so \\( ξ\\) works within \\(F_{19}\\).
    
* Over, \\( F_{23}\\) no cube root of 1 exists in \\(F_{23}\\). We need to extend to \\(F_{23^2}\\) to define \\( ξ\\). So we form quadratic extension \\(F_{q^2}(u), u^2 + 1 = 0\\). Now, \\( ξ_3 = 8u + 11\\) is a valid cube root.
    

**Complex Multiplication (CM):** When an elliptic curve has extra, non-trivial endomorphisms like \\(ξ\\), its endomorphism ring becomes larger than the integers \\(Z\\). This is called **complex multiplication (CM)**.

* All elliptic curves defined over finite fields \\(F_q\\) have CM because the Frobenius endomorphism \\(\pi\\)always adds complexity to \\(End(E)\\).
    
* For curves defined over other fields (like \\(Q\\)), the presence of CM is less common and depends on whether additional maps exist.
    

**Trace of Frobenius** plays role in the characteristic polynomial satisfied by \\(\pi\\) given as:

$$\pi ^2 - [t].\pi + [q] = 0 \space in \space End(E)$$

meaning for all \\((x,y) \in E( \overline{F_q})\\) we have \\((x^{q^2}, y^{q^2}) - [t] ( x^q, y^q) + [q](x,y) = O\\)

---

This brings us to the end of Part 1 of this series. In the next part, we will dig into "Divisors," a concept from Algebraic Geometry, but we'll limit its scope to how it works in Elliptic Curves. Stay tuned!

For more updates, insights, and discussions, follow me on Twitter: [@privacy\_prophet](https://x.com/privacy_prophet).

## References

* [Parings for beginners](https://static1.squarespace.com/static/5fdbb09f31d71c1227082339/t/5ff394720493bd28278889c6/1609798774687/PairingsForBeginners.pdf)
    
* [Pairings by Vitalik](https://medium.com/@VitalikButerin/exploring-elliptic-curve-pairings-c73c1864e627#:~:text=Elliptic%20curve%20pairings%20\(or%20%E2%80%9Cbilinear,curve%2Dbased%20protocols%20can%20do.)