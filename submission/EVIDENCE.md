# FestPass Jenkins assignment - evidence

**Alias (Git author):** Bhavya  |  **Class code:** 001  |  **Challenge code:** 00158
**Job:** festpass-ci (Pipeline script from SCM)  |  **Agent:** student-laptop

## Job configuration
- Repository: `git://127.0.0.1:9418/festpass-java`, no credentials
- Branch: `*/main`, script path: `Jenkinsfile`
- Agent: `student-laptop` (label `student-laptop`, built-in node has 0 executors)
- Trigger: committed in the Jenkinsfile, no UI trigger:
  ```groovy
  triggers {
      pollSCM('* * * * *')
  }
  ```

## Builds
| Build | Cause | Commit | Result | Tests | JAR |
|---|---|---|---|---|---|
| #1 | Started by user (bootstrap) | `b191074` Configure my Jenkins assignment | FAILURE | 28 run, 3 failed | Not published |
| #2 | Started by an SCM change | `9203415` Personalize my help introduction | FAILURE | 28 run, 3 failed | Not published |
| #3 | Started by an SCM change | `4e94d33` Free the seat when a booking is cancelled | SUCCESS | 28 run, 0 failed | Published |

The commit printed by Jenkins in each build matched `git log -1 --oneline` at the time.

## Failed run (build #2)
- Stages: Checkout ✔ → Submission setup ✔ → Test ✘
- **Build JAR, Smoke test JAR and Save artifact were skipped** ("skipped due to earlier failure(s)"). No JAR was archived; only `submission-evidence.csv` was published (JarSha256 = NOT_PUBLISHED).
- Failing tests: `cancellationFreesSeat`, `cancellationsSurviveReload`, `cliWorkflowPersistsAcrossCommands`
- One failing assertion: `cancellationFreesSeat` - books S1 into a 1-seat event, cancels S1, then books S2. Expected S2 to get the seat; actual result was `IllegalArgumentException: SOLD OUT. Even the developer needs a ticket.`

## Diagnosis and fix
**Business rule violated:** cancelling a booking must free that seat so another student can book it.

**Cause:** `BookingService.cancel()` checked that the student had a booking but never removed them from the event's student set, so the seat stayed occupied. The other two failures follow from the same bug: the cancellation was never saved to the data file and never showed up in `list`.

**Fix (one line, no tests changed)** in `src/main/java/BookingService.java`:
```java
event.students.remove(studentId);
```

## Green run (build #3)
- 28 tests, 0 failures; JAR built; seven real-JAR smoke checks passed; artifact saved.
- JAR SHA-256: `097e7bfa83c654ff686c551d448505a4a30d590fc100e3ffcb9dbf50115f7b7e`
- Output of `java -jar festpass.jar help` from the downloaded JAR:
  ```
  FestPass - campus event registration (CI setup by Bhavya)
  Usage: java -jar build/festpass.jar [--data FILE] COMMAND
    add-event ID "TITLE" CAPACITY
    list
    book EVENT_ID STUDENT_ID
    cancel EVENT_ID STUDENT_ID
    roster EVENT_ID
    summary
    help
  ...
  ```

## Files submitted
- `build-1-red-manual/submission-evidence.csv`
- `build-2-red-auto/submission-evidence.csv`
- `build-3-green-auto/submission-evidence.csv`
- `build-3-green-auto/festpass.jar`
- Source repository: `festpass-java` (with its `.git` history)
