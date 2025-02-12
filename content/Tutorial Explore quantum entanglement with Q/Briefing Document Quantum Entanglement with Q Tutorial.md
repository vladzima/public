---
aliases:
  - Briefing Document Quantum Entanglement with Q Tutorial
title: Briefing Document Quantum Entanglement with Q Tutorial
source: https://notebooklm.google.com/notebook/9d86aa66-a8c0-4b0c-b8d9-d250885b4af4?pli=1&authuser=1
created: 2025-01-26
description: Use the power of AI for quick summarization and note taking, NotebookLM is your powerful virtual research assistant rooted in information you can trust.
tags:
  - clippings
  - knowledge
linter-yaml-title-alias: Briefing Document Quantum Entanglement with Q Tutorial
date created: Sunday, January 26th 2025, 5:40:10 pm
date modified: Sunday, January 26th 2025, 5:43:52 pm
---

Okay, here's a detailed briefing document summarizing the provided Microsoft Learn tutorial on quantum entanglement with Q#:

**Briefing Document: Quantum Entanglement with Q# Tutorial**

**Date:** October 26, 2024

**Source:** "Tutorial: Quantum Entanglement with Q# - Azure Quantum | Microsoft Learn" (Accessed December 18, 2024)

**Overview:**

This tutorial provides a hands-on introduction to key concepts in quantum computing, specifically superposition and entanglement, using the Q# programming language and the Azure Quantum platform. The tutorial guides users through building a Q# program step-by-step, explaining the fundamental principles of qubit manipulation and measurement along the way.

**Key Concepts:**

1. **Qubits and Superposition:**

- **Classical bits** hold a single binary value (0 or 1). In contrast, a **qubit** can exist in a *superposition* of both 0 and 1 simultaneously. Each state has a probability amplitude associated with it.
- The act of *measuring* a qubit forces it to collapse out of superposition into a definite 0 or 1 state. This is a probabilistic process, and the outcome is not predetermined.
- The tutorial uses the **Hadamard gate (H)** to put a qubit into superposition, creating roughly equal probabilities of measuring a 0 or 1. The tutorial says, "the H operation flips the qubit *halfway* into a state of equal probabilities of Zero or One."

1. **Quantum Entanglement:**

- Entangled qubits are interconnected such that their states are interdependent. They cannot be described independently. "That is, whatever happens to one qubit in an entangled pair also happens to the other qubit."
- The tutorial demonstrates entanglement using the **CNOT (Controlled-NOT)** gate. The CNOT gate flips the state of the target qubit (second qubit) if the control qubit (first qubit) is in the state 1 after the Hadamard gate is applied.

1. **Q# Operations and Measurement:**

- **SetQubitState:** A Q# operation to initialize a qubit to a known state (0 or 1).
- It uses the M operation to measure the qubit's state and the X operation to flip the qubit state if the measurement does not match the desired value, which makes it set the qubit to a certain state every time it's called.
- The X operation flips the state of a qubit from 0 to 1 and vice versa.
- Code Snippet:

```
operation SetQubitState(desired: Result, target: Qubit): Unit {
 if desired!= M(target) {
 X(target);
 }
 }
 ```

 **Main:** The main operation where the program logic is defined. It demonstrates:
 Allocation and deallocation of qubits using use (q1, q2) = (Qubit(), Qubit());
 Iterating a measurement process, for example:

```
for test in 1..count {
 SetQubitState(initial, q1);
 SetQubitState(Zero, q2);
 // measure each qubit
 let resultQ1 = M(q1);
 let resultQ2 = M(q2);
 // Count the number of 'Ones' returned:
 if resultQ1 == One {
 numOnesQ1 += 1;
 }
 if resultQ2 == One {
 numOnesQ2 += 1;
 }
 }
```

- Measurement of qubits using M(qubit).
- Using the Message function to display results in the console.
- **H (Hadamard):** Puts a qubit into superposition.
- **CNOT:** Entangles two qubits (flips the second qubit's state if the first qubit is 1).

**Tutorial Walkthrough:**

1. **Qubit Initialization:** The tutorial starts by showing how to initialize a qubit to a classical state using the SetQubitState operation. It sets the first qubit to 1 and the second to 0, and measures them to confirm their states, repeatedly.
2. **Superposition:** It introduces the Hadamard gate (H) to create a superposition state in the first qubit. The results of the measurement now show that the first qubit returns 0 roughly 50% of the time and 1 roughly 50% of the time.
3. **Entanglement:** The tutorial adds the CNOT gate to entangle the two qubits after putting the first qubit into superposition. This results in both qubits having the same measurement result: either both will measure 0 or both will measure 1, with an approximately 50% chance of each result. The tutorial notes that "the measurement results for the second qubit are **always** the same as the measurement of the first qubit."
4. **Running Code:** The tutorial demonstrates how to run the code using two methods.

- The tutorial runs the code directly in the Copilot for Azure Quantum. The Copilot provides a convenient way to test code using a web browser.
- The tutorial also explains how to run the same code using Visual Studio Code. The tutorial provides clear instructions for setting up the environment, creating a new Q# file, and running the code within VS Code after setting the correct QIR target profile.

1. **Histogram Visualization:** The tutorial introduces the Q# histogram window, which helps visualize the frequency distribution of measurement results and shows how the various measurement results are distributed and entangled.

**Code Snippets:**

- The SetQubitState operation that ensures that a qubit has a certain state.

```
operation SetQubitState(desired: Result, target: Qubit): Unit {
    if desired!= M(target) {
        X(target);
    }
}

operation Main(): (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = Zero;
    use (q1, q2) = (Qubit(), Qubit());
    for test in 1..count {
        SetQubitState(initial, q1);
        SetQubitState(Zero, q2);
        H(q1);
        CNOT(q1, q2);
        let resultQ1 = M(q1);
        let resultQ2 = M(q2);
        if resultQ1 == One {
            numOnesQ1 += 1;
        }
        if resultQ2 == One {
            numOnesQ2 += 1;
        }
    }
    SetQubitState(Zero, q1);
    SetQubitState(Zero, q2);
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2);
}
```

**Key Takeaways:**

- Qubits can exist in a superposition of 0 and 1.
- Quantum operations like H and CNOT are used to manipulate qubit states.
- Entanglement is a fundamental property of quantum mechanics, where qubits become interconnected and their states correlated.
- Q# provides a way to write quantum programs that can be executed on quantum computers (or simulators).

**Target Audience:**

This tutorial is intended for developers and learners who are new to quantum computing and the Q# programming language. It is designed to be hands-on and provide practical experience with fundamental quantum concepts.

**Potential Next Steps (as suggested in the tutorial):**

- Explore other Q# tutorials, such as those on Grover's algorithm and the Quantum Fourier Transform.
- Dive into the Quantum Katas for self-paced learning.

This briefing document provides a comprehensive overview of the provided Q# tutorial. The user should be able to utilize this brief to quickly understand the content of the document.
