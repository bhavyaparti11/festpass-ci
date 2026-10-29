# Evidence

Alias: Bhavya
Class code: 001
Challenge code: 00158
Job: festpass-ci, agent: student-laptop
Repo: git://127.0.0.1:9418/festpass-java, branch */main, script path Jenkinsfile

## Builds

| Build | Started by | Commit | Result |
|---|---|---|---|
| 1 | me (Build Now) | b191074 | FAILURE - 3 of 28 tests failed |
| 2 | SCM change | 9203415 | FAILURE - 3 of 28 tests failed |
| 3 | SCM change | 4e94d33 | SUCCESS - 28 tests, 0 failures |

In the failed builds, Build JAR, Smoke test JAR and Save artifact were skipped, so no JAR was made. Only the evidence CSV was saved.

## The bug

The failing test was `cancellationFreesSeat`: S1 books the only seat, cancels, then S2 tries to book and gets "SOLD OUT".

`cancel()` checked that the student had a booking but never removed them, so the seat stayed taken. I added one line:

```java
event.students.remove(studentId);
```

The other two failing tests (cancellation after reload, and the CLI list) failed for the same reason.

## Final JAR

`java -jar festpass.jar help` prints:

```
FestPass - campus event registration (CI setup by Bhavya)
```

JAR SHA-256: 097e7bfa83c654ff686c551d448505a4a30d590fc100e3ffcb9dbf50115f7b7e
