# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Purpose

This is a personal CPS (Cyber-Physical Systems) security study repository, used to prepare for a Georgia Tech PhD application. It contains structured notes, weekly study logs, and eventually lab project artifacts.

## Repository Structure

- `claude_plan.md` — Master 13-week study plan covering CPS security topics, paper assignments, and lab projects
- `week_N.md` — Weekly study notes (one file per week, e.g., `week_1.md`)
- `cps_study_plan.docx` — Original study plan document

## Study Plan Overview

Three phases across 13 weeks (~15 hrs/week):

- **Phase 1 (Weeks 1–4):** Foundations — control theory, Purdue Model, IEC 62443, FDIA (Liu 2009), embedded systems basics
- **Phase 2 (Weeks 5–9):** Core security domains — ICS/SCADA, anomaly detection, embedded/firmware security, automotive CAN bus, smart grid
- **Phase 3 (Weeks 10–13):** Advanced topics + capstone project polish, SOP technical paragraphs

## Lab Projects (GitHub Artifacts for SOP)

Three planned projects:
1. **SWaT anomaly detector** — invariant-based + LSTM on the public SWaT dataset
2. **Virtual CAN bus IDS** — implement an intrusion detection system on a simulated CAN bus
3. **FDI attack + watermarking defense** — on the IEEE 14-bus power grid model

## Key Venues and Resources

Top venues to track: IEEE S&P, USENIX Security, ACM CCS, NDSS, and the CPS-SPC workshop (co-located with CCS).

Target Georgia Tech advisors: Raheem Beyah (now Dean), Taesoo Kim (systems/firmware), GTRI ICS security group.

## How to Help

When assisting with this repository:
- Weekly note files (`week_N.md`) capture daily study progress — help populate them with summaries, paper notes, and key concepts
- For paper reading tasks, prioritize extracting the core technical contribution and how it connects to the broader CPS security landscape
- Lab projects will eventually involve Python code (anomaly detection, simulation); treat those as research-grade scripts, not production software