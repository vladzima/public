---
aliases: [Introduction to the quantum programming language Q]
title: Introduction to the quantum programming language Q
source: https://learn.microsoft.com/en-us/azure/quantum/qsharp-overview
created: 2025-01-24
tags: [clippings, knowledge]
category: LLM workspace
notebookllm: https://notebooklm.google.com/notebook/0d8cb8ba-3243-46ad-b67a-863bc3402c32?authuser=1
linter-yaml-title-alias: Introduction to the quantum programming language Q
date created: Friday, January 24th 2025, 12:01:08 pm
date modified: Sunday, January 26th 2025, 6:58:43 pm
---

```folder-overview
id: 7cc120c2-ac17-4d57-bf34-4ad32df058c7
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

<audio src="/audio/Introduction to Quantum Programming with Q#.wav" controls></audio>

## Introduction to the Quantum Programming Language Q#

Q# is a high-level, [open-source](https://github.com/microsoft/qsharp) programming language for developed by Microsoft for writing quantum programs. Q# is included in the Quantum Development Kit (QDK). For more information, see [Set up the Quantum Development Kit](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk).

As a quantum programming language, Q# meets the following requirements for language, compiler, and runtime:

- **Hardware agnostic:** Qubits in quantum algorithms aren't tied to a specific quantum hardware or layout. The Q# compiler and runtime handle the mapping from program qubits to physical qubits, allowing the same code to run on different quantum processors.
- **Integration of quantum and classical computing:** Q# allows for the integration of quantum and classical computations, which is essential for universal quantum computing.
- **Qubit management:** Q# provides built-in operations and functions for managing qubits, including creating superposition states, entangling qubits, and performing quantum measurements.
- **Respect the laws of physics:** Q# and quantum algorithms must follow the rules of quantum physics. For example, you can't directly copy or access the qubit state in Q#.

For more information about the origins of Q#, see the blog post [Why do we need Q#?](https://devblogs.microsoft.com/qsharp/why-do-we-need-q/).

## Structure of a Q# Program

Before you start writing Q# programs, it's important to understand their structure and components. Consider the following Q# program, named **Superposition**, that creates a superposition state:

```qsharp
namespace Superposition {
    @EntryPoint()
    operation MeasureOneQubit() : Result {
        // Allocate a qubit. By default, it's in the 0 state.  
        use q = Qubit();  
        // Apply the Hadamard operation, H, to the state.
        // It now has a 50% chance of being measured as 0 or 1.
        H(q);      
        // Measure the qubit in the Z-basis.
        let result = M(q);
        // Reset the qubit before releasing it.
        Reset(q);
        // Return the result of the measurement.
        return result;
    }
}
```

Based on the comments (`//`), the Q# program first allocates a qubit, applies an operation to put the qubit in superposition, measures the qubit state, resets the qubit, and finally returns the result.

Let's break this Q# program down into its components.

### User Namespaces

Q# programs can optionally start with a user-defined [namespace](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/programstructure/namespaces), such as:

```qsharp
namespace Superposition {
    // Your code goes here.
}
```

Namespaces can help you organize related functionality. Namespaces are optionaL in Q# programs, meaning that you can write a program without defining a namespace.

For example, the **Superposition** program of the example could be also written without a namespace as:

```qsharp
@EntryPoint()
operation MeasureOneQubit() : Result {
    // Allocate a qubit. By default, it's in the 0 state.  
    use q = Qubit();  
    // Apply the Hadamard operation, H, to the state.
    // It now has a 50% chance of being measured as 0 or 1.
    H(q);      
    // Measure the qubit in the Z-basis.
    let result = M(q);
    // Reset the qubit before releasing it.
    Reset(q);
    // Return the result of the measurement.
    return result;
}
```

Note

Each Q# program can have only one `namespace`. If you don't specify a namespace, the Q# compiler uses the filename as the namespace.### Entry points

Every Q# program must have an entry point, which is the starting point of the program. By default, the Q# compiler starts executing a program from the `Main()` operation, if available, which can be located anywhere in the program. Optionally, you can use the `@EntryPoint()` attribute to specify any operation in the program as the point of execution.

For example, in the **Superposition** program, the `MeasureOneQubit()` operation is the entry point of the program because it has the `@EntryPoint()` attribute before the operation definition:

```qsharp
@EntryPoint()
operation MeasureOneQubit() : Result {
    ...
}
```

However, the program could also be written without the `@EntryPoint()` attribute by renaming the `MeasureOneQubit()` operation to `Main()`, such as:

```qsharp
// The Q# compiler automatically detects the Main() operation as the entry point. 

operation Main() : Result {
    // Allocate a qubit. By default, it's in the 0 state.  
    use q = Qubit();  
    // Apply the Hadamard operation, H, to the state.
    // It now has a 50% chance of being measured as 0 or 1.
    H(q);      
    // Measure the qubit in the Z-basis.
    let result = M(q);
    // Reset the qubit before releasing it.
    Reset(q);
    // Return the result of the measurement.
    return result;
}
```

### Types

Types are essential in any programming language because they define the data that a program can work with. Q# provides [built-in types](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/) that are common to most languages, including `Int`, `Double`, `Bool`, and `String`, and types that define ranges, arrays, and tuples.

Q# also provides types that are [specific to quantum computing](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/quantumdatatypes). For example, the `Result` type represents the result of a qubit measurement and can have two values: `Zero` or `One`.

In the **Superposition** program, the `MeasureOneQubit()` operation returns a `Result` type, which corresponds to the return type of the `M` operation. The measurement result is stored in a new variable that's defined using the `let` statement:

```qsharp
// The operation definition returns a Result type.
operation MeasureOneQubit() : Result {
    ...
    // Measure the qubit in the Z-basis, returning a Result type.
    let result = M(q);
    ...
}
```

Another example of a quantum-specific type is the `Qubit` type, which represents a quantum bit.

Q# also allows you to define your own custom types. For more information, see [Type declarations](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/programstructure/typedeclarations).### Allocating qubits

In Q#, you allocate qubits using the `use` keyword and the `Qubit` type. Qubits are always allocated in the $\left|\right. 0 \rangle$ state.

For example, the **Superposition** program defines a single qubit and stores it in the variable `q`:

```qsharp
// Allocate a qubit.
use q = Qubit();
```

You can also allocate multiple qubits and access each one through its index:

```qsharp
use qubits = Qubit[2]; // Allocate two qubits.
H(qubits[0]); // Apply H to the first qubit.
X(qubits[1]); // Apply X to the second qubit.
```

For more information, see [Use statement](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/statements/quantummemorymanagement#use-statement).### Quantum operations

After allocating a qubit, you can pass it to operations and functions. [Operations](https://learn.microsoft.com/en-us/azure/quantum/user-guide/language/typesystem/operationsandfunctions) are the basic building blocks of a Q# program. A Q# operation is a quantum subroutine, or a callable routine that contains quantum operations that change the state of the qubit register.

To define a Q# operation, you specify a name for the operation, its inputs, and its output. In the **Superposition** program, the `MeasureOneQubit()` operation takes no parameters and returns a `Result` type:

```qsharp
operation MeasureOneQubit() : Result {
    ...
}
```

Here's a basic example that takes no parameters and expects no return value. The `Unit` value is equivalent to `NULL` in other languages:

```qsharp
operation SayHelloQ() : Unit {
    Message("Hello quantum world!");
}
```

The Q# standard library also provides operations you can use in quantum programs, such as the Hadamard operation, `H`, in the `Superposition` program. Given a qubit in the Z-basis, `H` puts the qubit into an even superposition, where it has a 50% chance of being measured as `Zero` or `One`.### Measuring qubits

While there are many types of quantum measurements, Q# focuses on projective measurements on single qubits, also known as [Pauli measurements](https://learn.microsoft.com/en-us/azure/quantum/concepts-pauli-measurements).

In Q#, the `Measure` operation measures one or more qubits in the specified Pauli basis, which can be `PauliX`, `PauliY`, or `PauliZ`. `Measure` returns a `Result` type of either `Zero` or `One`.

To implement a measurement in the computational basis $\left{\right. \left|\right. 0 \rangle, \left|\right. 1 \rangle \left.\right}$, you can also use the `M` operation, which measures a qubit in the Pauli Z-basis. This makes `M` equivalent to `Measure([PauliZ], [qubit])`.

For example, the **Superposition** program uses the `M` operation:

```qsharp
// Measure the qubit in the Z-basis.
let result = M(q);
```### Resetting qubits

In Q#, qubits **must** be in the $\left|\right. 0 \rangle$ state when they're released to avoid errors in the quantum hardware. You can reset a qubit to the $\left|\right. 0 \rangle$ state using the `Reset` operation at the end of the program. Failure to reset a qubit results in a runtime error.

```qsharp
// Reset a qubit.
Reset(q);
```

### Standard Library Namespaces

The Q# standard library has built-in namespaces that contain functions and operations you can use in quantum programs. For example, the `Microsoft.Quantum.Intrinsic` namespace contains commonly used operations and functions, such as `M` to measure results and `Message` to display user messages anywhere in the program.

To call a function or operation, you can specify the full namespace or use an `import` statement, which makes all the functions and operations for that namespace available and makes your code more readable. The following examples call the same operation:

```qsharp
Microsoft.Quantum.Intrinsic.Message("Hello quantum world!");
```

```qsharp
// imports all functions and operations from the Microsoft.Quantum.Intrinsic namespace.
import Microsoft.Quantum.Intrinsic.*;
Message("Hello quantum world!");

// imports just the \`Message\` function from the Microsoft.Quantum.Intrinsic namespace.
import Microsoft.Quantum.Intrinsic.Message;
Message("Hello quantum world!");
```

```qsharp
// namespaces in the standard library may be imported using \`Std\` instead of \`Microsoft.Quantum\`. 
import Std.Intrinsic.*;
Message("Hello quantum world!");
```

Note

The **Superposition** program doesn't have any `import` statements or calls with full namespaces. That's because the Q# development environment automatically loads two namespaces: `Microsoft.Quantum.Core` and `Microsoft.Quantum.Intrinsic`, which contain commonly used functions and operations.

You can take advantage of the `Microsoft.Quantum.Measurement` namespace by using the `MResetZ` operation to optimize the **Superposition** program. `MResetZ` combines the measurement and reset operations into one step, as in the following example:

```qsharp
// Import the namespace for the MResetZ operation.
import Microsoft.Quantum.Measurement.*;

@EntryPoint()
operation MeasureOneQubit() : Result {
    // Allocate a qubit. By default, it's in the 0 state.      
    use q = Qubit();  
    // Apply the Hadamard operation, H, to the state.
    // It now has a 50% chance of being measured as 0 or 1. 
    H(q);   
    // Measure and reset the qubit, and then return the result value.
    return MResetZ(q);
}
```

## Learn to Develop Quantum Programs with Q# and Azure Quantum

Q# and Azure Quantum are a powerful combination for developing and running quantum programs. With Q# and Azure Quantum, you can write quantum programs, simulate their behavior, estimate resource requirements, and run them on real quantum hardware. This integration allows you to explore the potential of quantum computing and develop innovative solutions for complex problems. Whether you are a beginner or an experienced quantum developer, Q# and Azure Quantum provide the tools and resources you need to unlock the power of quantum computing.

The following diagram shows the stages through which a quantum program passes when you develop it with Q# and Azure Quantum. Your program starts with the development environment and ends with the submission of the job to real quantum hardware.

![Diagram showing the workflow of quantum programming development.](https://learn.microsoft.com/en-us/azure/quantum/media/quantum-development-kit-flow-diagram.svg)

Let's break down the steps in the diagram.### Choose your development environment

Run your quantum programs in your preferred development environment. You can use the online code editor in the Azure Quantum website, the hosted Jupyter Notebooks in your Azure Quantum workspace in the Azure portal, or a local development environment with Visual Studio Code. For more information, see [Different ways to run Q# programs](https://learn.microsoft.com/en-us/azure/quantum/qsharp-ways-to-work).### Write your quantum program

You can write quantum programs in Q# using the Quantum Development Kit (QDK). To get started, see [Quickstart: Create your first Q# program](https://learn.microsoft.com/en-us/azure/quantum/qsharp-quickstart).

Besides Q#, the QDK offers support for other languages for quantum computing, such as [Qiskit](https://learn.microsoft.com/en-us/azure/quantum/quickstart-microsoft-qiskit) and [Cirq](https://learn.microsoft.com/en-us/azure/quantum/quickstart-microsoft-cirq).### Integrate with Python

You can use Q# by itself or together with Python in various IDEs. For example, you can use a Q# project with a Python host program to call Q# operations or integrate Q# with Python in Jupyter Notebooks. For more information, see [Integration of Q# and Python](https://learn.microsoft.com/en-us/azure/quantum/qsharp-ways-to-work#integration-of-q-and-python).

### The `%%qsharp` Command

By default, Q# programs in Jupyter Notebooks use the `ipykernel` Python package. To add Q# code to a notebook cell, use the `%%qsharp` command, which is enabled with the `qsharp` Python package, followed by your Q# code.

When using `%%qsharp`, keep the following in mind:

- You must first run `import qsharp` to enable `%%qsharp`.
- `%%qsharp` scopes to the notebook cell in which it appears and changes the cell type from Python to Q#.
- You can't put a Python statement before or after `%%qsharp`.
- Q# code that follows `%%qsharp` must adhere to Q# syntax. For example, use `//` instead of `#` to denote comments and `;` to end code lines.### Estimate resources

Before running on real quantum hardware, you need to figure out whether your program can run on existing hardware, and how many resources it'll consume.

The [Azure Quantum Resource Estimator](https://learn.microsoft.com/en-us/azure/quantum/overview-resources-estimator) allows you to assess architectural decisions, compare qubit technologies, and determine the resources needed to execute a given quantum algorithm. You can choose from pre-defined fault-tolerant protocols and specify assumptions of the underlying physical qubit model.

For more information, see [Run your first resource estimate](https://learn.microsoft.com/en-us/azure/quantum/quickstart-microsoft-resources-estimator).

Note

The Azure Quantum Resources Estimator is free of charge and doesn't require an Azure account.

## Run Your Program in Simulation

When you compile and run a quantum program, the QDK creates an instance of the quantum simulator and passes the Q# code to it. The simulator uses the Q# code to create qubits (simulations of quantum particles) and apply transformations to modify their state. The results of the quantum operations in the simulator are then returned to the program. Isolating the Q# code in the simulator ensures that the algorithms follow the laws of quantum physics and can run correctly on quantum computers.### Submit your program to real quantum hardware

You can submit your Q# programs to Azure Quantum to run on real quantum hardware. You can also run and submit quantum circuits written in Qiskit and Cirq languages. When you run a quantum program in Azure Quantum, you create and run a **job**. For more information, see [how to submit Q# programs to Azure Quantum](https://learn.microsoft.com/en-us/azure/quantum/how-to-submit-jobs).

Azure Quantum offers some of the most compelling and diverse quantum hardware available today from industry leaders. See [Quantum computing providers](https://learn.microsoft.com/en-us/azure/quantum/qc-target-list) for the current list of supported hardware providers.

Note

To submit a job to the Azure Quantum providers, you need an Azure account and quantum workspace. If you don't have a quantum workspace, see [Create an Azure Quantum workspace](https://learn.microsoft.com/en-us/azure/quantum/how-to-create-workspace).

Once you submit your job, Azure Quantum manages the job lifecycle, including job scheduling, execution, and monitoring. You can track the status of your job and view the results in the Azure Quantum portal. For more information, see [Work with Azure Quantum jobs](https://learn.microsoft.com/en-us/azure/quantum/how-to-work-with-jobs).## Related content

- [Different ways to run Q# programs](https://learn.microsoft.com/en-us/azure/quantum/qsharp-ways-to-work)
- [Set up the Quantum Development Kit](https://learn.microsoft.com/en-us/azure/quantum/install-overview-qdk)
- [Quickstart: Create your first Q# program](https://learn.microsoft.com/en-us/azure/quantum/qsharp-quickstart)
