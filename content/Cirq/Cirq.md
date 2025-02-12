---
aliases:
  - Cirq
tags:
  - quantum
  - knowledge
title: Cirq
linter-yaml-title-alias: Cirq
date created: Wednesday, February 5th 2025, 10:51:00 pm
date modified: Wednesday, February 5th 2025, 10:57:34 pm
---

```folder-overview
id: 9e49ddb2-ee9c-4013-ad85-8af4aa5d8e67
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

## An Open Source Framework for Programming Quantum Computers

Cirq is a Python software library for writing, manipulating, and optimizing quantum circuits, and then running them on quantum computers and quantum simulators. Cirq provides useful abstractions for dealing with today's noisy intermediate-scale quantum computers, where details of the hardware are vital to achieving state-of-the-art results.

[GitHub - quantumlib/Cirq: A Python framework for creating, editing, and invoking Noisy Intermediate-Scale Quantum (NISQ) circuits.](https://github.com/quantumlib/cirq)

```python
import cirq

# Pick a qubit.
qubit = cirq.GridQubit(0, 0)

# Create a circuit
circuit = cirq.Circuit(
    cirq.X(qubit)**0.5,  # Square root of NOT.
    cirq.measure(qubit, key='m')  # Measurement.
)
print("Circuit:")
print(circuit)

# Simulate the circuit several times.
simulator = cirq.Simulator()
result = simulator.run(circuit, repetitions=20)
print("Results:")
print(result)
```
