# Report: Experimenting with Shor’s Algorithm to Break RSA

## **Overview**

This report presents and compares three progressively complex implementations of **Shor’s algorithm**, developed and tested to explore its structure, feasibility, and performance on IBM’s quantum hardware.  
The three implementations are:

1. **Simplified Shor** – a conceptual version for educational purposes.  
2. **Real QPE Shor** – a hardware-oriented implementation using Quantum Phase Estimation (QPE).  
3. **Advanced Shor** – a fully explicit reconstruction including modular exponentiation via matrix operators.

Each implementation aims to reveal a different layer of Shor’s algorithm — from intuition, to experimental application, to theoretical completeness.

---

## **Experimental Context**

All experiments were conducted using IBM Quantum backends via the **Qiskit Runtime Service**.  
Classical post-processing steps (e.g., computing the GCDs) were used to verify candidate factors.  
Quantum results were analyzed through their measurement distributions, phase reconstruction, and factor recovery attempts.

---

## **Implementation Comparison**

| Aspect | Simplified Shor | Real QPE Shor | Advanced Shor |
|---------|------------------|----------------|----------------|
| **Core Idea** | Demonstrates the high-level structure of Shor (Hadamard + QFT) without modular arithmetic | Implements full Quantum Phase Estimation and modular exponentiation black-box (`Uf(qpe)`) | Explicitly builds the modular exponentiation operator \( U_a^{2^k} \bmod N \) as a quantum matrix |
| **Quantum Components** | Only Hadamard + QFT | Controlled modular exponentiation (opaque) + inverse QFT | Controlled modular exponentiation (explicit matrix) + inverse QFT |
| **Fidelity to Original Algorithm** | Low (conceptual) | High (operational) | Very high (theoretically faithful) |
| **Scalability** | High (light circuits, runs easily on hardware) | Moderate (limited by depth and qubit count) | Very low (matrix-based, exponential in qubit number) |
| **Classical Analysis** | Approximate — mock estimation of period | Full — retrieves denominator and computes GCDs | Complete — derives \( r \) from phase fractions and applies GCD checks |
| **Usability on IBM Backends** | Works reliably for all \( N \), but only demonstrates structure | Works for small \( N \) (≤ 21), fails for larger due to circuit depth | Works for very small \( N \) (< 60), infeasible beyond that |
| **Output Distribution** | Flat or uniform (no physical interference) | Structured for small \( N \), random for large \( N \) | Flat for large \( N \), coherent peaks visible only in simulation |
| **Educational Value** | Excellent for teaching the principles | Good balance between realism and accessibility | Ideal for illustrating full mathematical correctness |
| **Goal** | Conceptual demonstration | Experimental validation | Theoretical reconstruction |

---

## **Key Findings**

### **Quantum vs. Classical Performance**
- For **very small moduli (e.g., 15, 21)**, all three implementations successfully recovered non-trivial factors when simulated locally.
- For **larger composites (e.g., 851)**, the **quantum interference pattern collapsed**, yielding flat measurement distributions.
- Classical post-processing still correctly handled GCD computation, but the **quantum improvement was lost** due to noise and circuit depth.

### **Hardware Observations**
- On real IBM backends (e.g., *ibm_fez* or *ibm_torino*), circuits with more than ~20 qubits suffered **high decoherence** and **gate errors**, producing effectively random results.
- Even with advanced transpilation (optimization level 3), the **controlled-U gates** dominated the circuit depth.
- The **inverse QFT** proved especially fragile: it amplified small phase errors, flattening the final distribution.

---

## **Hardware Limitations**

| Limitation | Impact |
|-------------|---------|
| **Qubit coherence time** | Phase information lost before measurement |
| **Gate error accumulation** | Interference patterns destroyed |
| **Circuit depth (Uf + QFT)** | Too long for current devices |
| **Limited connectivity** | Additional SWAP gates increase error rate |

IBM’s quantum hardware can currently handle **RSA-like moduli up to 48 bits** at best, under ideal conditions.  

---

## **Interpretation of Flat Distributions**

When the measurement histogram shows a **flat distribution**, it means:
- The quantum system lost coherence, and  
- The QPE stage failed to encode the periodicity of \( a^x \bmod N \).  

This behavior offers **no advantage over classical factoring** — the results are indistinguishable from random sampling.  
However, it **confirms that the full quantum workflow executes**, validating circuit design and illustrating the gap between *theoretical algorithms* and *current quantum hardware capabilities*.

---

## **Conclusions**

- The **Simplified Shor** effectively introduces the logic of quantum factoring but lacks physical realism.  
- The **Real QPE Shor** bridges theory and practice, showing how period finding is implemented on hardware.  
- The **Advanced Shor** captures the full mathematical depth of the algorithm but exposes the scalability limits of current architectures.  


The experiment demonstrates that Shor’s algorithm can be *constructed, executed, and analyzed* with modern quantum frameworks, even if the hardware cannot yet break RSA keys at meaningful scales.
