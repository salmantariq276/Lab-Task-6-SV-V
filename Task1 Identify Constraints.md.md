# Task 1: Identify Constraints (ARLCCS)

| ID | Constraint (simple English) | Why it is necessary |
|---|---|---|
| C1 | The barrier must not be open while a train is present in the crossing. | Open barriers would let road vehicles enter the crossing during a train passage, causing a collision. |
| C2 | The barrier may only be commanded to open after the system has confirmed the train has completely cleared the crossing. | The front of the train may have passed while the rear is still in the crossing. Opening early endangers road users. |
| C3 | Warning lights and the audible alarm must both be on whenever a train is approaching or present. | Drivers and pedestrians need visual and audible warning to stop before the barrier lowers. |
| C4 | Once the train is within the safe threshold distance, the barrier must already be closed. | A train cannot stop quickly. The road must be sealed before it arrives. |
| C5 | The road traffic signal must be red whenever a train is approaching or present. | Road traffic must be stopped ahead of the crossing so vehicles do not queue on the tracks. |
| C6 | If a sensor fails, the system must go to the safe state: barrier closed and warnings on. | With a sensor failure the system cannot know where the train is. The only safe assumption is that a train may be coming. |
| C7 | If the barrier fails, the road signal must be red and the control center must be alerted. | A broken barrier cannot physically stop traffic, so the signal and a human response are the remaining protections. |
| C8 | If communication is lost, the system must close the barrier, keep warnings on and alert the operator (where possible). | A system that cannot coordinate with sensors or the control center must fail safe rather than assume the track is clear. |
| C9 | If sensors give conflicting readings, the barrier must not be commanded to open. | Contradictory data means the train position is uncertain. Opening on uncertain data is unsafe. |
| C10 | In an emergency, the road signal must be red and the control center must be alerted. | Emergencies such as a vehicle stuck on the tracks need immediate road blocking and human intervention. |
| C11 | The barrier must not show closed while the road signal is green. | A green signal invites traffic toward a closed barrier, risking collision with the barrier and vehicles stopping on the crossing. |
| C12 | The barrier must finish closing within a maximum time after the warning starts, and must not begin lowering before the warnings have been on for a minimum lead time. | Drivers need time to react. Lowering too early or too late is unsafe. (Timing constraint, so it is not formalized in Task 2.) |
