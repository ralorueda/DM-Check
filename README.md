# DM-Check: A Deductive Model Checker

**DM-Check** integrates *narrowing-based symbolic exploration* with *inductive theorem proving* to establish **inductive invariants** (i.e., transition-closed invariants) in a symbolic manner for a given topmost Maude system module.

A candidate invariant is provided as a **disjunction of constrained patterns** of the form:

where each `u` is a pattern (a constructor term with variables describing sets of states) and each `φ` is a constraint (a conjunction of equalities).

---

## Table of Contents

- [Overview](#overview)
- [Verification Workflow](#verification-workflow)
- [Features](#features)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
- [Main Commands](#main-commands)
- [Auxiliary and Interactive Commands](#auxiliary-and-interactive-commands)
- [Examples](#examples)
- [Integration with NuITP](#integration-with-nuitp)
- [Citation and License](#citation-and-license)
- [Contact](#contact)

---

## Overview

DM-Check combines **symbolic model checking** with **deductive reasoning** to verify properties of concurrent systems specified in Maude.  
It symbolically explores the transition system using narrowing and proves inductiveness of invariants using constraint-based reasoning.

---

## Verification Workflow

1. **Symbolic Exploration**  
   The tool uses narrowing with the module’s rewrite rules to compute one-step symbolic successors of the provided patterns.

2. **Symbolic Instance Verification**  
   Each successor is checked to be a symbolic instance of a parent pattern through matching modulo axioms and constraint implication.  
   Constraint reasoning is delegated to the **NuITP** solver.

When automatic checking is insufficient, DM-Check allows interactive proof guidance through additional commands.

---

## Features

- ✅ Symbolic inductive invariant verification  
- 🔄 Subsumption (containment) checking between disjunctions of constrained patterns  
- 🚫 Intersection detection between symbolic state sets  
- 📚 Support for user-defined lemmas (in NuITP syntax)  
- 🧠 Delegation of complex reasoning to **NuITP**  
- 🔍 Interactive commands for manual proof steps  
- 🧾 Re-execution of proofs after adding new lemmas  

---

## Installation

### 1. Install Maude

Download and install **[The Maude System](https://maude.cs.illinois.edu/)** version **3.5.1** or later.  
If Maude is already installed, proceed to the next step.

### 2. Download DM-Check

- **Version:** v0.2.0a  
- **Download:** [Link to release](#)  
- **Patch Notes:** [View](#)

### 3. Unzip DM-Check

Extract the downloaded archive into a directory of your choice.

### 4. Load DM-Check in Maude

Start Maude and load both your system module and DM-Check:

load $PATH$/dm-check-ui


Once loaded, DM-Check will be available through its interactive interpreter.

## Basic Usage

### Set the Target Module

`DM-Check> set module R&W-FAIR .`

