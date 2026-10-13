
# DM-Check: A Deductive Model Checker

DM-Check is an interactive deductive model checker for concurrent systems specified as rewrite theories in [Maude](https://maude.cs.illinois.edu/). It combines symbolic exploration by constrained narrowing with inductive theorem proving in [NuITP](https://nuitp.webs.upv.es/) to verify and generate inductive invariants of infinite-state systems.

**Website:** https://dmcheck.webs.upv.es/  
**User manual:** [DM-Check User Manual (PDF)](https://dmcheck.webs.upv.es/docs/dm-check-user-manual.pdf)  
**Latest release:** Public Alpha 12 (13 October 2026)

## Features

- **Invariant verification:** Check inductive closure of symbolic state sets represented by disjunctions of constrained patterns.
- **Invariant generation:** Incrementally generate inductive invariants through symbolic exploration and generalization.
- **Symbolic reasoning:** Check subsumption and intersection of symbolic state sets.
- **Interactive proofs:** Apply lemmas, case splits, variant unification, narrowing, substitutions and generalization to complete proofs.
- **Proof obligations:** Generate proof obligations that can be discharged using the bundled NuITP.
- **Proof scripts:** Save and replay verification sessions using `.dmc` scripts.

## Requirements

- [Maude](https://github.com/maude-lang/Maude/releases) (tested with Maude Alpha 165).
- On Windows, Maude can be run through Windows Subsystem for Linux (WSL).

NuITP is included in the distribution and does not require a separate installation.

## Installation

Download the latest release from the [DM-Check installation page](https://dmcheck.webs.upv.es/install.html) or clone this repository.

The distribution includes:

- `dm-check.maude` — DM-Check implementation.
- `NuITP.maude` — Compatible version of NuITP.
- `examples/` — Example specifications and verification scripts.

Start Maude from the installation directory and load DM-Check:

```console
$ maude -allow-files
Maude> load dm-check.maude
```

The `-allow-files` option (or `-trust`) is required to load and save proof scripts.

## Quick Start

The following example checks an inductive invariant of the Readers–Writers system:

```console
$ maude -allow-files
Maude> load examples/rw/rw.maude
Maude> load dm-check.maude
DM-Check> set module R&W .
DM-Check> check ind-invariant < N:Natural , 0 > | true \/ < 0 , s(0) > | true .

  Invariant satisfied.
```

The command verifies that the specified symbolic state set is closed under the system transitions. To establish an inductive invariant, the initial states must also be shown to belong to this set.

The complete Readers–Writers verification can be reproduced using:

```console
DM-Check> load examples/rw/rw.dmc
```

Other examples are available in the `examples/` directory. Each case study includes a Maude specification and a `.dmc` script, together with a `.nuitp` script when additional proof obligations must be discharged.

## Documentation

The [user manual](https://dmcheck.webs.upv.es/docs/dm-check-user-manual.pdf) provides detailed information about:

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
https://doi.org/10.1016/j.jlamp.2025.101107

For invariant generation, please also cite:

**Bae, K., Escobar, S., López-Rueda, R., Meseguer, J., and Sapiña, J.**  
*Generating Invariants by Deductive Model Checking.*  
Pre-proceedings of the 16th International Workshop on Rewriting Logic and its Applications (WRLA 2026), pp. 96–114.  
[PDF](https://wrla2026.github.io/preproceedings/preproceedings.pdf)

## Development Team

DM-Check is developed by researchers from the Universitat Politècnica de València, the University of Illinois Urbana-Champaign, and POSTECH.

- José Meseguer — University of Illinois Urbana-Champaign
- Santiago Escobar — Universitat Politècnica de València
- Kyungmin Bae — POSTECH
- Raúl López Rueda — Universitat Politècnica de València

For questions or bug reports, please [contact us](https://dmcheck.webs.upv.es/contact.html) or open a GitHub issue.

## License

DM-Check and the bundled NuITP are distributed under the GNU General Public License, version 3 or later (GPL-3.0-or-later).

See [LICENSE](LICENSE) for details.
