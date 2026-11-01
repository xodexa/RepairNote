# Diagnosing and fixing mouse micro-tracking issues (LOD)

My mouse was acting up. Fast swipes and normal movements felt completely fine, but when I tried to make tiny, slow micro-adjustments (like lining up a pixel), the cursor would just freeze or skip. It was really frustrating, so instead of just blindly buying a new mousepad, I decided to actually look at the raw data using ClickSyncMouseTester to see what was going on.
At first glance, the 2.4GHz connection looked flawless. It was showing a stable 1000Hz polling rate with zero packet loss. But when I specifically tested slow, micro-movements. 

If you're running into this issue, here are the three charts you need to watch:
* **Report Interval:** Instead of a flat, steady line at ~1.0ms, the interval would randomly spike to 2ms, 5ms, or higher. The sensor was literally pausing.
* **XY Counts:** When moving slowly, the counts weren't showing a smooth wave of `1`s and `2`s. It was either flatlining at `0` (dropping the movement entirely) or rigidly snapping to `1`.
* **Delta Magnitude:** This was the dead giveaway. Even while my hand was physically dragging the mouse, the absolute movement magnitude would drop to `0` for fractions of a second.

## The Root Cause
Since the wireless connection was fine, I figured it had to be a physical issue. I started reading up on Lift-Off Distance (LOD) and how optical sensors have a very strict focal range (usually 1-2mm). If the sensor sits too high off the pad, it loses contrast and just gives up tracking at low speeds.
I took the mouse apart and found a tiny piece of plastic wedged inside that was pushing the PCB up just a fraction of a millimeter.
Because the PCB was lifted, the sensor was sitting too far from the pad. At high speeds, the mouse moved across enough texture to compensate, but at low speeds, the sensor got confused by the height and stopped sending movement data.

I popped the plastic out, screwed the mouse back together, and ran the exact same slow-movement test.
Before the fix, the XY counts were locked to rigid `-1, 0, 1` steps, and the timestamps between packets were all over the place. After removing the plastic, the data showed a healthy, granular mix of `-2, -1, 0, 1, 2` counts. The Report Interval chart stayed locked in, and the Delta Magnitude never dropped to zero while my hand was moving.
