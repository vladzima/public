---
aliases:
  - Quickstart Create your first Q program
title: Quickstart Create your first Q program
source: https://learn.microsoft.com/en-us/azure/quantum/qsharp-quickstart
created: 2025-01-24
tags:
  - clippings
  - knowledge
category: LLM workspace
linter-yaml-title-alias: Quickstart Create your first Q program
date created: Friday, January 24th 2025, 12:05:12 pm
date modified: Sunday, January 26th 2025, 5:28:45 pm
notebookllm: https://notebooklm.google.com/notebook/748adcd9-408d-4a87-a713-107ea9c428cc?authuser=1
---
```folder-overview
id: 5df4e85e-50ec-47d5-b218-4d170d5a0972
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

<audio src="/audio/Quantum Entanglement with Q#.wav" controls></audio>

## Quickstart: Create Your First Q# Program

- Article
- 10/29/2024

## In This Article

1. [Prerequisites](https://learn.microsoft.com/en-us/azure/quantum/#prerequisites)
2. [Create a Q# file](https://learn.microsoft.com/en-us/azure/quantum/#create-a-q-file)
3. [Write your Q# code](https://learn.microsoft.com/en-us/azure/quantum/#write-your-q-code)
4. [Run your Q# code](https://learn.microsoft.com/en-us/azure/quantum/#run-your-q-code)
5. [Next step](https://learn.microsoft.com/en-us/azure/quantum/#next-step)

Learn how to write a basic Q# program that demonstrates entanglement, a key concept of quantum computing.

When two or more [qubits](https://learn.microsoft.com/en-us/azure/quantum/concepts-the-qubit) are entangled, they share quantum information, which means whatever happens to one qubit also happens to the other. In this quickstart, you create a particular two-qubit entangled state called a Bell pair. In a Bell pair, if you measure one qubit in the $\left|\right. 0 \rangle$ state, you know the other qubit is also in the $\left|\right. 0 \rangle$ state without measuring it. For more information, see [Quantum entanglement](https://learn.microsoft.com/en-us/azure/quantum/concepts-entanglement).

In this quickstart, you:

- Create a Q# file.
- Allocate a pair of qubits.
- Entangle the qubits.
- ## Prerequisites
- The latest version of [Visual Studio Code](https://code.visualstudio.com/download).
- The [Azure Quantum Development Kit (QDK) extension](https://marketplace.visualstudio.com/items?itemName=quantum.qsharp-lang-vscode). For installation details, see [Set up the Quantum Development Kit](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk).## Create a Q# file

1. Open Visual Studio Code.
2. Select **File** > **New Text File**.
3. Save the file as `Main.qs`. The.qs extension denotes a Q# program.## Write your Q# code

In your `Main.qs` file, follow these steps to entangle and measure a pair of qubits.### Import a quantum library

The QDK includes the Q# standard library with predefined functions and operations for your quantum programs. To use them, you must first import the relevant library.

In your program, use an `import` statement to open the `Microsoft.Quantum.Diagnostics` library. This gives you access to all its functions and operations, including `DumpMachine()`, which you later use to display the entangled state.

```qsharp
    import Microsoft.Quantum.Diagnostics.*;
```

### Define an Operation

After importing the relevant libraries, define your quantum operation and its input and output values. For this quickstart, your operation is `Main`. This is where you'll write the remaining Q# code to allocate, manipulate, and measure two qubits.

`Main` takes no parameters and returns two `Result` values, either `Zero` or `One`, which represent the results of the qubit measurements:

```qsharp
    operation Main() : (Result, Result) {
        // Your entanglement code goes here.
}
```

### Allocate Two Qubits

The `Main` operation is currently empty, so the next step is to allocate two qubits, `q1` and `q2`. In Q#, you allocate qubits using the `use` keyword:

```qsharp
        // Allocate two qubits, q1 and q2, in the 0 state.
        use (q1, q2) = (Qubit(), Qubit());
```

Note

In Q#, qubits are always allocated in the $\left|\right. 0 \rangle$ state.### Put one qubit into superposition

The qubits `q1` and `q2` are in the $\left|\right. 0 \rangle$ state. To prepare the qubits for entanglement, you must put one of them into an even superposition, where it has a 50% chance of being measured as $\left|\right. 0 \rangle$ or $\left|\right. 1 \rangle$.

You put a qubit into superposition by applying the *Hadamard*, `H`, operation:

```qsharp
        // Put q1 into an even superposition.
        H(q1);
```

The resulting state of `q1` is $\frac{1}{\sqrt{2}} \left(\right. \left|\right. 0 \rangle + \left|\right. 1 \rangle \left.\right)$, which is an even superposition of $\left|\right. 0 \rangle$ and $\left|\right. 1 \rangle$.

### Entangle the Qubits

You're now ready to entangle the qubits using the controlled-NOT, `CNOT`, operation. `CNOT` is a control operation that takes two qubits, one acting as the control and the other as the target.

For this quickstart, you set `q1` as the control qubit and `q2` as the target qubit. This means `CNOT` flips the state of `q2` when the state of `q1` is $\left|\right. 1 \rangle$.

```qsharp
        // Entangle q1 and q2, making q2 depend on q1.
        CNOT(q1, q2);
```

The resulting state of both qubits is the Bell pair $\frac{1}{\sqrt{2}} \left(\right. \left|\right. 00 \rangle + \left|\right. 11 \rangle \left.\right)$.### Display the entangled state

Before measuring the qubits, it's important to verify that your previous code successfully entangles them. You can use the `DumpMachine` operation, which is part of the `Microsoft.Quantum.Diagnostics` library, to output the current state of your Q# program:

```qsharp
        // Show the entangled state of the qubits.
        DumpMachine();
```

### Measure the Qubits

Now that you verified the qubits are entangled, you can use the `M` operation to measure them. Measuring `q1` and `q2` collapses their quantum states into `Zero` or `One` with even probability.

In Q#, you use the `let` keyword to declare a new variable. To store the measurement results of `q1` and `q2`, declare the variables `m1` and `m2`, respectively:

```qsharp
        // Measure q1 and q2 and store the results in m1 and m2.
        let (m1, m2) = (M(q1), M(q2));
```

### Reset the Qubits

Before being released at the end of each Q# program, qubits must be in the $\left|\right. 0 \rangle$ state. You do this using the `Reset` operation:

```qsharp
        // Reset q1 and q2 to the 0 state.
        Reset(q1);
        Reset(q2);
```

### Return the Measurement Results

Finally, to complete the `Main` operation and observe the entangled state, return the measurement results of `m1` and `m2`:

```qsharp
        // Return the measurement results.
        return (m1, m2);
```

Tip

If you want to learn more about a Q# function or operation, hover over it.

![Screenshot of the details that appear when you hover the 'H' operation in Visual Studio Code.](https://learn.microsoft.com/en-us/azure/quantum/media/qsharp-quickstart-hover.png)## Run your Q# code

Congratulations! You wrote a Q# program that entangles two qubits and creates a Bell pair.

Your final Q# program should look like this:

```qsharp
import Microsoft.Quantum.Diagnostics.*;

operation Main() : (Result, Result) {  
    // Allocate two qubits, q1 and q2, in the 0 state.
    use (q1, q2) = (Qubit(), Qubit());
    
    // Put q1 into an even superposition.
    // It now has a 50% chance of being measured as 0 or 1.
    H(q1);
    
    // Entangle q1 and q2, making q2 depend on q1.
    CNOT(q1, q2);
    
    // Show the entangled state of the qubits.
    DumpMachine();
    
    // Measure q1 and q2 and store the results in m1 and m2.
    let (m1, m2) = (M(q1), M(q2));
    
    // Reset q1 and q2 to the 0 state.
    Reset(q1);
    Reset(q2);
    
    // Return the measurement results.
    return (m1, m2);
}
```

To run your program and view the result of both qubits, select **Run** above the `Main` operation or press **Ctrl+F5**

![Screenshot of the Q# file in Visual Studio Code showing where to find the 'Run' command.](https://learn.microsoft.com/en-us/azure/quantum/media/qsharp-quickstart-run.png)

You can run the program several times, each with a different result in the debug console. This demonstrates the probabilistic nature of quantum measurements and the entanglement of the qubits.

For example, if the result is `Zero`, your debug console should look like this:

```result
DumpMachine:

 Basis | Amplitude      | Probability | Phase
 -----------------------------------------------
  |00⟩ |  0.7071+0.0000𝑖 |    50.0000% |   0.0000
  |11⟩ |  0.7071+0.0000𝑖 |    50.0000% |   0.0000

Result: "(Zero, Zero)"
```

## Next step

To learn more about quantum entanglement with Q#, see [Tutorial: Explore quantum entanglement with Q#](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-explore-entanglement). This tutorial expands on the concepts covered in this quickstart and helps you write a more advanced entanglement program.

---

## Feedback

## Additional Resources

---

Training

---

Documentation

- [Set up the Quantum Development Kit Extension - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk?source=recommendations)

Learn how to set up the Azure Quantum Development Kit VS Code extension and set up your environment for different languages and platforms.

- [Tutorial: Create a Quantum Random Number Generator - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-quantum-random-number-generator?source=recommendations)

Build a Q# project that demonstrates fundamental quantum concepts like superposition by creating a quantum random number generator.

- [Tutorial: Quantum Entanglement with Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-explore-entanglement?source=recommendations)

In this tutorial, write a quantum program in Q# that demonstrates the superposition and entanglement of qubits.

- [Development Options for Quantum Programming with Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/qsharp-ways-to-work?source=recommendations)

This article describes the environment options for developing quantum programs with Q# and the Quantum Development Kit.

- [Introduction to the Quantum Programming Language Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/qsharp-overview?source=recommendations)

This article introduces Q#, a programming language for developing and running quantum algorithms, and the structure of a Q# program.

- [Submit Q# Programs with VS Code - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/how-to-submit-jobs?source=recommendations)

This document provides a basic guide to submit and run Azure Quantum using the Azure portal, Python, Jupyter Notebooks, or the Azure CLI.

- [Tutorial: Quantum Fourier Transform in Q\\# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-qubit-level-program?source=recommendations)

In this tutorial, learn how to write and simulate a quantum program that operates at the individual qubit level.

- [Develop and Manage Q# Projects and Custom Libraries - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/how-to-work-with-qsharp-projects?source=recommendations)

Learn how to define a Q# project that uses multiple source files, and use your projects as reusable custom libraries.

### In This Article
