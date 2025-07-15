Certainly. Here is a **fully updated, academic, continuous-prose README.md** for your project, using the structure and improvements discussed above. This version integrates all corrections, minimizes excessive bulleting, uses proper Markdown (so that rendering is uniform on GitHub and PDF), and retains maximal academic and narrative clarity. Pagination is handled via a numbered table of contents and clear section headings, which are automatically recognized by GitHub for navigation.

---

# RECODE-2.0-linux-

*Frozen implementation (jsPsych 7.2, JATOS 3.x) deployed during the 2024–2025 pilot trial at the University of Padua, Italy*

> ⚠️ **Preliminary Research Build**
> This repository contains the exact codebase used for participant sessions in the RECODE pilot study (January–June 2025, University of Padua). Although functionally stable for research, this version remains a development build and may include minor bugs, stylistic inconsistencies, or incomplete modules. It is intended for replication, peer review, and archival use only. Production or clinical deployment should await version 2.1, which will feature full QA, expanded accessibility, and enhanced reliability.

---

## Table of Contents

1. [Background and Motivation](#background-and-motivation)
2. [Project Goals](#project-goals)
3. [System Requirements](#system-requirements)
4. [Installation and Quickstart](#installation-and-quickstart)
5. [Architecture Overview](#architecture-overview)
6. [Exercise Taxonomy and Logic](#exercise-taxonomy-and-logic)
7. [Session Flow and Adaptive Progression](#session-flow-and-adaptive-progression)
8. [Avatar and Feedback System](#avatar-and-feedback-system)
9. [Data Handling and Privacy](#data-handling-and-privacy)
10. [Known Bugs and Limitations](#known-bugs-and-limitations)
11. [Citation and Acknowledgments](#citation-and-acknowledgments)
12. [Contact, Author Contributions, and Funding](#contact-author-contributions-and-funding)

---

## 1. Background and Motivation

RECODE-2.0-GC addresses the global challenge of cognitive decline in aging, specifically focusing on mild cognitive impairment and early dementia. Computerized Cognitive Training (CCT) offers scalable, non-pharmacological interventions with modest but robust benefits for memory, attention, executive functioning, and overall cognitive health. However, many existing CCT tools are proprietary, lack transparency, and are rarely localized for the Italian context. RECODE-2.0-GC overcomes these barriers by translating and adapting the validated volume *Demenza: 100 esercizi di stimolazione cognitiva* (Mapelli et al., 2007) into a web-native, open-source, Italian-language platform.

The platform supports remote or hybrid delivery, requiring only a modern browser and standard input devices, and is designed for deployment on institutional infrastructure using JATOS, guaranteeing secure data capture and ethical compliance. This repository preserves the "frozen" code version used in the preregistered pilot at Padua's Cognitive Disorders Centre (Ethics Protocol #4246, February–June 2025), with all sessions conducted under informed consent and with rigorous protection of participant privacy.

---

## 2. Project Goals

RECODE-2.0-GC was conceived to digitize and adapt established cognitive stimulation exercises, ensuring accessibility for older adults with neurocognitive decline. Its core aims include the open-source translation of pencil-and-paper protocols into modular web tasks, the implementation of accessible interfaces with high-contrast, large-font settings, and the deployment of adaptive difficulty mechanisms to sustain participant engagement. The platform was developed for seamless use in home, clinic, and telehealth environments, capturing granular behavioral data securely via JATOS. By prioritizing reproducibility and open-science values, RECODE-2.0-GC is intended to support rigorous research, transparent audit, and broad dissemination.

---

## 3. System Requirements

The platform is designed to run efficiently on widely available hardware. Minimum client-side requirements are a dual-core processor and 4 GB RAM, while the recommended specification is a quad-core machine with at least 8 GB RAM. Supported operating systems include Windows 10 or later, macOS 10.14 or later, and modern Linux distributions, provided they are running a compatible browser (Chrome 109+, Firefox 108+, or Edge 109+). The platform adapts to screen sizes down to 1366×768 pixels, though 1920×1080 is preferred. All core interaction is via mouse and keyboard; touch input is experimental. Institutional deployment requires JATOS 3.5+ (preferably 3.7+) with at least 2 GB RAM and an HTTPS endpoint. All interface settings, including font size and contrast, can be customized via `config/uiSettings.json`, with full adherence to WCAG 2.1 AA accessibility guidelines.

---

## 4. Installation and Quickstart

Begin by cloning the repository:

```bash
git clone https://github.com/giuconte/RECODE-2.0-GC.git
cd RECODE-2.0-GC
```

To deploy the platform, import the provided JATOS study bundle (`recode2.0_frozen.jzip`) into your JATOS instance, available at `https://<yourserver>:<port>`. Single-use participant links are generated and managed by JATOS, ensuring session isolation. All participant runs should be completed in a single session of 30–45 minutes.

For local testing or development, use a static server:

```bash
npm install -g http-server
http-server ./app -p 8080
```

The application will be accessible at `http://localhost:8080`, but in this mode, data are only written to local CSV for debugging and are not secured or session-managed.

---

## 5. Architecture Overview

RECODE-2.0-GC implements a three-tier structure: the browser-based frontend, the JATOS backend for data capture and management, and a configuration engine enabling dynamic adaptation. The frontend is built with jsPsych 7.2, using modular plugins for each cognitive task domain and a CSS/SASS-driven UI fully compliant with accessibility standards. Core logic is separated into timeline management (`app/js/timeline.js`), task plugin logic (`app/js/plugins/`), and adaptive control (`app/js/adaptive.js`). All configuration—including stimuli, difficulty, and appearance—is centralized in JSON files.

The backend leverages JATOS to manage study flow, data collection, and secure participant handling. Each session comprises sequential components (introduction, training, debrief), with participant progress tracked and data written atomically at each block via `jatos.submitResultData(...)`. Data are stored as JSON and CSV, accessible to authorized research staff via the JATOS interface.

---

## 6. Exercise Taxonomy and Logic

All exercises are derived from the clinical categories in Mapelli et al. (2007), spanning orientation (temporal/spatial awareness), memory (immediate and delayed recall), attention (visual search, Stroop-like paradigms), language (naming, word-image association, fluency), and executive function (digit span, arithmetic). Each domain is implemented as a series of jsPsych plugins, parameterized via JSON and organized into blocks of 10 trials. The system logs participant responses, reaction times, and block-level accuracy, with aggregate statistics computed for adaptive progression. All plugins are based on jsPsych core types, with targeted customization to ensure precise stimulus presentation and response collection.

---

## 7. Session Flow and Adaptive Progression

A standard session begins with participant onboarding and demographic data collection, followed by avatar selection and configuration of interface preferences (font size, contrast, audio). Each domain is introduced via a brief practice block to ensure familiarity. Main training is organized into 3–5 blocks per domain, with a round-robin sequence and mid-session rest. The adaptive staircase mechanism continuously monitors performance: three consecutive blocks at or above 80% accuracy prompt a difficulty increase, while two blocks at or below 50% trigger a decrease. Each session concludes with subjective feedback collection (fatigue, satisfaction), a session summary, and notification for the supervising clinician. All behavioral and progression data are securely stored after each block, supporting detailed longitudinal analysis.

---

## 8. Avatar and Feedback System

Participant engagement is maintained via an avatar system allowing choice among four visual identities (young/male, young/female, senior/male, senior/female). The avatar manager coordinates on-screen animations, verbal and visual feedback, and motivational cues. Positive performance triggers celebratory effects such as confetti and applause, while lower accuracy results in constructive encouragement. All avatar behavior is synchronized with the trial timeline through jsPsych event hooks, ensuring seamless integration and maximizing engagement for older users.

---

## 9. Data Handling and Privacy

The platform implements strict privacy by design principles. Only essential behavioral data (trial responses, timing, and progression) are collected, and all metadata are pseudonymized at the client level. No personally identifying information (such as name, IP, or email) is stored. Data transmission is encrypted (HTTPS/TLS), and access is restricted to authorized research staff via JATOS. All session logs are available in both JSON and CSV formats for subsequent analysis, with full traceability and audit. Ethical compliance is maintained through approval by the University of Padua Ethics Committee (Protocol #4246) and adherence to GDPR and Italian privacy law. Participants may abort sessions at any point, with immediate deletion of any partial data.

---

## 10. Known Bugs and Limitations

As a research release, this build may exhibit minor bugs or incomplete features. Rapid sequential clicks can cause overlapping audio feedback. Touch input is only partially supported and not intended for clinical use. Rare race conditions may misclassify adaptive progression, though these do not impact data integrity. The platform has been validated primarily in Chrome and Firefox; some display issues may occur in other browsers. At present, all content is in Italian, and internationalization is not yet implemented. Accessibility for screen readers is limited, though color contrast and font size conform to current guidelines. If a network interruption occurs, only data from completed blocks are saved. Some visual stimuli may be duplicated across categories. Open issues and progress toward version 2.1 are tracked on the project’s [GitHub Issues page](https://github.com/giuconte/RECODE-2.0-GC/issues).

---

## 11. Citation and Acknowledgments

If you use RECODE-2.0-GC for research or clinical purposes, please cite:

Ravelli, A.*, Livoti, V.*, Contemori, G., Macchia, E., Romeo, Z., Noale, M., Lucchi, E., Ghilardotti, G., Morandi, A., De Rui, M., Sergi, G., Maggi, S., Mapelli, D., Bonato, M., & Devita, M. (2025). RECODE Pilot Study: Preliminary Testing of a New Open, Computer-Based Platform for Cognitive Stimulation in Italian. Behavior Research Methods. Submitted.

Additionally, cite:

Mapelli, D., Parisi, P., Mondini, S., Iannizzi, P., & Bergamaschi, S. (2007). Demenza: 100 esercizi di stimolazione cognitiva \[with CD-ROM]. Varese, Italy: Raffaello Cortina Editore. ISBN 978-88-6030-153-6.

de Leeuw, J. R. (2021). jsPsych: A JavaScript library for creating rich behavioral experiments in a web browser. Behavior Research Methods, 53, 869–877. [https://doi.org/10.3758/s13428-020-01586-y](https://doi.org/10.3758/s13428-020-01586-y)

Lange, K. (2015). Just Another Tool for Online Studies (JATOS): An easy solution for setup and management of web servers supporting online studies. PLOS ONE, 10(6), e0130834. [https://doi.org/10.1371/journal.pone.0130834](https://doi.org/10.1371/journal.pone.0130834)

We express our sincere gratitude to all participants and caregivers, the clinical and administrative staff at CDCD Padua, and the entire development team. This work was supported by the University of Padua Life Sciences Fund (Grant #LS-2019-02), with additional contributions from the Department of General Psychology, the Geriatrics Unit (DIMED), CNR, Cremona Solidale, and the University of Brescia.

---

## 12. Contact, Author Contributions, and Funding

The RECODE-2.0-GC project is the result of a multi-institutional collaboration. The full author list is as follows: Adele Ravelli (Geriatrics Unit, DIMED, University of Padua), Vincenzo Livoti (Padua Neuroscience Center; Department of General Psychology, University of Padua), Giulio Contemori (Department of General Psychology, University of Padua; corresponding author: [giulio.contemori@unipd.it](mailto:giulio.contemori@unipd.it)), Eleonora Macchia, Zaira Romeo, Marianna Noale, Stefania Maggi (Neuroscience Institute, Aging Branch, National Research Council - CNR, Padua), Elena Lucchi, Giorgia Ghilardotti (Cremona Solidale), Alessandro Morandi (University of Brescia; Cremona Solidale), Marina De Rui, Giuseppe Sergi (Geriatrics Unit, DIMED, University of Padua), Daniela Mapelli, Mario Bonato, Maria Devita (Department of General Psychology, University of Padua).

ORCID identifiers and contact details are provided for all senior authors and are available upon request or in the manuscript. Adele Ravelli and Vincenzo Livoti contributed equally to this work. All correspondence should be addressed to Dr. Giulio Contemori, Department of General Psychology, University of Padua, Via Venezia 8, 35131 Padua, Italy ([giulio.contemori@unipd.it](mailto:giulio.contemori@unipd.it)).

The development and pilot evaluation of RECODE-2.0-GC were supported by the University of Padua Life Sciences grant (#LS-2019-02), with institutional support from the Department of General Psychology and the Geriatrics Unit (DIMED). The project also benefited from the collaboration and infrastructure provided by the National Research Council (CNR), Cremona Solidale, and the University of Brescia. Ethical approval was granted by the Ethics Committee for Psychological Research at the University of Padua (Protocol no. 4246), and all procedures complied with the Declaration of Helsinki and Italian privacy law.

This repository is distributed under the MIT License (see LICENSE.md). For technical issues, feature requests, or collaborative inquiries, please use the GitHub issue tracker or contact the corresponding author.

---

If you would like further narrative expansion or a specific formatting style (e.g., APA, Vancouver, BibTeX), or wish to include visual elements or quickstart code snippets, just specify your needs.
