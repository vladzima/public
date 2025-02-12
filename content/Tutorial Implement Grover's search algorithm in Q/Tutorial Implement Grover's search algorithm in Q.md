---
aliases:
  - Tutorial Implement Grover's search algorithm in Q
title: Tutorial Implement Grover's search algorithm in Q
source: https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-grovers-search?tabs=tabid-copilot
created: 2025-01-24
tags:
  - clippings
  - knowledge
notebookllm: https://notebooklm.google.com/notebook/eb704ec9-f586-480a-a2ce-4191067cc8b2
linter-yaml-title-alias: Tutorial Implement Grover's search algorithm in Q
date created: Friday, January 24th 2025, 12:46:57 pm
date modified: Sunday, January 26th 2025, 6:09:13 pm
---

```folder-overview
id: bd5174d2-7ea5-40bf-a144-fc203a9ac420
folderPath: ""
title: "{{folderName}} overview"
showTitle: false
depth: 3
includeTypes:
  - folder
  - markdown
style: list
disableFileTag: false
sortBy: name
sortByAsc: true
showEmptyFolders: false
onlyIncludeSubfolders: false
storeFolderCondition: true
showFolderNotes: false
disableCollapseIcon: true
```

---

<audio src="/audio/Implementing Grover's Algorithm in Q#.wav" controls>
<p>Fallback content goes here.</p>
</audio>

## Tutorial: Implement Grover's search Algorithm in Q#

- Article
- 01/13/2025

## In This Article

1. [Prerequisites](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#prerequisites)
2. [Define the problem](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#define-the-problem)
3. [The Grover's algorithm](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#the-grovers-algorithm)
4. [Write the code for Grover's algorithm in Q#](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#write-the-code-for-grovers-algorithm-in-q)
5. [Run the final code](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#run-the-final-code)
6. [Run the program](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#run-the-program)
7. [Related content](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#related-content)

In this tutorial, you implement Grover's algorithm in Q# to solve search-based problems. For an in-depth explanation of the theory behind Grover's algorithm, see the [Theory of Grover's algorithm](https://learn.microsoft.com/en-us/azure/quantum/concepts-grovers).

In this tutorial, you:

- Define the Grover's algorithm for a search problem
- Implement Grover's algorithm in Q#

Tip

If you want to accelerate your quantum computing journey, check out [Code with Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding), a unique feature of the [Azure Quantum website](https://quantum.microsoft.com/). Here, you can run built-in Q# samples or your own Q# programs, generate new Q# code from your prompts, open and run your code in [VS Code for the Web](https://vscode.dev/quantum) with one click, and ask Copilot any questions about quantum computing.## Prerequisites

- To run the code sample in the [Copilot in Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding):
- A Microsoft (MSA) email account.
- To develop and run the code sample in Visual Studio Code:
- The latest version of [Visual Studio Code](https://code.visualstudio.com/download) or open [VS Code on the Web](https://vscode.dev/quantum).
- The latest version of the [Azure Quantum Development Kit extension](https://marketplace.visualstudio.com/items?itemName=quantum.qsharp-lang-vscode). For installation details, see [Set up the QDK extension](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk).## Define the problem

Grover's algorithm is one of the most famous algorithms in quantum computing. The type of problem it solves is often referred to as "searching a database", but it's more accurate to think of it in terms of the *search problem*.

Any search problem can be mathematically formulated with an abstract function $f \left(\right. x \left.\right)$ that accepts search items $x$. If the item $x$ is a solution to the search problem, then $f \left(\right. x \left.\right) = 1$. If the item $x$ isn't a solution, then $f \left(\right. x \left.\right) = 0$. The search problem consists of finding any item $x_{0}$ such that $f \left(\right. x_{0} \left.\right) = 1$.

Thus, you can formulate the any search problem as: given a classical function $f \left(\right. x \left.\right): \left{\right. 0, 1 \left(\left.\right}\right)^{n} \rightarrow \left{\right. 0, 1 \left.\right}$, where $n$ is the bit-size of the search space, find an input $x_{0}$ for which $f \left(\right. x_{0} \left.\right) = 1$.

To implement Grover's algorithm to solve a search problem, you need to:

1. Transform the problem to the form of a **Grover's task**. For example, suppose you want to find the factors of an integer $M$ using Grover's algorithm. You can transform the integer factorization problem to a Grover's task by creating a function

$$
f_{M} \left(\right. x \left.\right) = 1 \left[\right. r \left]\right. ,
$$

 where $1 \left[\right. r \left]\right. = 1$ if $r = 0$ and $1 \left[\right. r \left]\right. = 0$ if $r \neq 0$ and $r$ is the remainder of $M / x$. This way, the integers $x_{i}$ that make $f_{M} \left(\right. x_{i} \left.\right) = 1$ are the factors of $M$ and you have transformed the problem to a Grover's task.

2. Implement the function of the Grover's task as a quantum oracle. To implement Grover's algorithm, you need to implement the function $f \left(\right. x \left.\right)$ of your Grover's task as a [quantum oracle](https://learn.microsoft.com/en-us/azure/quantum/concepts-oracles).
3. Use Grover's algorithm with your oracle to solve the task. Once you have a quantum oracle, you can plug it into your Grover's algorithm implementation to solve the problem and interpret the output.## The Grover's algorithm

Suppose there are $N = 2^{n}$ eligible items for the search problem and they are indexed by assigning each item an integer from $0$ to $N - 1$. The steps of the algorithm are:

1. Start with a register of $n$ qubits initialized in the state $\left|\right. 0 \rangle$.
2. Prepare the register into a uniform superposition by applying $H$ to each qubit in the register:

$$
\left|\right. \psi \rangle = \frac{1}{N^{1 / 2}} \sum_{x = 0}^{N - 1} \left|\right. x \rangle
$$

3. Apply the following operations to the register $N_{\text{optimal}}$ times:
4. The phase oracle $O_{f}$ that applies a conditional phase shift of $- 1$ for the solution items.
5. Apply $H$ to each qubit in the register.
6. Apply $- O_{0}$, a conditional phase shift of $- 1$ to every computational basis state except $\left|\right. 0 \rangle$.
7. Apply $H$ to each qubit in the register.
8. Measure the register to obtain the index of an item that's a solution with very high probability.
9. Check the item to see if it's a valid solution. If not, start again.## Write the code for Grover's algorithm in Q#

This section discusses how to implement the algorithm in Q#. There are few things to consider when implementing Grover's algorithm. You need to define what is your marked state, how to reflect about it, and how many iterations to run the algorithm for. You also need to define the oracle that implements the function of the Grover's task.### Define the marked state

First, you define what input you are trying to find in the search. To do so, write an operation that applies the steps **b**, **c** and **d** from the [Grover's algorithm](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#the-grovers-algorithm).

Together, these steps are also known as the **Grover'S diffusion operator** $- H^{\bigotimes n} O_{0} H^{\bigotimes n}$.

```qsharp
operation ReflectAboutMarked(inputQubits : Qubit[]) : Unit {
    Message("Reflecting about marked state...");
    use outputQubit = Qubit();
    within {
        // We initialize the outputQubit to (|0⟩ - |1⟩) / √2, so that
        // toggling it results in a (-1) phase.
        X(outputQubit);
        H(outputQubit);
        // Flip the outputQubit for marked states.
        // Here, we get the state with alternating 0s and 1s by using the X
        // operation on every other qubit.
        for q in inputQubits[...2...] {
            X(q);
        }
    } apply {
        Controlled X(inputQubits, outputQubit);
    }
}
```

The `ReflectAboutMarked` operation reflects about the basis state marked by alternating zeros and ones. It does so by applying the Grover's diffusion operator to the input qubits. The operation uses an auxiliary qubit, `outputQubit`, which is initialized in the state $\left|\right. - \rangle = \frac{1}{\sqrt{2}} \left(\right. \left|\right. 0 \rangle - \left|\right. 1 \rangle \left.\right)$ by applying the $X$ and $H$ gates. The operation then applies the $X$ gate to every other qubit in the register, which flips the state of the qubit. Finally, it applies the controlled $X$ gate to the auxiliary qubit and the input qubits. This operation flips the auxiliary qubit if and only if all the input qubits are in the state $\left|\right. 1 \rangle$, which is the marked state.### Define the number of optimal iterations

Grover's search has an optimal number of iterations that yields the highest probability of measuring a valid output. If the problem has $N = 2^{n}$ possible eligible items, and $M$ of them are solutions to the problem, the optimal number of iterations is:

$$
N_{\text{optimal}} \approx \frac{\pi}{4} \sqrt{\frac{N}{M}}
$$

Continuing to iterate past the optimal number of iterations starts reducing that probability until you reach nearly-zero success probability on iteration $2 N_{\text{optimal}}$. After that, the probability grows again until $3 N_{\text{optimal}}$, and so on.

In practical applications, you don't usually know how many solutions your problem has before you solve it. An efficient strategy to handle this issue is to "guess" the number of solutions $M$ by progressively increasing the guess in powers of two (i.e. $1, 2, 4, 8, 16, …, 2^{n}$). One of these guesses will be close enough that the algorithm will still find the solution with an average number of iterations around $\sqrt{\frac{N}{M}}$.

The following Q# function calculates the optimal number of iterations for a given number of qubits in a register.

```qsharp
function CalculateOptimalIterations(nQubits : Int) : Int {
    if nQubits > 63 {
        fail "This sample supports at most 63 qubits.";
    }
    let nItems = 1 <<< nQubits; // 2^nQubits
    let angle = ArcSin(1. / Sqrt(IntAsDouble(nItems)));
    let iterations = Round(0.25 * PI() / angle - 0.5);
    return iterations;
}
```

The `CalculateOptimalIterations` function uses the formula above to calculate the number of iterations, and then rounds it to the nearest integer.### Define the Grover's operation

The Q# operation for Grover's search algorithm has three inputs:

- The number of qubits, `nQubits: Int`, in the qubit register. This register will encode the tentative solution to the search problem. After the operation, it will be measured.
- The number of optimal iterations, `iterations: Int`.
- An operation, `phaseOracle: Qubit[] => Unit): Result[]`, that represents the phase oracle for the Grover's task. This operation applies an unitary transformation over a generic qubit register.

```qsharp
operation GroverSearch( nQubits : Int, iterations : Int, phaseOracle : Qubit[] => Unit) : Result[] {

    use qubits = Qubit[nQubits];
    PrepareUniform(qubits);

    for _ in 1..iterations {
        phaseOracle(qubits);
        ReflectAboutUniform(qubits);
    }

    // Measure and return the answer.
    return MResetEachZ(qubits);
}
```

The `GroverSearch` operation initializes a register of $n$ qubits in the state $\left|\right. 0 \rangle$, prepares the register into a uniform superposition, and then applies the Grover's algorithm for the specified number of iterations. The search itself consists of repeatedly reflecting about the marked state and the start state, which you can write out in Q# as a for loop. Finally, it measures the register and returns the result.

The code makes use of three helper operations: `PrepareUniform`, `ReflectAboutUniform`, and `ReflectAboutAllOnes`.

Given a register in the all-zeros state, the `PrepareUniform` operation prepares a uniform superposition over all basis states.

```qsharp
operation PrepareUniform(inputQubits : Qubit[]) : Unit is Adj + Ctl {
    for q in inputQubits {
        H(q);
    }
}
```

The \`\`ReflectAboutAllOnes\` operation reflects about the all-ones state.

```qsharp
operation ReflectAboutAllOnes(inputQubits : Qubit[]) : Unit {
    Controlled Z(Most(inputQubits), Tail(inputQubits));
}
```

The operation `ReflectAboutUniform` reflects about the uniform superposition state. First, it transforms the uniform superposition to all-zero. Then, it transforms the all-zero state to all-ones. Finally, it reflects about the all-ones state. The operation is called `ReflectAboutUniform` because it can be geometrically interpreted as a reflection in the vector space about the uniform superposition state.

```qsharp
operation ReflectAboutUniform(inputQubits : Qubit[]) : Unit {
    within {
        Adjoint PrepareUniform(inputQubits);
        // Transform the all-zero state to all-ones
        for q in inputQubits {
            X(q);
        }
    } apply {
        ReflectAboutAllOnes(inputQubits);
    }
}
```

## Run the Final Code

Now you have all the ingredients to implement a particular instance of Grover's search algorithm and solve the factoring problem. To finish, the `Main` operation sets up the problem by specifying the number of qubits and the number of iterations

```qsharp
operation Main() : Result[] {
    let nQubits = 5;
    let iterations = CalculateOptimalIterations(nQubits);
    Message($"Number of iterations: {iterations}");
    
    // Use Grover's algorithm to find a particular marked state.
    let results = GroverSearch(nQubits, iterations, ReflectAboutMarked);
    return results;
}
```

## Run the Program

Select the desired platform to run your program.

- [Copilot in Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#tabpanel_1_tabid-copilot)
- [Visual Studio Code](https://learn.microsoft.com/en-us/azure/quantum/?tabs=tabid-copilot#tabpanel_1_tabid-vscode)

You can test your Q# code with the Copilot in Azure Quantum free of charge - all you need is a Microsoft (MSA) email account. For more information about the Copilot in Azure Quantum, see [Explore Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/get-started-azure-quantum).

1. Open the [Copilot in Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding) in your browser.
2. Copy and paste the following code into the code editor.

```qsharp
import Microsoft.Quantum.Convert.*;
import Microsoft.Quantum.Math.*;
import Microsoft.Quantum.Arrays.*;
import Microsoft.Quantum.Measurement.*;
import Microsoft.Quantum.Diagnostics.*;

operation Main() : Result[] {
    let nQubits = 5;
    let iterations = CalculateOptimalIterations(nQubits);
    Message($"Number of iterations: {iterations}");

    // Use Grover's algorithm to find a particular marked state.
    let results = GroverSearch(nQubits, iterations, ReflectAboutMarked);
    return results;
}

operation GroverSearch(
    nQubits : Int,
    iterations : Int,
    phaseOracle : Qubit[] => Unit) : Result[] {

    use qubits = Qubit[nQubits];

    PrepareUniform(qubits);

    for _ in 1..iterations {
        phaseOracle(qubits);
        ReflectAboutUniform(qubits);
    }

    // Measure and return the answer.
    return MResetEachZ(qubits);
}

function CalculateOptimalIterations(nQubits : Int) : Int {
    if nQubits > 63 {
        fail "This sample supports at most 63 qubits.";
    }
    let nItems = 1 <<< nQubits; // 2^nQubits
    let angle = ArcSin(1. / Sqrt(IntAsDouble(nItems)));
    let iterations = Round(0.25 * PI() / angle - 0.5);
    return iterations;
}

operation ReflectAboutMarked(inputQubits : Qubit[]) : Unit {
    Message("Reflecting about marked state...");
    use outputQubit = Qubit();
    within {
        // We initialize the outputQubit to (|0⟩ - |1⟩) / √2, so that
        // toggling it results in a (-1) phase.
        X(outputQubit);
        H(outputQubit);
        // Flip the outputQubit for marked states.
        // Here, we get the state with alternating 0s and 1s by using the X
        // operation on every other qubit.
        for q in inputQubits[...2...] {
            X(q);
        }
    } apply {
        Controlled X(inputQubits, outputQubit);
    }
}

operation PrepareUniform(inputQubits : Qubit[]) : Unit is Adj + Ctl {
    for q in inputQubits {
        H(q);
    }
}

operation ReflectAboutAllOnes(inputQubits : Qubit[]) : Unit {
    Controlled Z(Most(inputQubits), Tail(inputQubits));
}

operation ReflectAboutUniform(inputQubits : Qubit[]) : Unit {
    within {
        // Transform the uniform superposition to all-zero.
        Adjoint PrepareUniform(inputQubits);
        // Transform the all-zero state to all-ones
        for q in inputQubits {
            X(q);
        }
    } apply {
        // Now that we've transformed the uniform superposition to the
        // all-ones state, reflect about the all-ones state, then let the
        // within/apply block transform us back.
        ReflectAboutAllOnes(inputQubits);
    }
}
```

Tip

From Copilot in Azure Quantum, you can open your program in [VS Code for the Web](https://vscode.dev/quantum) by selecting the VS Code logo button in the right-hand corner of the code editor.### Run the program using the in-memory simulator

1. Select **In-memory Simulator**.
2. Select the number of shots to run, and select **Run**.
3. The results are displayed in the histogram and in the **Results** fields.
4. Select **Explain code** to prompt Copilot to explain the code to you.### Run the program using the Quantinuum Emulator

You can also submit your program to the free [Quantinuum Emulator](https://learn.microsoft.com/en-us/azure/quantum/provider-quantinuum#quantinuum-emulator-cloud-based). The emulator simulates a quantum computer with 20 qubits.

1. Select the **In-Memory Simulator** dropdown and select **Quantinuum Emulator**.
2. Select the number of shots (currently limited to 20) and select Run.
3. Open Visual Studio Code and select **File > New Text File** to create a new file.
4. Save the file as `GroversAlgorithm.qs`. This file will contain the Q# code for your program.
5. Copy the following code into the `GroversAlgorithm.qs` file.

```qsharp
import Microsoft.Quantum.Convert.*;
import Microsoft.Quantum.Math.*;
import Microsoft.Quantum.Arrays.*;
import Microsoft.Quantum.Measurement.*;
import Microsoft.Quantum.Diagnostics.*;

operation Main() : Result[] {
    let nQubits = 5;
    let iterations = CalculateOptimalIterations(nQubits);
    Message($"Number of iterations: {iterations}");

    // Use Grover's algorithm to find a particular marked state.
    let results = GroverSearch(nQubits, iterations, ReflectAboutMarked);
    return results;
}

operation GroverSearch(
    nQubits : Int,
    iterations : Int,
    phaseOracle : Qubit[] => Unit) : Result[] {

    use qubits = Qubit[nQubits];

    PrepareUniform(qubits);

    for _ in 1..iterations {
        phaseOracle(qubits);
        ReflectAboutUniform(qubits);
    }

    // Measure and return the answer.
    return MResetEachZ(qubits);
}

function CalculateOptimalIterations(nQubits : Int) : Int {
    if nQubits > 63 {
        fail "This sample supports at most 63 qubits.";
    }
    let nItems = 1 <<< nQubits; // 2^nQubits
    let angle = ArcSin(1. / Sqrt(IntAsDouble(nItems)));
    let iterations = Round(0.25 * PI() / angle - 0.5);
    return iterations;
}

operation ReflectAboutMarked(inputQubits : Qubit[]) : Unit {
    Message("Reflecting about marked state...");
    use outputQubit = Qubit();
    within {
        // We initialize the outputQubit to (|0⟩ - |1⟩) / √2, so that
        // toggling it results in a (-1) phase.
        X(outputQubit);
        H(outputQubit);
        // Flip the outputQubit for marked states.
        // Here, we get the state with alternating 0s and 1s by using the X
        // operation on every other qubit.
        for q in inputQubits[...2...] {
            X(q);
        }
    } apply {
        Controlled X(inputQubits, outputQubit);
    }
}

operation PrepareUniform(inputQubits : Qubit[]) : Unit is Adj + Ctl {
    for q in inputQubits {
        H(q);
    }
}

operation ReflectAboutAllOnes(inputQubits : Qubit[]) : Unit {
    Controlled Z(Most(inputQubits), Tail(inputQubits));
}

operation ReflectAboutUniform(inputQubits : Qubit[]) : Unit {
    within {
        // Transform the uniform superposition to all-zero.
        Adjoint PrepareUniform(inputQubits);
        // Transform the all-zero state to all-ones
        for q in inputQubits {
            X(q);
        }
    } apply {
        // Now that we've transformed the uniform superposition to the
        // all-ones state, reflect about the all-ones state, then let the
        // within/apply block transform us back.
        ReflectAboutAllOnes(inputQubits);
    }
}
```

4. Before running the program, ensure the target profile is set to **Unrestricted**. Select **View -> Command Palette**, search for QIR, select **Q#: Set the Azure Quantum QIR target profile**, and then select **Q#: unrestricted**.
5. To run your program, select **Run** from the list of commands above the `Main` operation, or press **Ctrl+F5**. By default, the compiler runs the `Main` operation or function on the default simulator.
6. Your output will appear in the debug console in the terminal.

Note

If the target profile is not set to **Unrestricted**, you will get an error when you run the program.## Related content

Explore other Q# tutorials:

- [Quantum entanglement](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-explore-entanglement) shows how to write a Q# program that manipulates and measures qubits and demonstrates the effects of superposition and entanglement.
- [Quantum random number generator](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-quantum-random-number-generator) shows how to write a Q# program that generates random numbers out of qubits in superposition.
- [Quantum Fourier Transform](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-qubit-level-program) explores how to write a Q# program that directly addresses specific qubits.
- The [Quantum Katas](https://quantum.microsoft.com/tools/quantum-katas) are self-paced tutorials and programming exercises aimed at teaching the elements of quantum computing and Q# programming at the same time.

---

## Feedback

## Additional Resources

---

Training

---

Documentation

- [Tutorial: Quantum Fourier Transform in Q\\# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-qubit-level-program?source=recommendations)

In this tutorial, learn how to write and simulate a quantum program that operates at the individual qubit level.

- [Tutorial: Quantum Entanglement with Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-explore-entanglement?source=recommendations)

In this tutorial, write a quantum program in Q# that demonstrates the superposition and entanglement of qubits.

- [Tutorial: Create a Quantum Random Number Generator - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-quantum-random-number-generator?source=recommendations)

Build a Q# project that demonstrates fundamental quantum concepts like superposition by creating a quantum random number generator.

- [Quickstart: Create a Q# Program - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/qsharp-quickstart?source=recommendations)

This article explains how to create your first Q# program using the Quantum Development Kit and Visual Studio Code.

- [Q# features in Visual Studio Code - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/vscode-qsharp-reference?source=recommendations)

Learn about the features that are included with the Azure Quantum Development Kit extension for VS Code.

- [Introduction to the Quantum Programming Language Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/qsharp-overview?source=recommendations)

This article introduces Q#, a programming language for developing and running quantum algorithms, and the structure of a Q# program.

- [Develop and Manage Q# Projects and Custom Libraries - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/how-to-work-with-qsharp-projects?source=recommendations)

Learn how to define a Q# project that uses multiple source files, and use your projects as reusable custom libraries.

- [Submit Q# Programs with VS Code - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/how-to-submit-jobs?source=recommendations)

This document provides a basic guide to submit and run Azure Quantum using the Azure portal, Python, Jupyter Notebooks, or the Azure CLI.

### In This Article
