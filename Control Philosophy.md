## Theory

The project is based on **sequential PLC control**, where each station performs its operation only after the previous stage is completed. Bottles are transported by a conveyor through the **Filling, Capping, Labelling, and Inspection**. Timers control process durations, pneumatic actuators perform mechanical operations, and interlocks ensure safe and reliable sequencing. Production counters record accepted and rejected bottles, while safety logic prevents operation under fault conditions such as emergency stop, low air pressure, or insufficient tank level.

### Master Safety & Conveyor Control

* Emergency Stop resets all conveyor and station status bits.
* Conveyor operation is permitted only with valid motor feedback.
* Air pressure and tank level interlocks prevent unsafe operation.

# Inputs

| Tag                | Description                                             |
| ------------------ | ------------------------------------------------------- |
| Run                | Starts the production cycle                             |
| Emergency_Stop     | Stops the entire system and resets all station statuses |
| Conveyor_Motor_FB  | Conveyor motor running feedback                         |
| Tank_Level_OK      | Filling tank level available                            |
| Air_Pressure       | Compressed air pressure input                           |
| Bottle_Detection_1 | Bottle present at Filling Station                       |
| Bottle_Detection_2 | Bottle present at Capping Station                       |
| Bottle_Detection_3 | Bottle present at Labelling Station                     |
| Bottle_Detection_4 | Bottle present at Inspection Station                    |
| Bottle_Quality     | Quality inspection result (Good/Reject)                 |

# Outputs

| Tag               | Description                              |
| ----------------- | ---------------------------------------- |
| Conveyor_Running  | Starts the conveyor motor                |
| Pneumatic_Clamp_1 | Clamps bottle at Filling Station         |
| Filling_Valve     | Controls bottle filling                  |
| Pneumatic_Clamp_2 | Clamps bottle at Capping Station         |
| CapHead_Position  | Extends/Retracts capping head            |
| Capping_Motor     | Drives capping mechanism                 |
| Pneumatic_Clamp_3 | Clamps bottle at Labelling Station       |
| Labelling_Unit    | Applies bottle label                     |
| Bottle_Exit       | Releases bottle from the production line |

# Internal Memory Bits

* Conveyor_Status_1
* Conveyor_Status_2
* Conveyor_Status_3
* Conveyor_Status_4
* Filling_Valve_Status
* Clamp_Status
* Process Sequence Bits

# Timers

| Timer   | Function            |
| ------- | ------------------- |
| Timer_1 | Filling duration    |
| Timer_2 | Post-filling dwell  |
| Timer_3 | Capping duration    |
| Timer_4 | Clamp release delay |
| Timer_5 | Labelling duration  |

# Counters

| Counter | Function                |
| ------- | ----------------------- |
| CTU     | Counts accepted bottles |
| CTD     | Counts rejected bottles |

# Ladder Logic Overview

### Rungs 0–1 : Master Safety & Conveyor Control

* Emergency Stop resets all conveyor and station status bits.
* Conveyor runs only when a station requests movement and motor feedback is available.

### Rungs 2–9 : Filling Station

* Starts the production cycle.
* Detects bottle arrival.
* Stops conveyor.
* Extends pneumatic clamp.
* Opens filling valve.
* Executes filling timer.
* Waits for dwell period.
* Releases clamp.
* Transfers bottle to Station 2.

### Rungs 10–17 : Capping Station

* Detects bottle.
* Stops conveyor.
* Clamps bottle.
* Extends capping head.
* Starts capping motor.
* Executes capping timer.
* Retracts head.
* Releases clamp.
* Transfers bottle to Station 3.

### Rungs 18–22 : Labelling Station

* Detects bottle.
* Stops conveyor.
* Clamps bottle.
* Performs labelling operation.
* Releases clamp.
* Transfers bottle to Station 4.

### Rungs 23–27 : Inspection & Bottle Exit

* Detects bottle at inspection.
* Evaluates bottle quality.
* Increments good bottle counter.
* Increments reject bottle counter.
* Generates bottle exit pulse.
* Restarts conveyor for the next production cycle.

### Operating Philosophy

* Sequential station-to-station control
* Timer-based process execution
* Conveyor movement only after station completion
* Safety and process interlocking throughout the cycle
* Automatic production counting and quality monitoring
* Continuous cyclic operation after system start
