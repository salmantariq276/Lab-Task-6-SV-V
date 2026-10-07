# Task 2: Formalize Constraints (ARLCCS)

## Proposition Dictionary

| Symbol | Meaning |
|---|---|
| TA | Train_Approaching (train detected inside the approach zone) |
| TW | Train_Within_Threshold (train closer than the minimum safe warning distance) |
| TP | Train_Present (train on or inside the crossing) |
| CC | Crossing_Clear_Confirmed (whole train has left, confirmed by exit sensor) |
| BO | Barrier_Open |
| BC | Barrier_Closed |
| OC | Open_Command (controller issues an open command) |
| WL | Warning_Lights_On |
| AL | Audible_Alarm_On |
| RR | Road_Signal_Red |
| RG | Road_Signal_Green |
| SF | Sensor_Fault |
| BF | Barrier_Fault |
| CL | Communication_Loss |
| SC | Sensor_Conflict (contradictory readings) |
| EM | Emergency_Condition |
| OA | Operator_Alert_Sent (control center notified) |

Assumption: BO and BC are mutually exclusive and exhaustive at rest (a barrier in motion counts as neither).

## Formal Expressions

| ID | Formal expression | Reading |
|---|---|---|
| C1 | `TP → ¬BO` | If a train is present, the barrier is not open. |
| C2 | `OC → CC` | An open command is only allowed once the crossing is confirmed clear. |
| C3 | `(TA ∨ TP) → (WL ∧ AL)` | If a train approaches or is present, both lights and alarm are on. |
| C4 | `TW → BC` | If the train is within the threshold, the barrier is closed. |
| C5 | `(TA ∨ TP) → RR` | If a train approaches or is present, the road signal is red. |
| C6 | `SF → (BC ∧ WL ∧ AL)` | A sensor fault forces barrier closed, lights on and alarm on. |
| C7 | `BF → (RR ∧ OA)` | A barrier fault forces a red signal and an operator alert. |
| C8 | `CL → (BC ∧ WL ∧ OA)` | Communication loss forces barrier closed, lights on and an alert (where possible). |
| C9 | `SC → ¬OC` | Conflicting sensors mean no open command. |
| C10 | `EM → (RR ∧ OA)` | An emergency forces a red signal and an operator alert. |
| C11 | `BC → ¬RG` | A closed barrier means the signal is not green. |

C12 is left informal because it involves time bounds.
