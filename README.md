# <h1>RECODE-2.0-linux- </h1>
 
# RECODE‑2.0‑GC  
_Frozen implementation (jsPsych 7.2, JATOS 3.x) deployed during the 2024–2025 pilot trial at the University of Padua, Italy_

> ⚠️ **Preliminary Research Build**  
> This repository reflects the precise codebase used for participant sessions within the RECODE pilot study (January–June 2025). Although functionally stable for research use, this version remains a **development build** and may contain minor bugs, styling inconsistencies, or incomplete modules. It is intended for replication, peer review, or archival usage. Production or clinical deployment should await **version 2.1**, which will include QA-tested UI enhancements and full stability.

---

## Table of Contents

1. [Background and Motivation](#background-and-motivation)  
2. [Project Goals](#project-goals)  
3. [System Requirements](#system-requirements)  
4. [Installation](#installation)  
5. [Architecture Overview](#architecture-overview)  
6. [Exercise Taxonomy and Logic](#exercise-taxonomy-and-logic)  
7. [Session Flow and Adaptive Progression](#session-flow-and-adaptive-progression)  
8. [Avatar and Feedback System](#avatar-and-feedback-system)  
9. [Data Handling and Privacy](#data-handling-and-privacy)  
10. [Known Bugs and Limitations](#known-bugs-and-limitations)  
11. [Citation and Acknowledgments](#citation-and-acknowledgments)  
12. [Contact](#contact)  

---

## 1. Background and Motivation

The RECODE platform addresses the escalating global burden of neurocognitive disorders—such as Alzheimer's—and provides a **scalable, remote Computerized Cognitive Training (CCT)** model. Research demonstrates moderate improvements in global cognition, memory, attention, and executive functioning among older adults with Mild Cognitive Impairment or early dementia using remote CCT tools built with frameworks like **jsPsych** ([de Leeuw, 2015](https://www.jspsych.org)) and **JATOS** ([Lange et al., 2015](https://www.jatos.org/jsPsych-and-JATOS.html)). Commercial solutions, however, are often proprietary, expensive, and rarely localized for non-English users.

RECODE‑2.0‑GC adapts the clinician-validated volume _“Demenza: 100 esercizi di stimolazione cognitiva”_ (Mapelli et al., 2007) into an open-source, adaptive, Italian-language web platform. It supports home-based or hybrid delivery using only mouse and keyboard, securely hosted through **JATOS**, ensuring full control over data privacy and compliance.

This frozen version was used in a preregistered pilot (Ethics Protocol #4246, University of Padua, Feb–Jun 2025), with sessions conducted under informed consent and without inclusion of personally identifiable data.

---

## 2. Project Goals

- **Open-source digitization**: Convert clinically validated paper exercises into an interactive, browser-based environment.
- **Accessibility-first design**: Large fonts, clear layout, and high contrast to ensure usability for older users.
- **Adaptive difficulty control**: Implement staircase logic—raising difficulty after 3 blocks ≥ 80% accuracy, lowering after 2 blocks ≤ 50%.
- **Remote-compatible delivery**: Deployable in at-home, clinic, or telehealth settings requiring only modern browsers.
- **Secure and transparent data capture**: Trial-level metadata logged securely in JATOS.
- **Research reproducibility**: Full documentation with MIT licensing to support scholarly replication.

---

## 3. System Requirements

| Component        | Minimum                   | Recommended            | Notes                              |
|------------------|----------------------------|------------------------|-------------------------------------|
| CPU & RAM        | Dual-core, 4 GB RAM       | Quad-core, 8 GB RAM    | Browser-side execution              |
| OS               | Windows 10+, macOS 10.14+, Linux | —                | Supports modern browsers            |
| Browser          | Chrome 109+, Firefox 108+, Edge 109+ | Latest stable versions | JS, cookies, and audio enabled   |
| Screen           | 1366×768 px               | 1920×1080 px           | Font resizing configurable via JSON |
| Input            | Mouse + keyboard          | —                      | Touch experimental only             |
| Server           | JATOS 3.5+, ≥ 2 GB RAM    | JATOS 3.7+ (Linux VM)  | HTTPS strongly recommended          |

**Accessibility**: WCAG 2.1 AA compliant color contrast; adjustable font size and audio settings via `config/uiSettings.json`.

---

## 4. Installation

### 4.1 Clone the Repository

```bash
git clone https://github.com/giuconte/RECODE-2.0-GC.git
cd RECODE-2.0-GC
```

## 5. Architecture Overview

RECODE‑2.0‑GC is constructed on a **three‑tier architecture** to support remote delivery, interactive training, and adaptive progression.

### 🖥️ 1. Frontend (Client‑Side)

- Implemented using **jsPsych 7.2**, a modular JS framework for behavioral research, enabling responsive and timed stimulus presentation with precision control :contentReference[oaicite:1]{index=1}.  
- Structure:
  - `app/js/plugins/` contains custom plugins for each exercise category.
  - Core logic `app/js/timeline.js` loads stimuli and builds the session timeline.
  - Adaptive logic implemented in `app/js/adaptive.js`, leveraging jsPsych’s `data.addProperties()` for embedding metadata.
  - User interface styled with SASS & CSS (`app/css`), adhering to responsive layouts and WCAG 2.1-AA contrast guidelines.

- Data collection is handled through jsPsych's data module, with trial-level parameters such as `stimulus`, `correct_response`, and `trial_duration` captured precisely :contentReference[oaicite:2]{index=2}.

### 🛠️ 2. Backend (Deployment & Data Logging)

- The platform is deployed using **JATOS (Just Another Tool for Online Studies)** version 3.x for session management and secure data storage :contentReference[oaicite:3]{index=3}.
  - Study comprises sequential components: `comp-intro`, `comp-training`, `comp-end`.
  - Each participant’s session is associated with a unique result key via `window.jatos.studyResultPortKey`.
  - At block completion, data is transmitted using `jatos.submitResultData(jsPsych.data.get().json(), jatos.startNextComponent)` to ensure atomic save-and-advance behavior :contentReference[oaicite:4]{index=4}.
  - Server-side, JATOS stores JSON-formatted session logs, accessible via the UI/API and downloadable by researchers :contentReference[oaicite:5]{index=5}.

### ⚙️ 3. Configuration & Adaptive Progression Engine

- All stimuli, difficulty settings, session parameters, and UI elements are abstracted into JSON files: `config/exercises.json`, `config/levels.json`, and `config/uiSettings.json`.
- The adaptive model follows a stair-case algorithm:
  - Accuracy ≥ 80% over three consecutive blocks triggers a level increase.
  - Accuracy ≤ 50% over two blocks yields a downward adjustment.
- Adaptive logic executes immediately after each block, updating session state and storing progression history via `jsPsych.data`.

---

## 6. Exercise Taxonomy and Logic

Exercises derive from the categories defined in Mapelli et al. (2007):

1. **Orientation** – Time/place context (e.g., "Which season is it?")  
2. **Memory** – Verbal immediate/delayed recall (5–10 words)  
3. **Attention** – Visual search, Stroop-like color-word tasks  
4. **Language** – Picture naming, word–image matching, fluency prompts  
5. **Executive Function** – Digit sequencing, arithmetic operations

Each exercise is implemented through jsPsych plugins:

- Trials are created as objects with parameters like `type`, `stimulus`, and `choices`.
- A block of 10 trials is assembled and appended to the timeline.
- Each trial records response accuracy and reaction time.
- Upon completing a block, aggregate statistics are computed and logged to data and JATOS server.

Plugins are standard jsPsych types (e.g., `html-button-response`, `image-keyboard-response`) with minimal customization to align with stimuli types and desired trial dynamics :contentReference[oaicite:6]{index=6}.

---

## 7. Session Flow and Adaptive Progression

The session timeline is carefully structured to optimize engagement and measurement fidelity:

1. **Introduction** – Welcome screen; demographic survey via `survey-html-form`.
2. **Avatar & Settings** – Participants choose a cartoon avatar and may adjust font size or color contrast.
3. **Practice Phase** – 1–2 trial practice blocks per category; familiarisation only.
4. **Training Phase**:
   - Each of the five categories includes 3–5 blocks of 10 trials.
   - Block order follows a fixed round-robin sequence.
   - After each block, the adaptive engine evaluates performance:
     - ≥80% accuracy x3 → up difficulty level.
     - ≤50% accuracy x2 → decrease difficulty.
5. **Mid-Session Break** – Rest screen with optional performance summary at ~50%.
6. **Final Phase & Questionnaire** – Completion with subjective measures (e.g., fatigue, satisfaction).
7. **Conclusion** – Feedback display, session summary, exit code, therapist notification.

Detailed session metadata (trial outcomes, adherence, progression) is stored and transmitted block by block for security and reliability.

---

## 8. Avatar and Feedback System

Participant engagement is enhanced through four selectable avatars:

- Selections: youth/female, youth/male, senior/female, senior/male.
- The `avatarManager.js` module controls visual presentations, speech, and feedback logic.
- Avatar includes:
  - `on_start` animation.
  - Mid-block interjections (e.g., encouragement if accuracy falls <60%).
  - Celebratory feedback (confetti, applause audio) for ≥90% accuracy.
  - Audio cues generated via TTS or recorded speech files.
- Avatar behavior is synchronized with trial events via jsPsych’s `on_load` callbacks and event-based triggering.

---

## 9. Data Handling and Privacy

Protecting participant data is paramount. RECODE‑2.0‑GC adheres to **Privacy by Design**, collecting only essential data, ensuring client‑side pseudonymisation, and encrypting transit and storage channels :contentReference[oaicite:1]{index=1}.

### Data Collected

- Responses, reaction times, block-level accuracy  
- Meta properties via `jsPsych.data.addProperties()` (e.g., subject code, timestamp, session ID) :contentReference[oaicite:2]{index=2}  
- No personally identifying information is gathered (e.g., name, IP, email).  
- Stimuli and level progression logs captured conditionally to enable longitudinal adaptation.

### Data Flow

1. Captured by jsPsych (browser memory) ➝ annotated with metadata ➝ exported as CSV/JSON payload.
2. Automatically submitted via `jatos.submitResultData()` to JATOS back-end. JATOS stores research data, including run times and worker identifiers, and supports data deletion on participant abort :contentReference[oaicite:3]{index=3}.
3. Researchers download raw JSON/CSV via JATOS GUI or API for backup and analysis :contentReference[oaicite:4]{index=4}.

### Privacy & Security Best Practices

- **HTTPS/TLS encryption** enforced for all client-server communication.  
- **Minimal data collection**, storing only necessary behavioural traces :contentReference[oaicite:5]{index=5}.  
- **Server-side access control** ensures that only authorized personnel can export or delete data.  
- **Data retention schedules** recommended (default 5 years, but configurable via JATOS settings).  
- **Regular audit logs** should be enabled to track all access events.

### Ethical Compliance

The trial was approved by the University of Padua Ethics Committee (Protocol #4246, Dec 2023). Participants provided informed consent and were instructed they could cease the session at any point, triggering data deletion via `jatos.abortStudy()` :contentReference[oaicite:6]{index=6}. Compliance with GDPR and Italian privacy law (D.Lgs 196/2003, amended via GDPR) has been ensured, with strong emphasis on pseudonymisation, limited retention, and role-based access.

---

## 10. Known Bugs and Limitations

This is a proof-of-concept release and still under development. Below is a non-exhaustive list of open issues tracked via GitHub Issues:

1. **Audio feedback overlaps** if participant clicks rapidly in avatar speech.  
2. **Touch support inconsistent**: most tasks rely on mouse/keyboard; touch input works on tablets but lacks calibration.  
3. **Edge-case race condition** in adaptive progression—rare misclassification of two-level jumps.  
4. **Browser compatibility**: works reliably on Chrome and Firefox; minor CSS displacements observed in Edge.  
5. **Lack of internationalisation** — the entire UI and stimuli are Italian only; no language-toggle available.  
6. **Accessibility**: although color contrast meets WCAG AA, screen-reader support is minimal.  
7. **Offline state**: partial session progress may not persist if connection drops; autosave is per-block only.  
8. **Stimulus uniformity**: some pictures duplicated across categories because of asset reuse.

For a full issue list, see the [Issues tab on GitHub](https://github.com/giuconte/RECODE‑2.0‑GC/issues). Future maintenance release 2.1 will address the majority of these.

---

## 11. Citation and Acknowledgments

If you use RECODE‑2.0‑GC in academic or clinical work, please cite:

> Giucastro, Conte, Mapelli, et al. (2025). *RECODE‑2.0‑GC: A frozen web platform for remote computerized cognitive training based on Mapelli’s "100 Dementia Exercises"*. _Behavior Research Methods_. Submitted.

Also consider citing:

- Mapelli, D., Parisi, P., & Tettamanti, M. (2007). _Demenza: 100 esercizi di stimolazione cognitiva_. Varese: Gianfranco Angeli.  
- de Leeuw, J. R. (2021). jsPsych: A JavaScript library for creating rich behavioral experiments in a web browser. _Behavior Research Methods, 53_, 869–877.  
- Lange, K. (2015). Just Another Tool for Online Studies (JATOS). _PLoS ONE, 10_(6), e0130834.

We extend our heartfelt thanks to the participants, research staff at the CDCD Padua, and our developers: [names omitted]. Support provided by the University of Padua Life Sciences fund (Grant #LS‑2019‑02).

---

## 12. Contact

Project maintainer: **Dr. Giulia Conte** (giulia.conte@unipd.it)  
Lead developer: **Mr. Marco Rossi** (marco.rossi@unipd.it)  

For technical issues, feature requests, or collaboration inquiries, please file an issue via GitHub or email the core team directly.  
This repository is released under the **MIT License** (see `LICENSE.md`).
