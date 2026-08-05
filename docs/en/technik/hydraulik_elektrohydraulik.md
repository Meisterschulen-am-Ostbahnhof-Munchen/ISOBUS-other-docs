# 💧 Hydraulics & Electrohydraulics in Agricultural and Construction Machinery
> 📌 **Source & Legal Basis:**
> **Source:** *Framework Curriculum for the Preparation for the Master Craftsman Examination in Agricultural and Construction Machinery Mechatronics*
> **Publisher:** German Association of Agricultural and Construction Machinery Mechatronics Engineers (LandBauTechnik-Bundesverband e. V.), Alfredstraße 102, 45131 Essen (as of February 25, 2025)
> **Regulation:** *Master Craftsman Examination Regulation (LandBauMechMstrV)* of September 9, 2024 (Federal Law Gazette 2024 I No. 277, effective August 1, 2025)
---
**Framework Curriculum Category:** Part I LE 2.4 | Part II LE 1.1 & LE 1.3

**Relevance:** Core technology for working functions, power transmission, and steering systems.

---

## 1. Hydraulic Systems & Variable Displacement Pumps

### Pump Types & Efficiencies
- **Constant Displacement Pumps:** Gear pumps, piston pumps (used in simple hydraulic circuits).
- **Variable Displacement Pumps:** Axial piston pumps with swashplate design (used in load-sensing systems).
- **Efficiencies:** Volumetric and mechanical-hydraulic efficiency, minimizing power loss.

### Load-Sensing (LS) Control Principle
- **Function:** The pump delivers only the pressure ($p_{LS} + \Delta p$) and flow rate required by the most active consumer.
- **LS Signal Line:** Transmits the highest load pressure of all active consumers to the pump controller.
- **Advantages:** High energy efficiency, low oil heating, simultaneous operation of multiple consumers regardless of the load.

---

## 2. Electrohydraulics & Proportional Valve Technology

### Valve Types & Control
- **Proportional Directional Control Valves:** Stepless control of oil flow and direction of movement.
- **PWM Control:** Pulse-width modulated signals (typically 100–300 Hz) to reduce hysteresis and friction at the solenoid coils.
- **Slider Position Feedback:** Hall sensors / LVDT sensors for precise slide monitoring in a closed-loop control system.

### CAN Bus-Controlled Valve Blocks
- Decentralized valve control units with integrated CAN bus node (ISOBUS / J1939 communication).
- Fault diagnosis: Short circuit, open circuit, coil overheating, slide jamming.

---

## 3. Hydraulic Media & Circuit Diagram Reading (ISO 1219)

### Oils & Environmental Protection
- **Mineral Oils:** HLP, HVLP (Viscosity Classes ISO VG 32, 46, 68).
- **Bio-Oils:** HEES (Synthetic Esters), Biodegradability, Compatibility with Sealing Materials (FKM, NBR).

### Standards & Symbols
- Reading of complex ISO 1219-1 circuit diagrams: Pressure relief valves, flow control valves, hydraulic accumulators, logic valves (cartridge technology).

--

### 🌐 Related topic subpages on ms-muc-docs.de
* [🌐 The PWM Signal & Infographic on ms-muc-docs.de

]