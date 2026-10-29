# Lemon Clicker Bug Report

**Name:** Tianyu Yang
**Tickets solved:** <!-- e.g. 1, 2, 3, 5, 6 (at least five) --> 1 2 3 4 5

Copy the template below once for each ticket you solved. 

---

## Ticket 1: Stats always say "Stats unavailable"

### Actual cause

<!-- Which file and function was the bug in, and what was wrong with the code?
     Explain why that code produced the behavior described in the ticket. -->

**File / function:** src/screens/MenuScreen/MenuScreenController.ts — loadStatsFromServer(), line 47

**What was wrong:** The GET request URL /api/stat did not match /api/stats defined in server/index.ts. The request failed, causing the screen to display "Stats unavailable".

### Fix

<!-- What did you change, and why does that change fix the problem? -->

Change fetch("/api/stat") to fetch("/api/stats"). This matches the URL defined on the server and allows the statistics to load correctly.

### How did you find it?

#### Hypothesis testing

**What hypothesis ultimately led you to identify the buggy code, and why?** I suspect that loadStatsFromServer() uses an incorrect request URL because scores are saved but statistics do not appear. To test this hypothesis, I will compare the request URL with the URL defined in server/index.ts. If my hypothesis is correct, I will observe a mismatch.

#### Backward tracing

<!-- Start from the symptom you saw and work backwards, one step at a time,
     until you reach the buggy line. For each step, note where you looked
     and what you saw there. Add steps as needed. -->

1. **Symptom:** <!-- what you saw, e.g. text on screen, console error --> The screen displayed "Stats unavailable".
2. **Traced back to:** <!-- where that came from, e.g. a function, a request in the Network tab, a server log --> updateStats() in MenuScreenView.ts displays this message when it receives null.
3. **Traced back to:** refreshStats() in MenuScreenController.ts calls loadStatsFromServer() to load the statistics, then passes stats to updateStats(). When the request fails, stats remains null, so updateStats() displays "Stats unavailable".
4. **Buggy code:** <!-- the file, function and line where the trace ended --> fetch("/api/stat") in loadStatsFromServer() was missing an s and did not match /api/stats defined in server/index.ts.

<!-- It is okay to use your judgement here in limiting the number of traceback steps. We want to understand your 
    reasoning for how you discovered where the bug was -->

### Confirming the fix

<!-- How did you confirm the problem is gone? --> 

Repeat the reproduction steps and confirm that the start screen now displays statistics instead of "Stats unavailable".

---

## Ticket 2: Best score always shows 0

### Actual cause

<!-- Which file and function was the bug in, and what was wrong with the code?
     Explain why that code produced the behavior described in the ticket. -->

**File / function:** server/index.ts, line 53

**What was wrong:** best starts at 0, but the code only updates it when a score is smaller. Since scores are nonnegative, best remains 0.

### Fix

<!-- What did you change, and why does that change fix the problem? -->

Change entries[i].score < best to entries[i].score > best so that a higher score updates best.

### How did you find it?

#### Hypothesis testing

**What hypothesis ultimately led you to identify the buggy code, and why?** 

The suspected cause is incorrect best-score logic because positive scores are saved but the best score remains 0. Checking the initial value and update condition tests this hypothesis. If correct, the condition will prevent positive scores from updating best.

#### Backward tracing

<!-- Start from the symptom you saw and work backwards, one step at a time,
     until you reach the buggy line. For each step, note where you looked
     and what you saw there. Add steps as needed. -->

1. **Symptom:** <!-- what you saw, e.g. text on screen, console error --> The start screen displayed "Best: 0".
2. **Traced back to:** <!-- where that came from, e.g. a function, a request in the Network tab, a server log --> updateStats() in MenuScreenView.ts displays the value of stats.best.
3. **Traced back to:** refreshStats() in MenuScreenController.ts calls loadStatsFromServer() to retrieve data from /api/stats, then passes it to updateStats().
4. **Buggy code:** <!-- the file, function and line where the trace ended --> Line 53 in server/index.ts uses entries[i].score < best, preventing best, initially 0, from updating to a higher score.

<!-- It is okay to use your judgement here in limiting the number of traceback steps. We want to understand your reasoning for how you discovered where the bug was --> 

### Confirming the fix

<!-- How did you confirm the problem is gone? -->

Repeat the reproduction steps with a single saved score of 19 and confirm that the start screen displays "Best: 19" instead of "Best: 0".

---

## Ticket 3: The lemon only lands in four spots

### Actual cause

<!-- Which file and function was the bug in, and what was wrong with the code?
     Explain why that code produced the behavior described in the ticket. -->

**File / function:** src/screens/GameScreen/GameScreenView.ts — randomizeLemonPosition(), lines 85–86

**What was wrong:** Math.round(Math.random()) produces only 0 or 1. Each coordinate therefore takes only its minimum or maximum value, producing four possible positions.

### Fix

<!-- What did you change, and why does that change fix the problem? -->

Remove Math.round() from both coordinate calculations and use Math.random() directly. This allows positions throughout the allowed area.

### How did you find it?

#### Hypothesis testing

**What hypothesis ultimately led you to identify the buggy code, and why?**

The suspected cause is incorrect random coordinate calculations because the lemon only moves between four fixed positions. Checking the formulas tests this hypothesis. If correct, each coordinate will have only two possible values.

#### Backward tracing

<!-- Start from the symptom you saw and work backwards, one step at a time,
     until you reach the buggy line. For each step, note where you looked
     and what you saw there. Add steps as needed. -->

1. **Symptom:** <!-- what you saw, e.g. text on screen, console error --> The lemon only moved between four fixed positions.
2. **Traced back to:** <!-- where that came from, e.g. a function, a request in the Network tab, a server log --> Since the positions were restricted, the investigation focused on randomizeLemonPosition() in GameScreenView.ts, which calculates and sets the lemon’s coordinates.
3. **Traced back to:**
4. **Buggy code:** <!-- the file, function and line where the trace ended --> Math.round(Math.random()) on lines 85–86 produces only 0 or 1, giving each coordinate two possible values and resulting in only four positions.

<!-- It is okay to use your judgement here in limiting the number of traceback steps. We want to understand your reasoning for how you discovered where the bug was -->

### Confirming the fix

<!-- How did you confirm the problem is gone? --> 

Repeat the reproduction steps and click the lemon multiple times to confirm that it appears outside the original four fixed positions.

---

## Ticket 4: Stuck on the Game Over screen

### Actual cause

<!-- Which file and function was the bug in, and what was wrong with the code?
     Explain why that code produced the behavior described in the ticket. -->

**File / function:** src/screens/GameScreen/GameScreenController.ts — endGame(), line 87

**What was wrong:** The call to stopTimer() was written incorrectly as this.stopTimer;, so the method did not execute. The timer continued running and called endGame() every second, repeatedly returning to the Game Over screen.

### Fix

<!-- What did you change, and why does that change fix the problem? -->

Change this.stopTimer; to this.stopTimer();. This correctly calls the method and executes clearInterval() to stop the timer.

### How did you find it?

#### Hypothesis testing

**What hypothesis ultimately led you to identify the buggy code, and why?** The suspected cause is a timer that continues running after the game ends because the screen returns to Game Over after clicking Play Again. Checking the timer stopping method and how it is called tests this hypothesis. If correct, the timer will not have been stopped properly.

#### Backward tracing

<!-- Start from the symptom you saw and work backwards, one step at a time,
     until you reach the buggy line. For each step, note where you looked
     and what you saw there. Add steps as needed. -->

1. **Symptom:** <!-- what you saw, e.g. text on screen, console error --> After clicking Play Again, the screen returned to Game Over.
2. **Traced back to:** <!-- where that came from, e.g. a function, a request in the Network tab, a server log --> Repeatedly returning to Game Over suggested that endGame() was being called multiple times, likely because the timer had not stopped.
3. **Traced back to:**
4. **Buggy code:** <!-- the file, function and line where the trace ended --> Checking endGame() revealed that this.stopTimer; was missing parentheses, so stopTimer() did not execute and the timer continued calling endGame().

<!-- It is okay to use your judgement here in limiting the number of traceback steps. We want to understand your 
    reasoning for how you discovered where the bug was -->

### Confirming the fix

<!-- How did you confirm the problem is gone? -->

Refresh the page and repeat the reproduction steps to confirm that Play Again returns to the menu, stays there, and allows another game to start normally.

---

## Ticket 5: Leaderboard dates show "undefined"

### Actual cause

<!-- Which file and function was the bug in, and what was wrong with the code?
     Explain why that code produced the behavior described in the ticket. -->

**File / function:**

server/index.ts, lines 4 and 37

**What was wrong:**

The server saves dates as time, but updateLeaderboard() in ResultsScreenView.ts reads entry.timestamp. The names do not match, so the date displays as undefined.

### Fix

<!-- What did you change, and why does that change fix the problem? -->

Change time to timestamp on lines 4 and 37 in server/index.ts to match the name used in ResultsScreenView.ts.

### How did you find it?

#### Hypothesis testing

**What hypothesis ultimately led you to identify the buggy code, and why?**

The suspected cause is mismatched date names because scores display correctly but dates show undefine. Comparing the names in ResultsScreenView.ts and server/index.ts tests this hypothesis. If correct, the names will differ.

#### Backward tracing

<!-- Start from the symptom you saw and work backwards, one step at a time,
     until you reach the buggy line. For each step, note where you looked
     and what you saw there. Add steps as needed. -->

1. **Symptom:** <!-- what you saw, e.g. text on screen, console error --> Leaderboard dates displayed as undefined.
2. **Traced back to:** <!-- where that came from, e.g. a function, a request in the Network tab, a server log --> updateLeaderboard() in ResultsScreenView.ts uses entry.timestamp to display dates.
3. **Traced back to:** The entries saved by server/index.ts contain time instead of timestamp, so reading entry.timestamp returns undefined.
4. **Buggy code:** <!-- the file, function and line where the trace ended --> Lines 4 and 37 in server/index.ts use time instead of timestamp.

<!-- It is okay to use your judgement here in limiting the number of traceback steps. We want to understand your reasoning for how you discovered where the bug was -->

### Confirming the fix

<!-- How did you confirm the problem is gone? -->

Repeat the reproduction steps and confirm that the leaderboard now displays actual dates instead of undefined.