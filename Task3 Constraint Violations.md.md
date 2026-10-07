# Task 3: Identify Constraint Violations (ARLCCS)

Each violation is the case where the constraint's implication evaluates to **FALSE**: antecedent TRUE, consequent FALSE. Symbols are defined in the Task 2 file.

### V1, violates C1: `TP → ¬BO`
- **Scenario:** A train is in the crossing and a barrier actuator relay sticks or a faulty command lifts the barrier.
- **State:** `TP = TRUE`, `BO = TRUE`.
- **What went wrong:** The barrier is open while the train is physically on the crossing.
- **How we know:** `TRUE → ¬TRUE` = `TRUE → FALSE` = FALSE. The barrier-position sensor says open while the occupancy sensor says occupied.

### V2, violates C2: `OC → CC`
- **Scenario:** The entry sensor shows the train has passed, so the controller issues an open command. The train is long and its rear carriages are still in the crossing, but the exit sensor has not confirmed clearance.
- **State:** `OC = TRUE`, `CC = FALSE`.
- **What went wrong:** The system opened on a premature "train gone" assumption instead of confirmed clearance.
- **How we know:** `TRUE → FALSE` = FALSE. The log shows an open command with no clear-confirmation event before it.

### V3, violates C3: `(TA ∨ TP) → (WL ∧ AL)`
- **Scenario:** A train enters the approach zone. The lights switch on but the alarm driver fails, so there is no sound.
- **State:** `TA = TRUE`, `WL = TRUE`, `AL = FALSE`.
- **What went wrong:** Only part of the required warning is active. Anyone who cannot see the lights gets no alert.
- **How we know:** Antecedent TRUE, consequent `TRUE ∧ FALSE` = FALSE. Alarm status feedback reads off while a train is detected.

### V4, violates C4: `TW → BC`
- **Scenario:** The train is within the safe threshold but the barrier is still lowering because the motor is slow or detection triggered too late.
- **State:** `TW = TRUE`, `BC = FALSE`.
- **What went wrong:** The barrier did not reach the closed position before the train reached the safe-distance boundary.
- **How we know:** `TRUE → FALSE` = FALSE. The barrier-position sensor reports "moving" or "open" while the train-distance sensor reports inside the threshold.

### V5, violates C5: `(TA ∨ TP) → RR`
- **Scenario:** The road signal controller stays on a normal green cycle while a train approaches, because the "train detected" message was dropped between subsystems.
- **State:** `TA = TRUE`, `RR = FALSE` (signal green).
- **What went wrong:** Vehicles are still invited to enter the crossing area.
- **How we know:** `TRUE → FALSE` = FALSE. Signal state feedback shows green while the train sensor is active.

### V6, violates C6: `SF → (BC ∧ WL ∧ AL)`
- **Scenario:** The approach sensor fails (open circuit found by self-test) but the controller treats the missing signal as "no train" and leaves the barrier open with warnings off.
- **State:** `SF = TRUE`, `BC = FALSE`, `WL = FALSE`, `AL = FALSE`.
- **What went wrong:** The system failed in the unsafe direction. A fault was detected but no safe state was entered.
- **How we know:** `TRUE → FALSE` = FALSE. The fault flag is raised but barrier, lights and alarm stay in normal idle states.

### V7, violates C7: `BF → (RR ∧ OA)`
- **Scenario:** The barrier arm jams half-way. The system detects the fault but does not turn the road signal red and does not message the control center.
- **State:** `BF = TRUE`, `RR = FALSE`, `OA = FALSE`.
- **What went wrong:** The fault is silent. Traffic is not stopped and no human is aware.
- **How we know:** `TRUE → (FALSE ∧ FALSE)` = FALSE. The fault flag is set, but the signal log shows green and the alert log is empty.

### V8, violates C8: `CL → (BC ∧ WL ∧ OA)`
- **Scenario:** The link between sensors and controller is cut. The controller keeps its last known state ("no train") and leaves the barrier open.
- **State:** `CL = TRUE`, `BC = FALSE`, `WL = FALSE`, `OA = FALSE`.
- **What went wrong:** Instead of failing safe, the system acted on stale data.
- **How we know:** `TRUE → FALSE` = FALSE. The heartbeat or watchdog timeout fires (CL = TRUE) but no fail-safe action follows.

### V9, violates C9: `SC → ¬OC`
- **Scenario:** The entry sensor reports "train present" while the exit sensor reports "crossing clear". The controller trusts the exit sensor and issues an open command.
- **State:** `SC = TRUE`, `OC = TRUE`.
- **What went wrong:** The system resolved an uncertain reading in favour of opening instead of keeping the barrier closed.
- **How we know:** `TRUE → ¬TRUE` = FALSE. The sensor cross-check reports a mismatch, yet an open command is in the log.

### V10, violates C10: `EM → (RR ∧ OA)`
- **Scenario:** A car stalls on the tracks and the emergency button is pressed. The emergency flag is set, but the signal stays green and no alert reaches the control center.
- **State:** `EM = TRUE`, `RR = FALSE`, `OA = FALSE`.
- **What went wrong:** The emergency was recognised internally but no response was triggered.
- **How we know:** `TRUE → (FALSE ∧ FALSE)` = FALSE. The emergency flag is TRUE while signal and alert outputs are unchanged.

### V11, violates C11: `BC → ¬RG`
- **Scenario:** The barrier is closed after a train passes, but a software timer cycles the road signal back to green too early.
- **State:** `BC = TRUE`, `RG = TRUE`.
- **What went wrong:** Signal and barrier disagree. Drivers see green while the barrier blocks them.
- **How we know:** `TRUE → ¬TRUE` = FALSE. Signal and barrier states are logged simultaneously as green and closed.

---

## Summary

| Constraint | Violation | Failure type |
|---|---|---|
| C1 | V1 | Actuator or command fault |
| C2 | V2 | Premature clearance assumption |
| C3 | V3 | Partial warning failure |
| C4 | V4 | Timing / slow barrier |
| C5 | V5 | Communication between subsystems |
| C6 | V6 | Unsafe fault handling |
| C7 | V7 | Silent barrier failure |
| C8 | V8 | Stale data on comms loss |
| C9 | V9 | Unsafe resolution of conflicting data |
| C10 | V10 | Emergency not acted on |
| C11 | V11 | Subsystem inconsistency |

**Key observation:** Every constraint is an implication `A → B`. A violation is exactly the case where `A` is TRUE and `B` is FALSE, so each one can be checked at run time by a monitor that evaluates the antecedent and consequent from live sensor and actuator states.
