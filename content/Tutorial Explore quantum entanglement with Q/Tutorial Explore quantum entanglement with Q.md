---
aliases:
  - Tutorial Explore quantum entanglement with Q
title: Tutorial Explore quantum entanglement with Q
source: https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-explore-entanglement?pivots=ide-azure-copilot
created: 2025-01-24
tags:
  - clippings
  - knowledge
linter-yaml-title-alias: Tutorial Explore quantum entanglement with Q
date created: Friday, January 24th 2025, 12:15:43 pm
date modified: Sunday, January 26th 2025, 5:36:52 pm
notebookllm: https://notebooklm.google.com/notebook/9d86aa66-a8c0-4b0c-b8d9-d250885b4af4?authuser=1
---
```folder-overview
id: 2ebcaefb-5068-4417-9d4c-eaa87dceba1b
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

<audio src="/audio/Tutorial Quantum Entanglement with Q#.wav" controls></audio>

## Tutorial: Explore Quantum Entanglement with Q#

- Article
- 12/18/2024

## In This Article

1. [Prerequisites](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#prerequisites)
2. [Initialize a qubit to a known state](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#initialize-a-qubit-to-a-known-state)
3. [Write a test operation to test the Bell state](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#write-a-test-operation-to-test-the-bell-state)
4. [Run the code in the Copilot for Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#run-the-code-in-the-copilot-for-azure-quantum)
5. [Put a qubit in superposition](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#put-a-qubit-in-superposition)
6. [Entangle two qubits](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#entangle-two-qubits)
7. [Related content](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#related-content)

In this tutorial, you write a Q# program that manipulates and measures qubits and demonstrates the effects of superposition and entanglement. You prepare two qubits in a specific quantum state, learn how to operate on qubits with Q# to change their state, and demonstrate the effects of superposition and entanglement. You build your Q# program piece-by-piece to introduce qubit states, operations, and measurements.

Here are some key concepts to understand before you begin:

- Where classical bits hold a single binary value such as a 0 or 1, the state of a qubit can be in a superposition of two quantum states, 0 and 1. Each possible quantum state has an associated probability amplitude.
- The act of measuring a qubit produces a binary result with a certain probability, and changes the state of the qubit out of superposition.
- Multiple qubits can be entangled such that they can't be described independently from each other. That is, whatever happens to one qubit in an entangled pair also happens to the other qubit.

In this tutorial, you'll learn how to:

- Create Q# operations to initialize a qubit to a desired state.
- Put a qubit in superposition.
- Entangle a pair of qubits.
- Measure a qubit and observe the results.

Tip

If you want to accelerate your quantum computing journey, check out [Code with Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding), a unique feature of the [Azure Quantum website](https://quantum.microsoft.com/). Here, you can run built-in Q# samples or your own Q# programs, generate new Q# code from your prompts, open and run your code in [VS Code for the Web](https://vscode.dev/quantum) with one click, and ask Copilot any questions about quantum computing.## Prerequisites

To run the code sample in the [Copilot for Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding), you need:

- A Microsoft (MSA) email account.

For more information about the Copilot, see [Explore Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/get-started-azure-quantum).## Initialize a qubit to a known state

The first step is to define a Q# operation that initializes a qubit to a known state. This operation can be called to set a qubit to a classical state, meaning that, when measured, it either returns `Zero` 100% of the time or returns `One` 100% of the time. Measuring a qubit returns a Q# type `Result`, which can only have a value of `Zero` or `One`.

Open the [Copilot for Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding) and copy the following code into the code editor window. Don't select **Run** yet; you'll run the code later in the tutorial.

```qsharp
import Microsoft.Quantum.Intrinsic.*;
import Microsoft.Quantum.Canon.*;

operation SetQubitState(desired : Result, target : Qubit) : Unit {
    if desired != M(target) {
        X(target);
    }
}
```

The code example introduces two standard operations, `M` and `X`, which transform the state of a qubit.

The `SetQubitState` operation:

1. Takes two parameters: a type [`Result`](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/#available-types), named `desired`, that represents the desired state for the qubit to be in (`Zero` or `One`), and a type [`Qubit`](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/#available-types).
2. Performs a measurement operation, `M`, which measures the state of the qubit (`Zero` or `One`) and compares the result to the value specified in `desired`.
3. If the measurement does not match the compared value, it runs an `X` operation, which flips the state of the qubit to where the probabilities of a measurement returning `Zero` and `One` are reversed. This way, `SetQubitState` always puts the target qubit in the desired state.## Write a test operation to test the Bell state

Next, to demonstrate the effect of the `SetQubitState` operation, create another operation named `Main`. This operation will allocate two qubits, call `SetQubitState` to set the first qubit to a known state, and then measure the qubits to see the results.

Copy the following code into the code editor window, below the `SetQubitState` operation.

```qsharp
operation Main() : (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = One;

    // allocate the qubits
    use (q1, q2) = (Qubit(), Qubit());   
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

    // reset the qubits
    SetQubitState(Zero, q1);             
    SetQubitState(Zero, q2);
    

    // Display the times that |0> is returned, and times that |1> is returned
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2 );
}
```

In the code, the `count` and `initial` variables are set to `1000` and `One` respectively. This initializes the first qubit to `One` and measures each qubit 1000 times.

The `Main`operation:

1. Sets variables for the counter and the initial qubit state.
2. Calls the `use` statement to initialize two qubits.
3. Loops for `count` iterations. For each loop, it
4. Calls `SetQubitState` to set a specified `initial` value on the first qubit.
5. Calls `SetQubitState` again to set the second qubit to a `Zero` state.
6. Uses the `M` operation to measure each qubit.
7. Stores the number of measurements for each qubit that return `One`.
8. After the loop completes, it calls `SetQubitState` again to reset the qubits to a known state (`Zero`) to allow others to allocate the qubits in a known state. Resetting is required by the `use` statement.
9. Finally, it uses the `Message` function to print results to the Copilot output windows before returning the results.## Run the code in the Copilot for Azure Quantum

Before moving on to the procedures for superposition and entanglement, you can test the code up to this point to see the initialization and measurement of the qubits.

In order to run the code as a standalone program, the Q# compiler in the Copilot needs to know *where* to start the program. Because no namespace is specified, the compiler recognizes the default entry point as the `Main` operation. For more information, see [Projects and implicit namespaces](https://learn.microsoft.com/en-us/azure/quantum/how-to-work-with-qsharp-projects#projects-and-implicit-namespaces).

Your Q# program up to this point should now look like this:

```qsharp
import Microsoft.Quantum.Intrinsic.*;
import Microsoft.Quantum.Canon.*;

operation SetQubitState(desired : Result, target : Qubit) : Unit {
    if desired != M(target) {
        X(target);
    }
}

operation Main() : (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = One;

    // allocate the qubits
    use (q1, q2) = (Qubit(), Qubit());   
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

    // reset the qubits
    SetQubitState(Zero, q1);             
    SetQubitState(Zero, q2);
        
    
    // Display the times that |0> is returned, and times that |1> is returned
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2 );

}
```

Copy and paste the complete code sample into the [Copilot for Azure Quantum](https://quantum.microsoft.com/tools/quantum-coding) code window, set the slider for the number of shots to "1", and select **Run**. The results are displayed in the histogram and in the **Results** fields.

```output
Q1 - Zeros: 0
Q1 - Ones: 1000
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Because the qubits haven't been manipulated yet, they have retained their initial values: the first qubit returns `One` every time, and the second qubit returns `Zero`.

If you change the value of `initial` to `Zero` and run the program again, you should observe that the first qubit also returns `Zero` every time.

```output
Q1 - Zeros: 1000
Q1 - Ones: 0
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Tip

Select **Ctrl-Z** or **Edit > Undo** and save your file whenever you introduce a test change to the code before running it again.

## Put a Qubit in Superposition

Currently, the qubits in the program are all in a **classical state**, that is, they are either 1 or 0. You know this because the program initializes the qubits to a known state, and you haven't added any processes to manipulate them. Before entangling the qubits, you put the first qubit into a **superposition state**, where a measurement of the qubit returns `Zero` ~50% of the time and `One` ~50% of the time. Conceptually, the qubit can be thought of as having an equal probability of measuring either `Zero` or `One`.

To put a qubit in superposition, Q# provides the `H`, or *Hadamard*, operation. Recall the `X` operation from the [Initialize a qubit to a known state](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#initialize-a-qubit-to-a-known-state) procedure earlier, which flipped a qubit from 0 to 1 (or vice versa); the `H` operation flips the qubit *halfway* into a state of equal probabilities of `Zero` or `One`. When measured, a qubit in superposition should return roughly an equal number of `Zero` and `One` results.

Modify the code in the `Main` operation by resetting the initial value to `One` and inserting a line for the `H` operation:

```qsharp
for test in 1..count {
    use (q1, q2) = (Qubit(), Qubit());   
    for test in 1..count {
        SetQubitState(initial, q1);
        SetQubitState(Zero, q2);
        
        H(q1);                // Add the H operation after initialization and before measurement

        // measure each qubit
        let resultQ1 = M(q1);            
        let resultQ2 = M(q2); 
        ...
```

Now when you run the program, you can see the results of the first qubit in superposition.

```output
Q1 - Zeros: 523            // results vary
Q1 - Ones: 477
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Every time you run the program, the results for the first qubit vary slightly, but will be close to 50% `One` and 50% `Zero`, while the results for the second qubit remain `Zero` all the time.

```output
Q1 - Zeros: 510           
Q1 - Ones: 490
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Initializing the first qubit to `Zero` returns similar results.

```output
Q1 - Zeros: 504           
Q1 - Ones: 496
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Note

By moving the slider in the Copilot for Azure Quantum and increasing the number of shots, you can see how the superposition results vary slightly over the distribution of the shots.## Entangle two qubits

As mentioned earlier, entangled qubits are connected such that they cannot be described independently from each other. That is, whatever operation happens to one qubit, also happens to the entangled qubit. This allows you to know the resulting state of one qubit without measuring it, just by measuring the state of the other qubit. (This example uses two qubits; however, it is also possible to entangle three or more qubits).

To enable entanglement, Q# provides the `CNOT` operation, which stands for *Controlled-NOT*. The result of running this operation on two qubits is to flip the second qubit if the first qubit is `One`.

Add the `CNOT` operation to your program immediately after the `H` operation. Your full program should look like this:

```qsharp
import Microsoft.Quantum.Intrinsic.*;
import Microsoft.Quantum.Canon.*;

    operation SetQubitState(desired : Result, target : Qubit) : Unit {
        if desired != M(target) {
            X(target);
        }
    }

operation Main() : (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = Zero;

    // allocate the qubits
    use (q1, q2) = (Qubit(), Qubit());   
    for test in 1..count {
        SetQubitState(initial, q1);
        SetQubitState(Zero, q2);
    
        H(q1);            
        CNOT(q1, q2);      // Add the CNOT operation after the H operation

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

    // reset the qubits
    SetQubitState(Zero, q1);             
    SetQubitState(Zero, q2);
    

    // Display the times that |0> is returned, and times that |1> is returned
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2 );

    }
```

Now when you run the program you should see something like:

```output
Q1 - Zeros: 502           // results will vary
Q1 - Ones: 498
Q2 - Zeros: 502
Q2 - Ones: 498
```

Notice that the statistics for the first qubit haven't changed (there is still a ~50/50 chance of a `Zero` or a `One` after measurement), but the measurement results for the second qubit are **always** the same as the measurement of the first qubit, no matter how many times you run the program. The `CNOT` operation has entangled the two qubits, so that whatever happens to one of them, happens to the other.## Prerequisites

To develop and run the code sample in your local development environment:

- The latest version of [Visual Studio Code](https://code.visualstudio.com/download) or open [VS Code on the Web](https://vscode.dev/quantum).
- The latest version of the [Azure Quantum Development Kit extension](https://marketplace.visualstudio.com/items?itemName=quantum.qsharp-lang-vscode). For installation details, see [Set up the QDK extension](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk).

## Create a New Q# File

1. Open Visual Studio Code and select **File > New Text File** to create a new file.
2. Save the file as `CreateBellStates.qs`. This file will contain the Q# code for your program.## Initialize a qubit to a known state

The first step is to define a Q# operation that initializes a qubit to a known state. This operation can be called to set a qubit to a classical state, meaning it either returns `Zero` 100% of the time or returns `One` 100% of the time. `Zero` and `One` are Q# values that represent the only two possible results of a measurement of a qubit.

Open `CreateBellStates.qs` and copy the following code:

```qsharp
import Microsoft.Quantum.Intrinsic.*;
import Microsoft.Quantum.Canon.*;

operation SetQubitState(desired : Result, target : Qubit) : Unit {
    if desired != M(target) {
        X(target);
    }
}
```

The code example introduces two standard operations, `M` and `X`, which transform the state of a qubit.

The `SetQubitState` operation:

1. Takes two parameters: a type [`Result`](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/#available-types), named `desired`, that represents the desired state for the qubit to be in (`Zero` or `One`), and a type [`Qubit`](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/#available-types).
2. Performs a measurement operation, `M`, which measures the state of the qubit (`Zero` or `One`) and compares the result to the value specified in `desired`.
3. If the measurement does not match the compared value, it runs an `X` operation, which flips the state of the qubit to where the probabilities of a measurement returning `Zero` and `One` are reversed. This way, `SetQubitState` always puts the target qubit in the desired state.## Write a test operation to test the Bell state

Next, to demonstrate the effect of the `SetQubitState` operation, create another operation named `Main`. This operation allocates two qubits, call `SetQubitState` to set the first qubit to a known state, and then measure the qubits to see the results.

Add the following operation to your `CreateBellStates.qs` file after the `SetQubitState` operation:

```qsharp
operation Main() : (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = One;

    // allocate the qubits
    use (q1, q2) = (Qubit(), Qubit());   
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

    // reset the qubits
    SetQubitState(Zero, q1);             
    SetQubitState(Zero, q2);
    

    // Display the times that |0> is returned, and times that |1> is returned
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2 );
}
```

In the code, the `count` and `initial` variables are set to `1000` and `One` respectively. This step initializes the first qubit to `One` and measures each qubit 1000 times.

The `Main`operation:

1. Takes two parameters: `count`, the number of times to run a measurement, and `initial`, the desired state to initialize the qubit.
2. Calls the `use` statement to initialize two qubits.
3. Loops for `count` iterations. For each loop, it
4. Calls `SetQubitState` to set a specified `initial` value on the first qubit.
5. Calls `SetQubitState` again to set the second qubit to a `Zero` state.
6. Uses the `M` operation to measure each qubit.
7. Stores the number of measurements for each qubit that return `One`.
8. After the loop completes, it calls `SetQubitState` again to reset the qubits to a known state (`Zero`) to allow others to allocate the qubits in a known state. Resetting the qubit is required by the `use` statement.
9. Finally, it uses the `Message` function to print a message to the console before returning the results.## Run the code

Before moving on to the procedures for superposition and entanglement, test the code up to this point to see the initialization and measurement of the qubits.

In order to run the code as a standalone program, the Q# compiler needs to know *where* to start the program. Because no namespace is specified, the compiler recognizes the default entry point as the `Main` operation. For more information, see [Projects and implicit namespaces](https://learn.microsoft.com/en-us/azure/quantum/how-to-work-with-qsharp-projects#projects-and-implicit-namespaces).

1. Your `CreateBellStates.qs` file up to this point should now look like this:

```qsharp
import Microsoft.Quantum.Intrinsic.*;
import Microsoft.Quantum.Canon.*;

operation SetQubitState(desired : Result, target : Qubit) : Unit {
    if desired != M(target) {
        X(target);
    }
}

operation Main() : (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = One;

    // allocate the qubits
    use (q1, q2) = (Qubit(), Qubit());   
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

    // reset the qubits
    SetQubitState(Zero, q1);             
    SetQubitState(Zero, q2);

    // Display the times that |0> is returned, and times that |1> is returned
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2 );
}
```

2. Before running the program, ensure that the target profile is set to **Unrestricted**. Select **View -> Command Palette**, search for QIR, select **Q#: Set the Azure Quantum QIR target profile**, and then select **Q#: unrestricted**.

Note

If the target profile isn't set to **Unrestricted**, you get an error when you run the program.

3. To run the program, select **Run Q# File** from the play icon drop-down in the top-right, select **Run** from the list of commands preceding the `Main` operation, or press **Ctrl+F5**. The program runs the `Main` operation on the default simulator.
4. Your output appears in the debug console.

```output
Q1 - Zeros: 0
Q1 - Ones: 1000
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Because the qubits haven't been manipulated yet, they have retained their initial values: the first qubit returns `One` every time, and the second qubit returns `Zero`.

5. If you change the value of `initial` to `Zero` and run the program again, you should observe that the first qubit also returns `Zero` every time.

```output
Q1 - Zeros: 1000
Q1 - Ones: 0
Q2 - Zeros: 1000
Q2 - Ones: 0
```

Tip

Select **Ctrl-Z** or **Edit > Undo** and save your file whenever you introduce a test change to the code before running it again.## Put a qubit in superposition

Currently, the qubits in the program are all in a **classical state**, that is, they are either 1 or 0. You know this because the program initializes the qubits to a known state, and you haven't added any processes to manipulate them. Before entangling the qubits, you put the first qubit into a **superposition state**, where a measurement of the qubit returns `Zero` 50% of the time and `One` 50% of the time. Conceptually, the qubit can be thought of as halfway between the `Zero` and `One`.

To put a qubit in superposition, Q# provides the `H`, or *Hadamard*, operation. Recall the `X` operation from the [Initialize a qubit to a known state](https://learn.microsoft.com/en-us/azure/quantum/?pivots=ide-azure-copilot#initialize-a-qubit-to-a-known-state) procedure earlier, which flipped a qubit from `Zero` to `One` (or vice versa); the `H` operation flips the qubit *halfway* into a state of equal probabilities of `Zero` or `One`. When measured, a qubit in superposition should return roughly an equal number of `Zero` and `One` results.

1. Modify the code in the `Main` operation to include the `H` operation:

```qsharp
for test in 1..count {
    use (q1, q2) = (Qubit(), Qubit());   
    for test in 1..count {
        SetQubitState(initial, q1);
        SetQubitState(Zero, q2);

        H(q1);                // Add the H operation after initialization and before measurement

        // measure each qubit
        let resultQ1 = M(q1);            
        let resultQ2 = M(q2); 
        ...
```

2. Now when you run the program, you can see the results of the first qubit in superposition:

```output
Q1 - Zeros: 523            // results will vary
Q1 - Ones: 477
Q2 - Zeros: 1000
Q2 - Ones: 0
```

3. Every time you run the program, the results for the first qubit vary slightly, but will be close to 50% `One` and 50% `Zero`, while the results for the second qubit remain `Zero` all the time.

```output
Q1 - Zeros: 510           
Q1 - Ones: 490
Q2 - Zeros: 1000
Q2 - Ones: 0
```

4. Initializing the first qubit to `Zero` returns similar results.

```output
Q1 - Zeros: 504           
Q1 - Ones: 496
Q2 - Zeros: 1000
Q2 - Ones: 0
```

## Entangle Two Qubits

As mentioned earlier, entangled qubits are connected such that they cannot be described independently from each other. That is, whatever operation happens to one qubit, also happens to the entangled qubit. This allows you to know the resulting state of one qubit without measuring it, just by measuring the state of the other qubit. (This example uses two qubits; however, it is also possible to entangle three or more qubits).

To enable entanglement, Q# provides the `CNOT` operation, which stands for *Controlled-NOT*. The result of running this operation on two qubits is to flip the second qubit if the first qubit is `One`.

1. Add the `CNOT` operation to your program immediately after the `H` operation. Your full program should look like this:

```qsharp
import Microsoft.Quantum.Intrinsic.*;
import Microsoft.Quantum.Canon.*;

    operation SetQubitState(desired : Result, target : Qubit) : Unit {
        if desired != M(target) {
            X(target);
        }
    }

operation Main() : (Int, Int, Int, Int) {
    mutable numOnesQ1 = 0;
    mutable numOnesQ2 = 0;
    let count = 1000;
    let initial = Zero;

    // allocate the qubits
    use (q1, q2) = (Qubit(), Qubit());   
    for test in 1..count {
        SetQubitState(initial, q1);
        SetQubitState(Zero, q2);

        H(q1);            
        CNOT(q1, q2);      // Add the CNOT operation after the H operation

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

    // reset the qubits
    SetQubitState(Zero, q1);             
    SetQubitState(Zero, q2);

    // Display the times that |0> is returned, and times that |1> is returned
    Message($"Q1 - Zeros: {count - numOnesQ1}");
    Message($"Q1 - Ones: {numOnesQ1}");
    Message($"Q2 - Zeros: {count - numOnesQ2}");
    Message($"Q2 - Ones: {numOnesQ2}");
    return (count - numOnesQ1, numOnesQ1, count - numOnesQ2, numOnesQ2 );

    }
```

```output
Q1 - Zeros: 502           
Q1 - Ones: 498       // results will vary
Q2 - Zeros: 502
Q2 - Ones: 498
Result: "(502, 498, 502, 498)"
```

The statistics for the first qubit haven't changed (a 50/50 chance of a `Zero` or a `One` after measurement), but the measurement results for the second qubit are **always** the same as the measurement of the first qubit. The `CNOT` operation entangled the two qubits, so that whatever happens to one of them, happens to the other.### Plot the frequency histogram

Let's visualize the distribution of results obtained from running the quantum program multiple times. The frequency histogram helps visualize the probability distribution of these outcomes.

1. Select **View -> Command Palette**, or press **Ctrl+Shift+P**, and type "histogram" which should bring up the **Q#: Run file and show histogram** option. You can also select **Histogram** from the list of commands preceding `Main`. Select this option to open the Q# histogram window.
2. Enter a number of **shots** to execute the program, for example, 100 shots, and press **Enter**. The histogram displays in the Q# histogram window.
3. Each bar in the histogram corresponds to a possible outcome, and its height represents the number of times that outcome is observed. In this case, there are 50 different unique results. Note that for each outcome the measurement results for the first and the second qubit are always the same.

![Screenshot the Q# histogram window in Visual Studio Code.](https://learn.microsoft.com/en-us/azure/quantum/media/histogram-vscode-entanglement.png)

Tip

You can zoom the histogram using the mouse scroll wheel or a trackpad gesture. When zoomed in, you can pan the chart by pressing **Alt** while scrolling.

4. Select a bar to display the **percentage** of that outcome.
5. Select the top-left **settings icon** to display options. You can display top 10 results, top 25 results, or all results. You can also sort the results from high to low, or low to high.

![Screenshot the Q# histogram window in Visual Studio Code showing how to display settings.](https://learn.microsoft.com/en-us/azure/quantum/media/histogram-vscode-entanglement-tab.png)## Related content

Explore other Q# tutorials:

- [Grover's search algorithm](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-grovers-search) shows how to write a Q# program that uses Grover's search algorithm.
- [Quantum Fourier Transform](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-qubit-level-program) explores how to write a Q# program that directly addresses specific qubits.
- The [Quantum Katas](https://quantum.microsoft.com/tools/quantum-katas) are self-paced tutorials and programming exercises aimed at teaching the elements of quantum computing and Q# programming at the same time.

---

## Feedback

## Additional Resources

---

Training

---

Documentation

- [Tutorial: Create a Quantum Random Number Generator - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-quantum-random-number-generator?source=recommendations)

Build a Q# project that demonstrates fundamental quantum concepts like superposition by creating a quantum random number generator.

- [Tutorial: Quantum Fourier Transform in Q\\# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-qubit-level-program?source=recommendations)

In this tutorial, learn how to write and simulate a quantum program that operates at the individual qubit level.

- [Quickstart: Create a Q# Program - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/qsharp-quickstart?source=recommendations)

This article explains how to create your first Q# program using the Quantum Development Kit and Visual Studio Code.

- [Tutorial: Implement Grover's Algorithm in Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/tutorial-qdk-grovers-search?source=recommendations)

In this tutorial, you will build a Q# project that demonstrates Grover's search algorithm, one of the canonical quantum algorithms.

- [Introduction to the Quantum Programming Language Q# - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/qsharp-overview?source=recommendations)

This article introduces Q#, a programming language for developing and running quantum algorithms, and the structure of a Q# program.

- [Q# features in Visual Studio Code - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/vscode-qsharp-reference?source=recommendations)

Learn about the features that are included with the Azure Quantum Development Kit extension for VS Code.

- [Set up the Quantum Development Kit Extension - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk?source=recommendations)

Learn how to set up the Azure Quantum Development Kit VS Code extension and set up your environment for different languages and platforms.

- [Submit Q# Programs with VS Code - Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/how-to-submit-jobs?source=recommendations)

This document provides a basic guide to submit and run Azure Quantum using the Azure portal, Python, Jupyter Notebooks, or the Azure CLI.

### In This Article
