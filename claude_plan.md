This is a rich field with a lot of interdisciplinary depth. Here's a complete map of everything you need to know, organized from foundations up to research frontiers, with targeted paper recommendations for each area.

---

## 1. CPS Foundations

Before diving into security, you need to genuinely understand what CPS *is* — not just as a buzzword but as an engineering discipline.

**Core concepts to master:**

The defining property of a CPS is the tight feedback loop between computation and physical processes. You need to understand how **control theory** underpins this: PID controllers, state-space models, stability analysis (Lyapunov), and feedback control. This is where CPS fundamentally differs from pure software systems — an attacker doesn't just corrupt data, they alter physical trajectories.

From there, study **real-time systems**: timing constraints, WCET (Worst-Case Execution Time), scheduling algorithms (EDF, Rate Monotonic), and what happens when deadlines are missed. Real-time operating systems like FreeRTOS and RTOS concepts matter here.

Then learn the **communication protocols** that glue CPS together: Modbus, DNP3, IEC 61850 (power grid), CAN bus (automotive), BACnet (building automation), OPC-UA (industrial), MQTT (IoT). Each of these was designed with availability in mind and security as an afterthought, which is exactly why they're attack surfaces.

Finally, understand **SCADA and ICS architecture**: the Purdue Model (ISA-95), the separation between the IT network and the OT (Operational Technology) network, PLCs (Programmable Logic Controllers), RTUs, and HMIs. The Purdue Model is slowly dying in favor of flatter architectures, which creates entirely new security problems.

**Key reading:**
- Lee, E.A. "Cyber Physical Systems: Design Challenges." *IEEE ISORC*, 2008. (The foundational paper defining the field — still essential.)
- Kim & Kumar, "Cyber–Physical Systems: A Perspective at the Centennial." *Proceedings of the IEEE*, 2012.

---

## 2. Threat Landscape: Attack Taxonomy

You need a mental model of *how* CPS gets attacked, because CPS attacks are categorically different from IT attacks.

**Attack classes to understand deeply:**

**Sensor spoofing / signal injection** — injecting false physical signals into sensors without touching the software. GPS spoofing, acoustic injection into MEMS accelerometers, light injection into cameras, and electromagnetic injection are all real demonstrated attacks.

**False Data Injection (FDI) attacks** — crafting malicious measurements that pass state estimators. The seminal 2009 Liu et al. paper showed that an attacker with knowledge of the system model can inject errors that are invisible to traditional bad-data detectors. This opened an entire subfield.

**Replay attacks** — recording sensor data from a normal period and replaying it while tampering with actuators. This is exactly what was used in the Stuxnet-era attacks.

**Semantic attacks** — attacks that are syntactically valid (they don't trigger anomaly detectors) but are semantically malicious, gradually steering a physical process toward an unsafe state.

**Supply chain and firmware attacks** — compromising firmware in PLCs and RTUs. Triton/TRISIS (2017) targeted Schneider Electric safety controllers and remains the most dangerous ICS attack publicly documented.

**Denial of Safety (DoS on actuators)** — unlike IT DoS, where you deny *information*, in CPS you can deny the ability to *actuate*, with potentially catastrophic physical consequences.

**Papers:**
- Liu, Reiter & Ning. "False Data Injection Attacks Against State Estimation in Electric Power Grids." *CCS 2009.* (A classic, but read it — it defined the field.)
- Abbasi et al. "Challenges in Designing Exploit Mitigations for Deeply Embedded Systems." *IEEE EuroS&P 2019.*
- Carcano et al. "A Multidimensional Critical State Analysis for Detecting Intrusions in SCADA Systems." *IEEE Transactions on Industrial Informatics*, 2011.

---

## 3. ICS/SCADA Security

This is the most operationally relevant domain. Real-world incidents have occurred here — power grids going down, water treatment plants being manipulated, pipelines disrupted.

**Topics:**

- IEC 62443 (the industrial security standard, analogous to NIST SP 800-53 for IT)
- Defense-in-depth for OT networks: network segmentation, DMZs, unidirectional gateways (data diodes)
- Incident analysis: Ukraine power grid attacks (2015, 2016), Stuxnet, Triton, Colonial Pipeline
- The difference between safety systems (SIS) and control systems (DCS/PLC) and why attacking the boundary between them is so dangerous

**Recent papers:**
- Abbas et al. "SAIN: Improving ICS Attack Detection Sensitivity via State-Aware Invariants." *USENIX Security 2024.* This paper proposes invariant-based detection that accounts for the physical state of the system, which pure network-layer detectors miss entirely.
- Tang et al. "ERACAN: Defending Against a Game-Changing CAN Threat Model." *ACM CCS 2024.* Directly relevant to automotive/CPS CAN bus security with an updated attacker model.
- Kim et al. "A Systematic Study of Physical Sensor Attack Hardness." *USENIX Security 2024.* Provides a framework for reasoning about which sensors are harder to attack physically.

---

## 4. Anomaly Detection and Intrusion Detection for CPS

This is the primary defensive research direction in academic CPS security. The core challenge is that CPS has **physical invariants** — laws of physics that constrain what valid sensor readings can look like — and clever detectors exploit this.

**Topics:**

- Physics-based anomaly detection (using control-theoretic models as the "ground truth")
- Invariant mining (automatically learning what physical relationships must hold)
- Hybrid detectors combining network traffic analysis with physical-state monitoring
- The fundamental tension: sensitivity vs. specificity, especially in safety-critical environments where false positives may be as dangerous as false negatives
- Adversarial attacks against ML-based detectors: an attacker who knows your detector can craft attacks that evade it

**Key conceptual frameworks:**
CUSUM (cumulative sum) detectors, $\chi^2$ detectors, state estimation residuals, and more recently, deep learning approaches using LSTMs and autoencoders for time-series anomaly detection.

**Papers:**
- Inoue et al. "Anomaly Detection for a Water Treatment System Using Unsupervised Machine Learning." *IEEE ICDM Workshop*, 2017. (The SWaT dataset paper — this dataset is used in nearly every CPS anomaly detection paper.)
- Feng et al. "A Systematic Evaluation of Cyber-Physical Attacks and Countermeasures in the Power Grid." *ACSAC 2017.*
- Moriano et al. "Adaptive Anomaly Detection in CPS." *OSTI 2025.* This recent survey identifies near-real-time and adaptive anomaly detection as open problems, and maps the landscape of attack types (DoS, fuzzing, FDIA, spoofing, masquerade) across ICS, vehicle, power grid, and IoT domains.
- Erba et al. "Real-Time Evasion Attacks with Physical Constraints on Deep Learning-Based Anomaly Detectors in Industrial Control Systems." *RAID 2020.* (Read this to understand the adversarial perspective on CPS detectors.)

---

## 5. Embedded Systems and Firmware Security

CPS devices are fundamentally embedded systems. Understanding their security requires going a layer lower than OS security.

**Topics:**

- Memory-unsafe code in resource-constrained environments (no MMU, no ASLR, no stack canaries by default)
- Firmware extraction and reverse engineering (binwalk, Ghidra, firmware emulation with QEMU/FIRMADYNE)
- Embedded bootloader security, secure boot, code signing
- Hardware security primitives: TPM, ARM TrustZone, Hardware Security Modules (HSMs)
- Side-channel attacks: power analysis (SPA/DPA), timing attacks, EM emanation
- Fault injection: voltage glitching, clock glitching — used to bypass secure boot

**Papers:**
- Khan, Xu & Tian. "EC: Embedded Systems Compartmentalization via Intra-Kernel Isolation." *IEEE S&P 2023.*
- Khan, Xu & Tian. "Low-Cost Privilege Separation with Compile Time Compartmentalization for Embedded Systems." *IEEE S&P 2023.* Two papers from the same group tackling how to bring isolation principles to deeply embedded environments.
- Clements et al. "ACES: Automatic Compartments for Embedded Systems." *USENIX Security 2018.* (Older but foundational for understanding the problem.)
- Bozzato et al. "Sowed the Wind and Reaped the Whirlwind: A Detailed Analysis of Firmware Security." *IEEE S&P 2019.*

---

## 6. Automotive and Vehicular Security

This is one of the fastest-growing CPS security domains, with major industry investment, CTF competitions (Automotive Village at DEF CON), and real regulatory pressure (UN WP.29/R155).

**Topics:**

- CAN bus architecture and its lack of authentication (broadcast, no source verification)
- Automotive Ethernet (100BASE-T1) and the transition to zonal architecture in modern vehicles
- V2X (Vehicle-to-Everything) communication security: C-V2X, DSRC/WAVE (IEEE 802.11p)
- ECU (Electronic Control Unit) firmware analysis and vulnerability discovery
- Infotainment → CAN bus pivot attacks (the Miller & Valasek Jeep hack mental model)
- ISO/SAE 21434 (automotive cybersecurity standard)

**Papers:**
- Miller & Valasek. "Remote Exploitation of an Unaltered Passenger Vehicle." *DEF CON 23*, 2015. (Read the full technical paper, not just the Wired article.)
- Abuabed et al. applied the STRIDE threat model to modern vehicle vulnerability assessments in 2023.
- Checkoway et al. "Comprehensive Experimental Analyses of Automotive Attack Surfaces." *USENIX Security 2011.* (Still the gold standard methodology paper.)
- Püllen et al. "Covert Channel Attack to Cyber-Physical Systems." *IEEE ARES 2021.*

---

## 7. Smart Grid and Energy Systems Security

The power grid is considered the most critical of critical infrastructure. CPS security and power systems have an enormous intersection.

**Topics:**

- Power grid topology: generation, transmission (SCADA/EMS), distribution (AMI/smart meters)
- State estimation and the FDIA problem (see section 2)
- PMUs (Phasor Measurement Units) and their role in grid observability and their vulnerability
- Demand response manipulation and load redistribution attacks
- Microgrid and distributed energy resource (DER) security

**Papers:**
- Liu, Reiter & Ning. "False Data Injection Attacks Against State Estimation in Electric Power Grids." *CCS 2009.* Still foundational — read it even though it's older.
- Nafees et al. studied smart grid CPS cybersecurity in 2023, and Zografopoulos et al. analyzed cyber-physical energy systems security from a multi-layered attack perspective.
- Liang et al. "A Review of False Data Injection Attacks Against Modern Power Systems." *IEEE Transactions on Smart Grid*, 2017.

---

## 8. Formal Methods and Verification for CPS Security

This connects your program analysis coursework directly to CPS. Formal methods are used to prove security properties about CPS before deployment.

**Topics:**

- Model checking (TLA+, SPIN, NuSMV) applied to control logic
- Timed automata (UPPAAL) for real-time systems verification
- Hybrid automata for systems that mix continuous dynamics with discrete events
- Reachability analysis: given a set of initial states and a threat model, can the system reach an unsafe state?
- Contract-based design and assume-guarantee reasoning

**Papers:**
- Platzer, André. "Logical Foundations of Cyber-Physical Systems." *Springer*, 2018. (This is a textbook, but Platzer's dL (differential dynamic logic) and KeYmaera tool are the gold standard for CPS verification. You'll see this cited everywhere.)
- Frehse et al. "SpaceEx: Scalable Verification of Hybrid Systems." *CAV 2011.*
- Dreossi et al. "Compositional Falsification of Cyber-Physical Systems with Machine Learning Components." *FMCAD 2017.*

---

## 9. Physical Layer Security and Sensor Security

A unique aspect of CPS security that has no analogue in pure IT security.

**Topics:**

- GPS spoofing and anti-spoofing techniques (signal authentication, cross-checking)
- Ultrasonic/acoustic attacks on MEMS inertial sensors (accelerometers, gyroscopes) — demonstrated on drones, medical devices, and self-driving cars
- Camera-based sensor attacks: adversarial patches on road signs, LiDAR spoofing
- Watermarking-based attack detection: injecting known signals into actuators and checking whether sensor readings reflect them
- Sensor fusion as a defense (harder to simultaneously spoof multiple physically independent sensor modalities)

**Papers:**
- Trippel et al. "WALNUT: Waging Doubt on the Integrity of MEMS Accelerometers with Acoustic Injection Attacks." *IEEE EuroS&P 2017.*
- Tu et al. "Trick Me If You Can: Human-in-the-loop Generation of Adversarial Examples for Autonomous Driving." *IEEE TPAMI 2021.*
- Shen et al. "Drift with Devil: Security of Multi-Sensor Fusion Based Localization in High-Autonomy Autonomous Driving Under GPS Spoofing." *USENIX Security 2020.*
- Davidson et al. "Controlling UAVs with Sensor Input Spoofing Attacks." *WOOT 2016.*

---

## 10. Digital Twins for Security

This is an emerging area directly aligned with where the field is heading and very relevant for Georgia Tech's research directions.

**Topics:**

- Using digital twin simulation to detect anomalies by comparing real system behavior against a simulated model running in parallel
- Attack simulation on digital twins to test defenses without risking physical infrastructure
- The fidelity problem: a digital twin is only as useful as it accurately reflects the physical system, and model drift introduces security gaps

**Papers:**
- CISA's 2023 compendium specifically identifies digital twin frameworks as a key technology for manufacturing security and CPS resiliency.
- Eckhart & Ekelhart. "Towards Security-Aware Virtual Environments for Digital Twins." *SafeConfig 2018.*
- Dietz & Pernul. "Digital Twin: Empowering Enterprises Towards a System-of-Systems Approach." *Business & Information Systems Engineering*, 2020.

---

## 11. Resilience, Recovery, and Safe Failure

Cybersecurity in CPS isn't just about prevention — it's about graceful degradation and recovery.

**Topics:**

- Fail-safe vs. fail-secure design
- Byzantine fault tolerance adapted for CPS (where some sensors may be compromised, not just failed)
- Moving target defense: dynamically varying system configurations to make attack modeling harder
- Cyber resilience metrics and the NIST Cybersecurity Framework applied to OT
- Incident response for OT environments (very different from IT — you can't just patch and reboot a running power plant)

---

## 12. Where the Field Is Heading (Hot Research Areas)

These are the topics appearing most frequently in top venues right now:

**LLM-assisted vulnerability discovery in PLC/firmware code** — using LLMs to find logic vulnerabilities in ladder diagram or structured text PLC programs. Very early-stage but moving fast.

**AI/ML security in CPS** — adversarial attacks against the ML components of autonomous vehicles and industrial systems (not just the network layer). ML-based anomaly detectors using KNN, SVM, and deep neural networks are widely studied for CPS intrusion detection, but their robustness against adaptive adversaries is an open problem.

**5G/private networks for ICS** — as OT networks migrate from serial/proprietary protocols to 5G private networks, the attack surface transforms dramatically.

**Post-quantum cryptography for resource-constrained CPS devices** — NIST finalized its PQC standards by end of 2024, and the transition challenge for embedded OT devices with 20-year lifespans is an active research area.

**Provenance-based detection in CPS** — extending audit-log-based threat detection (popular in IT security, as seen in papers like HOLMES and WATSON) to OT environments.

---

## Suggested Learning Sequence

For your level, I'd suggest attacking this in three phases over the next year:

**Phase 1 (now through summer):** Foundations. Control theory basics, the Purdue Model, IEC 62443, and close reading of the FDIA paper (Liu 2009) and one of the embedded systems papers. Set up the SWaT or BATADAL public ICS datasets and run simple anomaly detection experiments yourself.

**Phase 2 (fall semester):** Go deep on one domain — either automotive (CAN bus hacking with a test bench) or ICS anomaly detection (implement a physics-based invariant checker). This gives you something concrete to show Georgia Tech in your SOP.

**Phase 3 (early 2026 pre-application):** Read actively from the venues that matter — IEEE S&P, USENIX Security, ACM CCS, NDSS, and specifically the *Workshop on Cyber-Physical Systems Security and Privacy (CPS-SPC)* which runs co-located with ACM CCS and is the top dedicated CPS security venue. Knowing the recent papers from your target advisors' labs cold is essential for application interviews.

The professors you'll want to know at Georgia Tech for CPS security are Raheem Beyah (now Dean, but his students are still active), Taesoo Kim (systems/firmware), and the folks in the Georgia Tech Research Institute (GTRI) ICS security group. Their recent publications are the best signal for what to read.

---

Here's your full 13-week plan. Here's what's inside:

**Structure:** Three phases — Foundations (Weeks 1–4), Core Security Domains (Weeks 5–9), Advanced Topics + Projects (Weeks 10–13) — each ending with a concrete deliverables checklist.

**Daily breakdown:** Every week has a day-by-day schedule with specific tasks, not just topics. About 15 hours/week total.

**28 papers:** Assigned week by week with a short rationale for why each one matters. A master table at the end lists all of them with the key concept they teach.

**3 lab projects** that produce GitHub-ready artifacts for your SOP:
- SWaT anomaly detector (invariant-based + LSTM)
- Virtual CAN bus with your own IDS
- FDI attack + watermarking defense on IEEE 14-bus grid

**Tools table** maps every tool to the exact week it's needed so you're not scrambling to install things mid-study.

The plan ends on May 31 with a week dedicated to polishing one capstone project and drafting the technical paragraphs of your SOP — timed so you have those artifacts ready before the June application push.