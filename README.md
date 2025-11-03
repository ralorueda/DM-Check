# 🧩 DM-Check: A Deductive Model Checker

**DM-Check** is a *deductive model checker* that integrates narrowing-based symbolic model checking with inductive theorem proving.  
It aims to establish inductive invariants (i.e., transition-closed invariants) symbolically for concurrent systems specified as topmost Maude modules.

The tool allows users to:
- Verify whether a disjunction of patterns is an invariant.
- Check containment (subsumption) between two symbolic state descriptions.
- Detect intersections between symbolic state sets.
- Interactively reason with lemmas and symbolic exploration.

👉 For the full command reference and detailed documentation, visit:  
🔗 [https://safe-tools.dsic.upv.es/dmc/](https://safe-tools.dsic.upv.es/dmc/)

---

## ⚙️ Installation

### 1️⃣ Install Maude
DM-Check requires **The Maude System** (version 3.5.1 or later).  
Download and install Maude from the [official Maude GitHub](https://github.com/maude-lang/Maude/releases/tag/Maude3.5.1).

### 2️⃣ Download DM-Check
Visit the DM-Check official page:  
🔗 [https://safe-tools.dsic.upv.es/dmc/](https://safe-tools.dsic.upv.es/dmc/)

Select the latest version (e.g., **v0.2.0a**) and download the compressed package.

### 3️⃣ Unzip DM-Check
Extract the downloaded archive to a directory of your choice.  
This folder will contain all files needed to run DM-Check.

### 4️⃣ Load Maude and DM-Check
1. Open Maude in your terminal.  
2. Load your system module (the `.maude` file describing the system to verify).  
3. Load DM-Check with the following command:
   ```maude
   load $folder_path$/dm-check-ui
   ```
   *(replace `$folder_path$` with the directory where DM-Check was extracted).*

Once loaded, you can start using DM-Check commands to check invariants, containment, or disjointness between symbolic state sets.

---

## 🚀 Quick Start Example

Inside Maude:

```maude
Maude> load dm-check-ui
DM-Check> set module MY-SYSTEM .
DM-Check> check invariant myInvariant .
```

---

## 📚 More Information

For a complete list of commands, examples, and advanced features (lemmas, subsumption checking, rechecking proofs, etc.), visit the full documentation:  
🔗 [https://safe-tools.dsic.upv.es/dmc/](https://safe-tools.dsic.upv.es/dmc/)

---

## 🧠 Credits

**DM-Check** is developed at the *Universitat Politècnica de València (UPV)* as part of the **SAFE-Tools** framework for formal verification.

---

## 📜 License

This project is distributed for academic and research purposes.  
For reuse or modifications, please contact the authors or refer to the licensing information on the [official DM-Check page](https://safe-tools.dsic.upv.es/dmc/).
