# RECODE‑2.0-linux-

*Frozen implementation (jsPsych 7.2, JATOS 3.x) deployed during the 2024–2025 pilot trial at the University of Padua, Italy*

> ⚠️ **Preliminary Research Build**
> This repository reflects the precise codebase used for participant sessions within the RECODE pilot study (January–June 2025). Although functionally stable for research use, this version remains a **development build** and may contain minor bugs, styling inconsistencies, or incomplete modules. It is intended for replication, peer review, or archival usage. Production or clinical deployment should await **version 2.1**, which will include QA-tested UI enhancements and full stability.

---

## Table of Contents

1. [Background and Motivation](#1-background-and-motivation)
2. [Project Goals](#2-project-goals)
3. [System Requirements](#3-system-requirements)
4. [Installation](#4-installation)
5. [Architecture Overview](#5-architecture-overview)
6. [Exercise Taxonomy and Logic](#6-exercise-taxonomy-and-logic)
7. [Session Flow and Adaptive Progression](#7-session-flow-and-adaptive-progression)
8. [Avatar and Feedback System](#8-avatar-and-feedback-system)
9. [Data Handling and Privacy](#9-data-handling-and-privacy)
10. [Known Bugs and Limitations](#10-known-bugs-and-limitations)
11. [Citation and Acknowledgments](#11-citation-and-acknowledgments)
12. [Contact, Author Contributions, and Funding](#12-contact-author-contributions-and-funding)

---

## 1. Background and Motivation

The RECODE platform addresses the escalating global burden of neurocognitive disorders—such as Alzheimer's—and provides a **scalable, remote Computerized Cognitive Training (CCT)** model. Research demonstrates moderate improvements in global cognition, memory, attention, and executive functioning among older adults with Mild Cognitive Impairment or early dementia using remote CCT tools built with frameworks like [jsPsych](https://www.jspsych.org) ([de Leeuw, 2015](https://doi.org/10.3758/s13428-020-01586-y)) and [JATOS](https://www.jatos.org) ([Lange, 2015](https://doi.org/10.1371/journal.pone.0130834)).

RECODE‑2.0‑GC adapts the clinician-validated volume *“Demenza: 100 esercizi di stimolazione cognitiva”* ([Mapelli et al., 2007](https://www.amazon.it/Demenza-stimolazione-cognitiva-elettroniche-disponibili/dp/886030153X)) into an open-source, adaptive, Italian-language web platform. This platform was used in a preregistered pilot (Ethics Protocol #4246, University of Padua, Feb–Jun 2025), with sessions conducted under informed consent and without collection of personally identifiable data.

---

## 2. Project Goals

* **Open-source digitization**: Convert clinically validated paper exercises into an interactive, browser-based environment.
* **Accessibility-first design**: Large fonts, clear layout, and high contrast to ensure usability for older users.
* **Adaptive difficulty control**: Implement staircase logic—raising difficulty after 3 blocks ≥ 80% accuracy, lowering after 2 blocks ≤ 50%.
* **Remote-compatible delivery**: Deployable in at-home, clinic, or telehealth settings requiring only modern browsers.
* **Secure and transparent data capture**: Trial-level metadata logged securely in JATOS.
* **Research reproducibility**: Full documentation with MIT licensing to support scholarly replication.

---

## 3. System Requirements

| Component | Minimum                              | Recommended            | Notes                               |
| --------- | ------------------------------------ | ---------------------- | ----------------------------------- |
| CPU & RAM | Dual-core, 4 GB RAM                  | Quad-core, 8 GB RAM    | Browser-side execution              |
| OS        | Windows 10+, macOS 10.14+, Linux     | —                      | Supports modern browsers            |
| Browser   | Chrome 109+, Firefox 108+, Edge 109+ | Latest stable versions | JS, cookies, and audio enabled      |
| Screen    | 1366×768 px                          | 1920×1080 px           | Font resizing configurable via JSON |
| Input     | Mouse + keyboard                     | —                      | Touch experimental only             |
| Server    | JATOS 3.5+, ≥ 2 GB RAM               | JATOS 3.7+ (Linux VM)  | HTTPS strongly recommended          |

**Accessibility:** WCAG 2.1 AA compliant color contrast; adjustable font size and audio settings via `config/uiSettings.json`.

---

## 4. Installation

### 4.1 Clone the Repository

```bash
git clone https://github.com/giuconte/RECODE-2.0-GC.git
cd RECODE-2.0-GC
```

### 4.2 Deploy via JATOS

1. Access your JATOS instance (`https://<server>:<port>`).
2. Go to **Studies → Import Study** and upload `recode2.0_frozen.jzip`.
3. Configure single-use participant links.
4. Set run limits in `global.conf` (e.g., `maxRunsPerParticipant = 1`).
5. Instruct participants to complete the session in a single sitting (30–45 minutes).

### 4.3 Local Testing (Optional)

```bash
npm install -g http-server
http-server ./app -p 8080
```

* Access via `http://localhost:8080`; data saved in `app/data/local_debug.csv`.

---

## 5. Architecture Overview

**Frontend:**

* jsPsych 7.2 (modular plugins in `app/js/plugins/`)
* Timeline and adaptive logic (`app/js/timeline.js`, `app/js/adaptive.js`)
* Responsive SASS/CSS

**Backend:**

* JATOS 3.x for data storage/session management
* Study segmented: `comp-intro`, `comp-training`, `comp-end`
* Data transmitted via `jatos.submitResultData()`, stored in JSON

**Configuration:**

* JSON files: `config/exercises.json`, `config/levels.json`, `config/uiSettings.json`
* Adaptive progression engine (staircase: up ≥80%, down ≤50%)

---

## 6. Exercise Taxonomy and Logic

* **Orientation:** Time/place context
* **Memory:** Immediate/delayed verbal recall
* **Attention:** Visual search, Stroop-like tasks
* **Language:** Naming, matching, fluency
* **Executive Function:** Sequencing, arithmetic

Each implemented via jsPsych plugin, with block-based accuracy and RT logging.

---

## 7. Session Flow and Adaptive Progression

1. **Introduction:** Welcome, demographics
2. **Avatar & Settings:** Avatar choice, font/contrast adjustments
3. **Practice Phase:** 1–2 sample trials per category
4. **Training Phase:** 3–5 blocks per category, adaptive progression
5. **Mid-Session Break:** Rest, optional feedback
6. **Questionnaire:** Subjective measures
7. **Conclusion:** Feedback, code, therapist notification

---

## 8. Avatar and Feedback System

* 4 avatars (youth/senior x male/female)
* Visual, auditory, and celebratory feedback
* TTS/recorded audio cues
* Synchronized via jsPsych `on_load` and events

---

## 9. Data Handling and Privacy

* No personal data collected
* All session data pseudonymised, transferred securely via HTTPS/TLS
* Data retention, deletion, and access handled by JATOS policies
* Approved by Ethics Committee (Protocol #4246, Dec 2023)
* GDPR and Italian privacy law compliant

---

## 10. Known Bugs and Limitations

* Audio feedback overlap possible
* Touch input inconsistent
* Edge-case progression logic
* Minor browser CSS issues
* No internationalisation (Italian only)
* Limited screen reader support
* Autosave is per-block
* Stimulus reuse in some categories

See [GitHub Issues](https://github.com/giuconte/RECODE-2.0-GC/issues) for details.

---

## 11. Citation and Acknowledgments

If you use RECODE‑2.0‑GC, please cite:

> Ravelli, A.*, Livoti, V.*, Contemori, G., Macchia, E., Romeo, Z., Noale, M., Lucchi, E., Ghilardotti, G., Morandi, A., De Rui, M., Sergi, G., Maggi, S., Mapelli, D., Bonato, M., & Devita, M. (2025).
> RECODE Pilot Study: Preliminary Testing of a New Open, Computer-Based Platform for Cognitive Stimulation in Italian. *Behavior Research Methods*. Submitted.

Also cite:

* Mapelli, D., Parisi, P., Mondini, S., Iannizzi, P., & Bergamaschi, S. (2007). *Demenza: 100 esercizi di stimolazione cognitiva* \[with CD-ROM]. Varese: Raffaello Cortina Editore. ISBN 978-88-6030-153-6.
* de Leeuw, J. R. (2021). jsPsych: A JavaScript library for creating rich behavioral experiments in a web browser. *Behavior Research Methods, 53*, 869–877. [https://doi.org/10.3758/s13428-020-01586-y](https://doi.org/10.3758/s13428-020-01586-y)
* Lange, K. (2015). Just Another Tool for Online Studies (JATOS): An easy solution for setup and management of web servers supporting online studies. *PLOS ONE, 10*(6), e0130834. [https://doi.org/10.1371/journal.pone.0130834](https://doi.org/10.1371/journal.pone.0130834)

**Acknowledgments**
We thank all participants and caregivers, the clinical/administrative team at CDCD Padua, and our development team.
Supported by the University of Padua Life Sciences Fund (Grant #LS-2019-02), Department of General Psychology, Geriatrics Unit (DIMED), CNR, Cremona Solidale, University of Brescia.

---

## 12. Contact, Author Contributions, and Funding

**Authors and Institutional Affiliations:**

* **Adele Ravelli\***, Geriatrics Unit, DIMED, University of Padua, Italy – [adele.ravelli@studenti.unipd.it](mailto:adele.ravelli@studenti.unipd.it), ORCID: 0000-0002-6345-6836
* **Vincenzo Livoti\***, PNC/Department of General Psychology, University of Padua, Italy – [vincenzo.livoti@phd.unipd.it](mailto:vincenzo.livoti@phd.unipd.it), ORCID: 0009-0003-6261-5422
* **Giulio Contemori** (corresponding), Department of General Psychology, University of Padua – [giulio.contemori@unipd.it](mailto:giulio.contemori@unipd.it), ORCID: 0000-0002-1873-1472
* (…other authors as in previous drafts…)

\*These authors contributed equally.

**Funding**
University of Padua Life Sciences grant (#LS‑2019‑02), with support from Department of General Psychology, Geriatrics Unit (DIMED), CNR, Cremona Solidale, and University of Brescia.

**Ethics**
Approved by the University of Padua Ethics Committee (Protocol 4246). Conducted per the Declaration of Helsinki.

**License**
MIT License (see `LICENSE.md`).

For technical questions, open a [GitHub issue](https://github.com/giuconte/RECODE-2.0-GC/issues) or email the corresponding author.

