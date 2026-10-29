# FestPass CI

Jenkins assignment - FestPass event booking app.

- Added a `pollSCM` trigger to the Jenkinsfile so Jenkins builds on every commit
- Fixed the cancel bug in `BookingService.java`
- Builds #1 and #2 failed (3 tests), build #3 passed (28/28)

Evidence, CSVs and the final JAR are in the `submission` folder.

To run:
```
java ci/Build.java verify
java -jar build/festpass.jar help
```
