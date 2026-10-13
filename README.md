# DM-Check: A Deductive Model Checker

DM-Check is an interactive deductive model checker for invariants of concurrent systems specified as rewrite theories in [Maude](https://maude.cs.illinois.edu/). It combines symbolic exploration by constrained narrowing with inductive theorem proving in [NuITP](https://nuitp.webs.upv.es/) to check and generate inductive invariants of infinite-state systems.

- Website: <https://dmcheck.webs.upv.es/>
- User manual (PDF): <https://dmcheck.webs.upv.es/docs/dm-check-user-manual.pdf>
- Current release: public alpha 12 (build of 13 October 2026)

## Features

- Check that a disjunction of constrained patterns is an inductive invariant (`check ind-invariant`).
- Generate an inductive invariant level by level from the initial states (`generate ind-invariant`, `generate next level`), with generalization of frontier patterns.
- Check containment between symbolic sets of states (`check ... subsumed by ...`), for instance that the initial states are covered by an invariant, or deadlock freedom.
- Check that two symbolic sets of states do not intersect (`intersect ... with ...`), for instance an invariant and a set of error states.
- Continue a proof by hand when automation reaches its limit: lemmas, constructor case splits, variant unification, narrowing of auxiliary terms, variable abstraction, substitution, Boolean splits and generalization.
- Report the proof obligations that are not discharged automatically. They can be proved with the bundled NuITP.
- Replay proofs from command scripts (`.dmc` files) and save the accepted commands of a session as a script.
- Rewrite rules may be conditional, with conditions that are conjunctions of equations.

## Requirements

- Maude. The current release is tested with Maude alpha 165, available from the [Maude releases page](https://github.com/maude-lang/Maude/releases). On Windows, use Maude through the Windows Subsystem for Linux (WSL).
- NuITP is included (`NuITP.maude`). No separate installation is required.

## Installation

Download the release archive from the [installation page](https://dmcheck.webs.upv.es/install.html) and extract it, or clone this repository. Either way you get:

- `dm-check.maude`: the tool;
- `NuITP.maude`: the compatible NuITP release, which `dm-check.maude` loads from its own directory (keep both files together);
- `examples/`: the case studies.

Start Maude from that directory, load your specification, then load DM-Check and select the module:

```
$ maude -allow-files
Maude> load my-system.maude
Maude> load dm-check.maude
DM-Check> set module MY-SYSTEM .
```

The `-allow-files` flag (or `-trust`) is needed only to read and write script files with `load` and `save proof`.

## Quick start

The Readers–Writers example in `examples/rw/` checks an inductive invariant and deadlock freedom:

```
$ maude -allow-files
Maude> load examples/rw/rw.maude
Maude> load dm-check.maude
DM-Check> set module R&W .
DM-Check> check ind-invariant < N:Natural , 0 > | true \/ < 0 , s(0) > | true .

  Invariant satisfied.

DM-Check> check < N:Natural , 0 > | true \/ < 0 , s(0) > | true subsumed by < 0, 0 > | true \/ < R:Natural, s(W:Natural) > | true \/ < R:Natural, 0 > | true \/ < s(R:Natural), W:Natural > | true .

  Subsumption satisfied.
```

At the prompt, each command goes on a single line. The same commands are stored in `examples/rw/rw.dmc`, where long commands are split with `\` at the end of the line, and can be run with `load examples/rw/rw.dmc` (`load` takes no final period).

## Main commands

A constrained pattern is written `t | C`, where `t` is a state term and `C` is a conjunction of equations (or `true`). A symbolic set of states `φ` is a disjunction of constrained patterns separated by `\/`.

| Command | Purpose |
|---|---|
| `set module <M> .` | Select the module to analyze |
| `check ind-invariant <φ> .` | Check that `φ` is an inductive invariant |
| `generate ind-invariant <φ> .` / `generate next level .` | Generate an inductive invariant from the initial states `φ` |
| `check φ1 subsumed by φ2 .` | Check that `φ1` is contained in `φ2` |
| `intersect φ1 with φ2 .` | Check whether `φ1` and `φ2` intersect |
| `lemma <id> .` / `lemma <id> match <n> .` | Close a node with an open implication, which becomes a proof obligation |
| `add lemma <lemma> .` / `reset lemmas .` | Add or remove session lemmas |
| `case to <id> on <v> with <G> .` | Split a node by the constructor cases `G` of a variable |
| `variant unify <t1> =? <t2> in <id> .` | Propagate an equation of the constraint by variant unification |
| `narrowing <id> on <term> .` | Narrow an auxiliary term with the equations of its root operator |
| `variable abstraction <term> with <variable> in <id> .` | Replace a subterm by a fresh variable |
| `substitution to <id> on <v> with <t> .` | Instantiate a variable |
| `split <id> with <term> .` | Split a node on a Boolean term |
| `generalize <id> to <pattern> .` | Replace frontier nodes by a more general pattern |
| `empty <id> .` | Close a node whose constraint is unsatisfiable |
| `show lemmas .` / `show obligations .` / `show path <id> .` | Inspect the current proof |
| `load <file>` / `save proof <file> .` | Run a script / save the session as a script |
| `q` | Return to Maude |

See the user manual for the exact behavior of each command, its side conditions and the proof obligations it may generate.

## Writing specifications

DM-Check analyzes topmost rewrite theories. In short:

- Mark the rewrite rules used for symbolic exploration with the `narrowing` attribute. Conditional rules must have equational conditions only.
- Declare constructors with the `ctor` attribute. Build the terms of constrained patterns, and the right-hand sides of rules, from constructors and symbols defined by `variant` equations; use other defined functions in the constraints.
- Start the specification with `set include BOOL off .`, as the examples do, and give the operators an RPO order with the `rpo` attribute for NuITP.
- `set module` rejects modules with `owise` equations, the built-in `_==_` or `_=/=_`, conditional rules with non-equational conditions, or membership axioms.

The full list of requirements is in the user manual.

## Examples

Each folder of `examples/` contains the Maude module, a `.dmc` script with the DM-Check commands and, when the proof generates obligations, a `.nuitp` script that discharges them. The case studies are described on the [website](https://dmcheck.webs.upv.es/).

```
$ maude -allow-files
Maude> load examples/qlock/qlock.maude
Maude> load dm-check.maude
DM-Check> load examples/qlock/qlock.dmc
```

To prove the obligations with NuITP, start a new Maude session:

```
$ maude -allow-files
Maude> load examples/qlock/qlock.maude
Maude> load NuITP.maude
Maude> erew in NuITP : init .
NuITP> load examples/qlock/qlock.nuitp
```

## How to cite

If you use DM-Check, please cite the JLAMP article. Work that uses invariant generation should also cite the WRLA 2026 paper.

```bibtex
@article{dmcheck-jlamp,
  author  = {Bae, Kyungmin and Escobar, Santiago and L{\'o}pez-Rueda, Ra{\'u}l and Meseguer, Jos{\'e} and Sapi{\~n}a, Julia},
  title   = {{DM-Check}: Verifying Invariants of Concurrent Systems by Deductive Model Checking},
  journal = {Journal of Logical and Algebraic Methods in Programming},
  volume  = {149},
  pages   = {101107},
  year    = {2026},
  doi     = {10.1016/j.jlamp.2025.101107}
}

@inproceedings{dmcheck-wrla,
  author    = {Bae, Kyungmin and Escobar, Santiago and L{\'o}pez-Rueda, Ra{\'u}l and Meseguer, Jos{\'e} and Sapi{\~n}a, Julia},
  title     = {Verifying Invariants by Deductive Model Checking},
  booktitle = {Rewriting Logic and Its Applications},
  series    = {Lecture Notes in Computer Science},
  volume    = {14953},
  pages     = {3--21},
  publisher = {Springer},
  year      = {2024},
  doi       = {10.1007/978-3-031-65941-6_1}
}

@inproceedings{dmcheck-wrla2026,
  author    = {Bae, Kyungmin and Escobar, Santiago and L{\'o}pez-Rueda, Ra{\'u}l and Meseguer, Jos{\'e} and Sapi{\~n}a, Julia},
  title     = {Generating Invariants by Deductive Model Checking},
  booktitle = {Pre-proceedings of the 16th International Workshop on Rewriting Logic and its Applications},
  pages     = {96--114},
  year      = {2026},
  url       = {https://wrla2026.github.io/preproceedings/preproceedings.pdf},
  note      = {Pre-proceedings}
}
```

## Team

DM-Check is developed by the ELP group at the VRAIN institute of the Universitat Politècnica de València, in collaboration with international researchers in formal methods and rewriting logic. The team:

- José Meseguer, University of Illinois at Urbana-Champaign
- Santiago Escobar, Universitat Politècnica de València
- Kyungmin Bae, Pohang University of Science and Technology (POSTECH)
- Raúl López Rueda, Universitat Politècnica de València

For questions and bug reports, use the [contact page](https://dmcheck.webs.upv.es/contact.html) or open an issue in this repository.

## License

DM-Check and the bundled NuITP are free software: you can redistribute them and/or modify them under the terms of the GNU General Public License, version 3 or (at your option) any later version. See [`LICENSE`](LICENSE) and the copyright notice at the top of each file.
