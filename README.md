# Fermentation Controller

## Overview
This node manages the fermentation process by controlling temperature, tracking time, and monitoring gravity to progress through various fermentation steps. The node ensures accurate execution of multi-step fermentation profiles based on specific time and gravity goals.

## Inputs
msg.payload.profile: An object containing the fermentation profile, which includes multiple steps. Each step has a target temperature and a goal, which can be based on time (days) or gravity (absolute or relative to the Final Gravity, FG).

msg.payload.gravity: The latest gravity reading of the fermenting liquid (Specific Gravity, SG).

msg.payload.temperature: The latest temperature reading of the fermenting vessel.

## Outputs
### Progress Output (msg.payload):

Contains the current step number, total steps, target temperature, the goal for the current step (days or gravity), and a fermentation log.
The fermentation log contains timestamps of when each step started, when the step temperature was reached, and the reasons for transitioning between steps.
Example msg.payload structure:

```json
{
    "target": 18.5,
    "currentStep": 2,
    "totalSteps": 4,
    "stepGoal": {
        "days": 7,
        "gravity": {
            "absolute": 1.010
        }
    },
    "log": [
        {
            "step": 1,
            "event": "First Step Started",
            "timestamp": "2024-09-14T10:00:00Z"
        },
        {
            "step": 1,
            "event": "Step temperature reached",
            "timestamp": "2024-09-14T11:00:00Z"
        },
        {
            "step": 2,
            "event": "Step Transition",
            "reason": "Gravity goal met",
            "timestamp": "2024-09-16T10:00:00Z"
        }
    ]
}
```

### Status Output:

Displays the current step, total steps, and the step's goal in a human-readable format.
Shows blue ring status when waiting for desired step temperature and blue dot when temperature has been reached, while the process is ongoing.
The time for a step does not start counting until step temperature has been reached.
When fermentation is complete, a green dot is displayed.
Example status text:

Step 2 / 4. Goal: 7 days or 1.010 SG

## Process Flow
1. Profile Setup: When a new profile is received (msg.payload.profile), the fermentation process resets, and the profile is activated. The first step begins immediately.

2. Step Temperature: Each step has a target temperature that must be reached (default within ±0.5°C but configurable) before the step's time goals are evaluated. If the temperature is not within range, the process will wait until it stabilizes before starting the step's timer.

3. Step Transition:
    * If a step's time goal is reached, the node transitions to the next step.
    * If a gravity goal is met (either a specific SG or a relative SG from FG), the node transitions to the next step.
    * Upon each transition, a log entry is made, including the reason for the transition (time or gravity).

4.Completion: When all steps are complete, the process stops, and the status changes to indicate "Fermentation Complete" with a green dot. The last temperature target is retained to prevent sudden changes in temperature control.

## Logging
The node keeps a detailed log of the fermentation process, which includes:

* Timestamp when each step starts.
* Timestamp when the step temperature was reached for each step.
* Reason for transitioning between steps (e.g., "Time goal met" or "Gravity goal met"). This log is provided in every progress message to track the progress history.


## Example Profile
Here is an example of a fermentation profile that the node would accept:
```json
{
    "expectedFG": 1.009,
    "steps": [
        {
            "targetTemperature": 10,
            "goal": {
                "gravity": {
                    "fromFG": 0.015
                }
            }
        },
        {
            "targetTemperature": 16,
            "goal": {
                "days": 3,
                "gravity": {
                    "absolute": 1.010
                }
            }
        },
        {
            "targetTemperature": 0,
            "goal": {
                "days": 3
            }
        }
    ]
}
```