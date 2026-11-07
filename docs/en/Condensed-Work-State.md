# Condensed Work State (CWS): 2-Bit Values and How to Combine Them

## Introduction

The Condensed Work State packs the working state of several sections of a machine into one process data value. Each section uses **2 bits**. This page explains what the four possible bit combinations mean, which DDIs belong to them, and how to combine several values.

> **Note on sources:** The content is based on licensed standard texts. It is deliberately reproduced here only in our own words and with a reference. Where the standard specifies nothing, this is stated explicitly. Statements that are an assessment and not a statement of the standard are marked as such.

## Two Different Meanings of the Same 2 Bits

Reference: ISO 11783-7:2022, clause 5.2.5, Tables 2 and 3 (applies generally to discrete parameters).

The value `11` is the pitfall: for an **actual value** it means something different than for a **command**.

| Value | Actual state (measured or estimated, Table 2) | Command (Table 3) |
| ----- | --------------------------------------------- | ----------------- |
| `00` | off | switch off |
| `01` | on | switch on |
| `10` | error | reserved (general, see note below) |
| `11` | not available or not installed | don't care, no action (the state stays as it is) |

Anyone who treats actual values and commands the same gets one of them wrong. Example: `11` as an actual value means "this section does not exist", as a command it means "change nothing".

> **Note:** For the setpoint CWS (DDI 290 to 305), the DDI definition makes `10` an error value, not "reserved". The definition of the individual DDI is authoritative.

## Assignment to the DDIs

Reference: ISO 11783-11, online database isobus.net, DDI list v2026101901 (as of 2026-10-19). 2 bits per section. The leftmost section (in driving direction) occupies the least significant bits of the value. Unused sections are set to `11`.

| DDI | Name | Sections per DDI | `00` | `01` | `10` | `11` |
| --- | ---- | ---------------- | ---- | ---- | ---- | ---- |
| 161 to 176 | Actual Condensed Work State | 16 | off | on | error | undefined, not installed |
| 290 to 305 | Setpoint Condensed Work State | 16 | switch off | switch on | error | no change |
| 367 to 382 | Condensed Section Override State | 16 | not overridden | overridden | reserved | undefined, not installed |

Further details from the definitions:

- **Override (367 to 382):** The value reports that a section is overridden and does not follow the section control commands. It is therefore a mask and **not** a working state. It sits in the same device element as the actual CWS and is sent only on request of the task controller.
- **Section Control State (DDI 160):** `00` manual or off (the device ignores all section control commands), `01` automatic or on. At start-up and when the prerequisites for automatic operation are lost, the device sets manual.
- **Setpoint and actual value:** For the setpoint, `11` is neutral ("no change"), for the actual value `11` means "not installed". This confirms the statement of the previous section.
- The attachment to DDI 290 only covers backward compatibility with DDIs 141 and 161, not any AND/OR combination.

## What the Standard Says About Combining

- **States along the device element hierarchy** (ISO 11783-10:2015, Annex F.3.5.1): The actual working states of the hierarchy levels are combined. If the master working state (DDI 141) is off, all sections count as off. In effect this is an AND in which "off" wins.
- **Order when switching on section control** (ISO 11783-10:2015, F.3.5.3 b and c): The task controller first switches on the Section Control State (DDI 160). The device then sets all working states to a safe state defined by the manufacturer. Only after that do setpoint commands follow.
- **Operator precedence** (AEF 007, RIG 3 Automation): The operator must be able to override any automation at any time. A manual switch-off therefore wins against the task controller's setpoint.
- **What is missing:** A truth table or priority order for AND/OR combinations of the values `10` and `11` exists neither in ISO 11783-7, -10 and -11 (nor in the DDI database) nor in AEF 007, 009 or 016.

## Recommended Combination (Assessment)

> **Assessment, not a statement of the standard:** The following rules are a recommendation by Professor-ISOBUS. They fill the gap the standards leave open.

Guiding idea: when in doubt, a combination should be fail-safe, i.e. switch off rather than stay on.

### Commands, AND Combination

Priority: **off > error > on**. The value `11` is neutral: `AND(11, x) = x`.

Reason: if `AND(off, 11)` returned `11`, the result would be read as the command "no action" and the section would stay on. That is not fail-safe.

For the setpoint, `10` is a genuine error value. A device that receives it should treat it like "off" (safe state). This is also an assessment.

| A \ B | `00` off | `01` on | `10` error | `11` neutral |
| --- | :---: | :---: | :---: | :---: |
| **`00` off** | `00` | `00` | `00` | `00` |
| **`01` on** | `00` | `01` | `10` | `01` |
| **`10` error** | `00` | `10` | `10` | `10` |
| **`11` neutral** | `00` | `01` | `10` | `11` |

Row = input A, column = input B. The table follows from the priority above and, like it, is an assessment.

### Commands, OR Combination

Priority: **error > on > off**. The value `11` is neutral: `OR(11, x) = x`. A receiver treats the result `10` like "off".

| A \ B | `00` off | `01` on | `10` error | `11` neutral |
| --- | :---: | :---: | :---: | :---: |
| **`00` off** | `00` | `01` | `10` | `00` |
| **`01` on** | `01` | `01` | `10` | `01` |
| **`10` error** | `10` | `10` | `10` | `10` |
| **`11` neutral** | `00` | `01` | `10` | `11` |

Row = input A, column = input B. The table follows from the priority above and, like it, is an assessment.

### Actual States, AND Combination

Priority: **error > not available > off > on**. "On" results only if both inputs are "on". Consumers must treat everything except `01` as "off".

Here `11` means "not installed". The table therefore applies to actual values only, not to commands.

| A \ B | `00` off | `01` on | `10` error | `11` not installed |
| --- | :---: | :---: | :---: | :---: |
| **`00` off** | `00` | `00` | `10` | `11` |
| **`01` on** | `00` | `01` | `10` | `11` |
| **`10` error** | `10` | `10` | `10` | `10` |
| **`11` not installed** | `11` | `11` | `10` | `11` |

Row = input A, column = input B. The table follows from the priority above and, like it, is an assessment.

### Actual States, OR Combination (Display)

Priority: **error > on > off > not available**.

| A \ B | `00` off | `01` on | `10` error | `11` not installed |
| --- | :---: | :---: | :---: | :---: |
| **`00` off** | `00` | `01` | `10` | `00` |
| **`01` on** | `01` | `01` | `10` | `01` |
| **`10` error** | `10` | `10` | `10` | `10` |
| **`11` not installed** | `00` | `01` | `10` | `11` |

Row = input A, column = input B. The table follows from the priority above and, like it, is an assessment.

### Override: Switch Instead of Combine

Do not combine the override state with AND or OR, use it as a selector: if the section is overridden, the manual value applies, otherwise the task controller's setpoint.

## Summary of the Priorities

| Combination | Priority (highest first) | Special feature |
| ----------- | ------------------------ | --------------- |
| Commands, AND | off, error, on | `11` neutral |
| Commands, OR | error, on, off | `11` neutral, treat `10` like "off" |
| Actual states, AND | error, not available, off, on | everything except `01` counts as "off" |
| Actual states, OR | error, on, off, not available | for display only |
| Override | no combination | selector: manual or task controller |

## Practical Relevance

The occasion for this page is the 4diac libraries `logiBUS_sections_bit` and `logiBUS_sections_quarter` (project `4diac_training1`, branch `feature/logiBUS-sections`). The libraries (package `logiBUS::sections`, [PR #347](https://github.com/Meisterschulen-am-Ostbahnhof-Munchen/4diac_training1/pull/347)) contain section switchers as well as the mask blocks `SEC_MASK_QUARTER_CMD` (setpoint) and `SEC_MASK_QUARTER_ACT` (actual value). There are two mask blocks because `11` means "no change" for the setpoint and "not installed" for the actual value. The setpoint block always sets blocked sections to `00` (also for `10` and `11`), so that a later `11` ("no change") cannot cancel the switch-off. For blocked sections the actual-value block only changes `01` to `00`; error (`10`) and "not installed" (`11`) stay visible. Section 1 is the leftmost section and occupies the least significant bits. The page itself applies to any implementation.

## References

| Statement | Reference |
| --------- | --------- |
| Meaning of the 2-bit values as actual value and as command | ISO 11783-7:2022, clause 5.2.5, Tables 2 and 3 |
| Combining working states, master working state (DDI 141) | ISO 11783-10:2015, Annex F.3.5.1 |
| Section Control State (DDI 160), safe state, order | ISO 11783-10:2015, F.3.5.3 b and c |
| Operator can override any automation | AEF 007 (RIG 3 Automation) |
| DDI definitions (160, 161 to 176, 290 to 305, 367 to 382) | ISO 11783-11, online database isobus.net, DDI list v2026101901 (as of 2026-10-19) |
