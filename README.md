# DM-Check: A Deductive Model Checker

**DM-Check** integrates **narrowing-based symbolic model checking** with **inductive theorem proving** to establish inductive invariants (transition-closed invariants) in a symbolic manner for a given topmost Maude system module — that is, for a topmost rewrite theory specifying a concurrent system.

The candidate inductive invariant is given as a disjunction of constrained patterns.

---

## 🔍 How It Works

1. **Narrowing-based symbolic exploration**  
   The tool uses narrowing with the module’s rewrite rules to compute one-step symbolic successors of the provided patterns.

2. **Symbolic instance verification**  
   Each successor is checked to be a symbolic instance of a parent pattern via matching modulo axioms and constraint implication.  
   Constraint reasoning is delegated to the **NuITP** tool.

The checks are **sound** but not necessarily complete. When they fail, DM-Check offers commands to guide additional reasoning.

---

## ⚙️ Additional Capabilities

- Check **subsumption** between two disjunctions of constrained patterns (for invariant generalization or initial-state characterization).  
- Detect **intersections** between two disjunctions of constrained patterns.  
- **Incorporate supporting lemmas** to help prove invariants.  
- **Display** loaded lemmas, the selected theory, proof obligations, and the result of the last command.  
- **Delegate** unproven results to NuITP by marking them as lemmas.  
- **Re-execute** verification after adding lemmas or external proofs.

---

## 🧩 Installation

1. **Install Maude**  
   Download and install **The Maude System** (version 3.5.1 or later).  
   👉 <https://maude.cs.illinois.edu/>

2. **Download DM-Check**  
   Latest version: **v0.2.0a**  
   - [⬇️ Download ZIP](https://github.com/vrain-upv/dm-check/releases/download/v0.2.0a/dm-check-v0.2.0a.zip)  
   - [📝 Patch Notes](https://github.com/vrain-upv/dm-check/releases/tag/v0.2.0a)

3. **Unzip** the archive into a folder of your choice.

4. **Load in Maude**
   ```maude
   load your-system-module.maude
   load /path/to/dm-check/dm-check-ui
