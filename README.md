# DM-Check: A Deductive Model Checker

DM-Check is an interactive deductive model checker for concurrent systems specified as rewrite theories in [Maude](https://maude.cs.illinois.edu/). It combines symbolic exploration by constrained narrowing with inductive theorem proving in [NuITP](https://nuitp.webs.upv.es/) to verify and generate inductive invariants of infinite-state systems.

- **Website:** <https://dmcheck.webs.upv.es/>
- **User manual:** [DM-Check User Manual (PDF)](https://dmcheck.webs.upv.es/docs/dm-check-user-manual.pdf)
- **Latest release:** Public Alpha 12 (26 October 2026)

## Features

- **Invariant verification:** check that a symbolic set of states, written as a disjunction of constrained patterns, is closed under the transitions of the system (`check ind-invariant`).
- **Invariant generation:** generate an inductive invariant level by level from the initial states, generalizing the patterns of the frontier (`generate ind-invariant`, `generate next level`).
- **Symbolic reasoning:** check containment between symbolic sets of states (`check ... subsumed by ...`), for instance that the initial states satisfy an invariant or that no state is a deadlock, and check that two sets do not intersect (`intersect ... with ...`), for instance an invariant and a set of error states.
- **Interactive proofs:** when automation reaches its limit, continue by hand with lemmas, constructor case splits, variant unification, narrowing of auxiliary terms, variable abstraction, substitutions, Boolean splits and generalization.
- **Proof obligations:** the semantic obligations that are not discharged automatically are reported and can be proved with the bundled NuITP.
- **Proof scripts:** replay a verification session from a `.dmc` script and save the accepted commands of a session as a script.
- **Conditional rules:** rewrite rules may be conditional, with conditions that are conjunctions of equations.

## Requirements

- [Maude](https://github.com/maude-lang/Maude/releases). The current release is tested with Maude Alpha 165.
- On Windows, run Maude through the Windows Subsystem for Linux (WSL).

NuITP is included in the distribution (`NuITP.maude`) and does not require a separate installation.

## Installation

Download the latest release from the [DM-Check installation page](https://dmcheck.webs.upv.es/install.html) or clone this repository. The distribution contains:

- `dm-check.maude` — the DM-Check implementation.
- `NuITP.maude` — the compatible NuITP release. `dm-check.maude` loads it from its own directory: keep both files together.
- `examples/` — the case studies.

Start Maude from the installation directory, load your specification, then load DM-Check and select the module:

```console
$ maude -allow-files
Maude> load my-system.maude
Maude> load dm-check.maude
DM-Check> set module MY-SYSTEM .
```

The `-allow-files` option (or `-trust`) is only needed to read and write script files with `load` and `save proof`.

## Quick Start

The Readers–Writers example in `examples/rw/` checks an inductive invariant and deadlock freedom:

```console
$ maude -allow-files
Maude> load examples/rw/rw.maude
Maude> load dm-check.maude
DM-Check> set module R&W .
DM-Check> check ind-invariant < N:Natural , 0 > | true \/ < 0 , s(0) > | true .

  Invariant satisfied.

DM-Check> check < N:Natural , 0 > | true \/ < 0 , s(0) > | true subsumed by < 0, 0 > | true \/ < R:Natural, s(W:Natural) > | true \/ < R:Natural, 0 > | true \/ < s(R:Natural), W:Natural > | true .

  Subsumption satisfied.
```

The first command verifies that the symbolic state set is closed under the system transitions; to establish an inductive invariant, the initial states must also be shown to belong to the set. The second one checks that every state of the set is covered by the left-hand side of some rule, so no reachable state is a deadlock.

At the prompt, each command goes on one line. The same commands are stored in `examples/rw/rw.dmc`, where long commands are split with `\` at the end of the line, and can be replayed with:

```console
DM-Check> load examples/rw/rw.dmc
```

## Main Commands

A constrained pattern is written `t | C`, where `t` is a constructor term with variables and `C` is a conjunction of equations (or `true`). A symbolic set of states `φ` is a disjunction of constrained patterns separated by `\/`.

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

The user manual gives the exact behavior of each command, its side conditions and the proof obligations it may generate.

## Writing Specifications

DM-Check analyzes topmost rewrite theories. In short:

- Mark the rewrite rules used for symbolic exploration with the `narrowing` attribute. Conditional rules may only have equational conditions.
- Declare the constructors with the `ctor` attribute. Build the terms of the constrained patterns and the right-hand sides of the rules from constructors and operators defined by `variant` equations; other defined functions go in the constraints.
- Start the specification with `set include BOOL off .`, as the examples do, and give the operators an RPO order with the `rpo` attribute for NuITP.
- `set module` rejects modules with `owise` equations, the built-in `_==_` or `_=/=_`, conditional rules with non-equational conditions, membership axioms or `idem` operators.

The complete list of requirements is in the user manual.

## Examples

Each folder of `examples/` contains a Maude specification, a `.dmc` script with the DM-Check commands and, when the proof leaves obligations, a `.nuitp` script that discharges them with NuITP. The case studies are described on the [website](https://dmcheck.webs.upv.es/).

```console
$ maude -allow-files
Maude> load examples/qlock/qlock.maude
Maude> load dm-check.maude
DM-Check> load examples/qlock/qlock.dmc
```

The paths given to `load` and `save proof` are relative to the directory of the loaded `dm-check.maude`. To prove the obligations with NuITP, start a new Maude session:

```console
$ maude -allow-files
Maude> load examples/qlock/qlock.maude
Maude> load NuITP.maude
Maude> erew in NuITP : init .
NuITP> load examples/qlock/qlock.nuitp
```

## Documentation

The [user manual](https://dmcheck.webs.upv.es/docs/dm-check-user-manual.pdf) covers:

- Supported Maude specifications and their requirements.
- DM-Check commands and symbolic proof procedures.
- Invariant verification and generation.
- Interactive proofs and proof obligations.
- Worked examples and case studies.

Additional information is available on the [DM-Check website](https://dmcheck.webs.upv.es/).

## Publications

If you use DM-Check in your research, please cite:

**Bae, K., Escobar, S., López-Rueda, R., Meseguer, J., and Sapiña, J.**
*DM-Check: Verifying Invariants of Concurrent Systems by Deductive Model Checking.*
Journal of Logical and Algebraic Methods in Programming, 149, 101107 (2026).
<https://doi.org/10.1016/j.jlamp.2025.101107>

For invariant generation, please also cite:

**Bae, K., Escobar, S., López-Rueda, R., Meseguer, J., and Sapiña, J.**
*Generating Invariants by Deductive Model Checking.*
In C. Rocha (ed.), 16th International Workshop on Rewriting Logic and Its Applications (WRLA 2026), Lecture Notes in Computer Science, Springer (to appear).
[Pre-proceedings version (PDF)](https://wrla2026.github.io/preproceedings/preproceedings.pdf)

The original presentation of the tool:

**Bae, K., Escobar, S., López-Rueda, R., Meseguer, J., and Sapiña, J.**
*Verifying Invariants by Deductive Model Checking.*
In Rewriting Logic and Its Applications (WRLA 2024), Lecture Notes in Computer Science 14953, pp. 3–21, Springer (2024).
<https://doi.org/10.1007/978-3-031-65941-6_1>

```bibtex
@article{bae2026dmcheck,
  author  = {Kyungmin Bae and Santiago Escobar and Ra{\'u}l L{\'o}pez-Rueda and Jos{\'e} Meseguer and Julia Sapi{\~n}a},
  title   = {{DM-Check}: Verifying Invariants of Concurrent Systems by Deductive Model Checking},
  journal = {Journal of Logical and Algebraic Methods in Programming},
  volume  = {149},
  pages   = {101107},
  year    = {2026},
  doi     = {10.1016/j.jlamp.2025.101107}
}

@inproceedings{bae2026generating,
  author    = {Kyungmin Bae and Santiago Escobar and Ra{\'u}l L{\'o}pez-Rueda and Jos{\'e} Meseguer and Julia Sapi{\~n}a},
  title     = {Generating Invariants by Deductive Model Checking},
  editor    = {Camilo Rocha},
  booktitle = {16th International Workshop on Rewriting Logic and Its Applications (WRLA 2026)},
  series    = {Lecture Notes in Computer Science},
  publisher = {Springer},
  year      = {2026},
  note      = {To appear}
}

@inproceedings{bae2024verifying,
  author    = {Kyungmin Bae and Santiago Escobar and Ra{\'u}l L{\'o}pez-Rueda and Jos{\'e} Meseguer and Julia Sapi{\~n}a},
  title     = {Verifying Invariants by Deductive Model Checking},
  booktitle = {Rewriting Logic and Its Applications (WRLA 2024)},
  series    = {Lecture Notes in Computer Science},
  volume    = {14953},
  pages     = {3--21},
  publisher = {Springer},
  year      = {2024},
  doi       = {10.1007/978-3-031-65941-6_1}
}
```

## Development Team

DM-Check is developed by researchers from the Universitat Politècnica de València, the University of Illinois Urbana-Champaign and POSTECH:

- José Meseguer — University of Illinois Urbana-Champaign
- Santiago Escobar — Universitat Politècnica de València
- Kyungmin Bae — Pohang University of Science and Technology (POSTECH)
- Raúl López Rueda — Universitat Politècnica de València

For questions or bug reports, please [contact us](https://dmcheck.webs.upv.es/contact.html) or open a GitHub issue.

## License

DM-Check and the bundled NuITP are free software, distributed under the GNU General Public License, version 3 or (at your option) any later version (GPL-3.0-or-later). See [LICENSE](LICENSE) and the copyright notice at the top of each file.
