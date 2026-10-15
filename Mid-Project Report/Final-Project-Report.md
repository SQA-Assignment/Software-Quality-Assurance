# I.	Introduction


# II. Project Completion & Final Implementation

# III. Requirements Verification & Traceability
## 3.1 Review and Finalisation of Requirements
The functional requirements (FR1 - FR12) and non-functional requirements (NFR1 - NFR7) defined in Assessment 1 (Tables 3 & 4) have been reviewed against the final implementation. All twelve functional requirements remain valid and relevant to the completed system; no requirements were removed. Eleven of the twelve functional requirements have now been implemented. FR12 (administrator reporting) has been formally deferred to a later sprint, as documented in Section 1.3.

Functional Requirements
No ID	Functional requirements
FR1	    Students can search available rooms using date, time, location, capacity and room type filters
FR2	    Students can book an available room
FR3	    The system shall validate the availability of study rooms before confirming a booking
FR4	    The system will validate programme or major eligibility before allowing specialised room bookings
FR5	    Student will receive booking confirmation via email
FR6	    Student can modify future bookings
FR7	    Student can cancel future bookings
FR8	    Student can view booking history
FR9	    Academic staff can create recurring bookings
FR10	Administrators can manage room information and room access rules
FR11	Administrators can override or cancel bookings when necessary
FR12	Administrators can generate room utilisation reports

Non-Functional Requirements
No ID	Requirements	Measurement
NFR1	Performance	    Room searches will return results within 2 seconds for at least 50 reservations
NFR2	Reliability	    The system will prevent duplicate bookings for the same room and time slot
NFR3	Security	    Only authorised users may access administrative functions
NFR4	Usability	    First-time users will complete a booking within 4 minutes without assistance
NFR5	Maintainability	The system will use a modular architecture to simplify future development
NFR6	Accessibility	Text shall remain readable, and navigation will support keyboard interaction where appropriate. The system will support both Vietnamese and English interfaces, allowing users to switch languages without restarting the application
NFR7	Availability	The prototype shall remain operational throughout demonstration and testing sessions

## 3.2 Updated Requirements Traceability Matrix
The initial test cases (TC-01 to TC-08) designed in Assessment 1 (Table 8) have been reviewed and expanded with twelve additional test cases (TC-09 to TC-20) to achieve broader coverage across all functional and non-functional requirements, bringing the total to 20 test cases ahead of execution in Task 3.

| Test ID   | Requirement Covered    | Test Case Description                                                    | Test Steps (Given/When/Then)                                                                                                                                                                                                                                                    | Expected Result                                                                                                  |
| --------- | ---------------------- | ------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **TC-01** | FR2, FR3               | Book an available room successfully                                      | **Given** a room is available for the selected date/time<br>**When** a student submits a booking request<br>**Then** the booking is created                                                                                                                                     | Booking succeeds. `BookingResult.Success = true`; confirmation message returned                                  |
| **TC-02** | FR3, NFR2              | Reject a double-booking for the same room and time slot                  | **Given** a room is already booked for a time slot<br>**When** another student attempts to book the same room and time<br>**Then** the booking is rejected                                                                                                                      | `BookingResult.Success = false`; validation message explains the conflict                                        |
| **TC-03** | FR4, NFR3              | Hide a specialised room from an ineligible student’s search results      | **Given** a student’s programme doesn’t match a room’s access requirement (e.g. a Business student searching while a room is restricted to Design students)<br>**When** the student searches for available rooms<br>**Then** the restricted room does not appear in the results | The restricted room is excluded from the returned room list                                                      |
| **TC-04** | FR4, NFR3              | Show a specialised room in an eligible student’s search results          | **Given** a student’s programme matches a room’s access requirement<br>**When** the student searches for available rooms<br>**Then** the restricted room appears in the results                                                                                                 | The restricted room is included in the returned room list                                                        |
| **TC-05** | FR6                    | Modify an existing future booking to a new available time                | **Given** a student has an existing future booking<br>**When** the student changes the booking to a new available date/time<br>**Then** the booking is updated successfully                                                                                                     | `BookingResult.Success = true`; booking reflects the new date/time, and the original time slot becomes available |
| **TC-06** | FR7                    | Cancel a future booking                                                  | **Given** a future booking exists<br>**When** the student cancels the booking<br>**Then** the booking will be removed and the room will become available for future bookings                                                                                                    | Booking status updated to cancelled; room reappears in availability search                                       |
| **TC-07** | FR3 (edge case)        | Reject a booking request for a date/time in the past                     | **Given** a student attempts to book a room for a date/time that has already passed<br>**When** the booking request is submitted<br>**Then** the system rejects the booking                                                                                                     | `BookingResult.Success = false`; message explains the date is invalid, or `ArgumentException` thrown             |
| **TC-08** | FR2 (edge case)        | Reject a booking with a missing / invalid student ID                     | **Given** a booking request with an empty or null student ID<br>**When** the booking is submitted<br>**Then** the system rejects it                                                                                                                                             | `ArgumentException` thrown or `BookingResult.Success = false` with a clear message                               |
| **TC-09** | FR1                    | Search returns rooms matching all selected filters                       | **Given** rooms with varying date, time, capacity, and room type<br>**When** a student searches using a combination of filters<br>**Then** only rooms matching all criteria are returned                                                                                        | Correct filtered result set; no non-matching rooms included                                                      |
| **TC-10** | FR1 (negative)         | Search with no matching rooms returns a clear empty-result message       | **Given** no rooms match the selected filters<br>**When** a student searches<br>**Then** the system displays a “no rooms found” message                                                                                                                                         | Empty result set; clear message shown, no error thrown                                                           |
| **TC-11** | FR5                    | Confirmation email is sent with correct booking details                  | **Given** a booking is successfully created<br>**When** the confirmation email is generated<br>**Then** the email contains the correct room, date, time, and booking reference                                                                                                  | Email content matches the actual booking details                                                                 |
| **TC-12** | FR8                    | Booking history displays correct status for past and upcoming bookings   | **Given** a student has a mix of expired, cancelled, and upcoming bookings<br>**When** the student views booking history<br>**Then** each booking shows the correct status                                                                                                      | All bookings listed with accurate status labels                                                                  |
| **TC-13** | FR9                    | Recurring booking creates a linked series, skipping conflicts            | **Given** academic staff request a weekly recurring booking over 4 weeks<br>**When** one occurrence conflicts with an existing booking<br>**Then** the system creates the other 3 bookings and flags/skips the conflicting one                                                  | 3 bookings created; conflicting occurrence flagged, not silently dropped                                         |
| **TC-14** | FR10, NFR3             | Administrator can update room access rules                               | **Given** an administrator is logged in<br>**When** they update a room’s access rules (e.g. restrict to Design students)<br>**Then** the new rule applies to all future bookings of that room                                                                                   | Updated rule saved and enforced on subsequent booking attempts                                                   |
| **TC-15** | FR11, NFR3             | Non-administrator cannot override or cancel another user’s booking       | **Given** a logged-in student (non-admin)<br>**When** they attempt to cancel another student’s booking<br>**Then** the system denies the action                                                                                                                                 | Action blocked; appropriate authorisation error returned                                                         |
| **TC-16** | FR11                   | Administrator override cancels booking and notifies the affected student | **Given** an administrator overrides an existing booking<br>**When** the override is confirmed<br>**Then** the booking is cancelled, the room becomes available, and the student is notified                                                                                    | Booking cancelled; room freed; notification sent/logged                                                          |
| **TC-17** | NFR1 (Performance)     | Room search returns results within 2 seconds for 50+ reservations        | **Given** at least 50 sample reservations exist in the database<br>**When** a student performs a search<br>**Then** results are returned within 2 seconds                                                                                                                       | Search completes within the 2-second threshold                                                                   |
| **TC-18** | NFR4 (Usability)       | First-time user completes a booking within 4 minutes unassisted          | **Given** a first-time user with no prior instruction<br>**When** they attempt to search for and book a room<br>**Then** they complete the booking within 4 minutes                                                                                                             | Booking completed within the time threshold, without external help                                               |
| **TC-19** | NFR6 (Accessibility)   | Language switch applies immediately without restarting the application   | **Given** a user is on any page of the application<br>**When** they select a different language (Vietnamese/English)<br>**Then** the interface text updates without requiring the program restart                                                                               | All visible text updates to the selected language; no restart required                                           |
| **TC-20** | NFR2, FR3 (Regression) | Re-verify double-booking prevention after database migration             | **Given** the system now uses a database instead of in-memory storage<br>**When** two students attempt to book the same room and time concurrently<br>**Then** the system still correctly rejects the conflicting booking                                                       | Double-booking still prevented after the architecture change (regression check)                                  |

Requirements Traceability Matrix
| Requirement ID | Requirement Summary                                        | Related NFR (if any) | Test Cases                 | Status                 |
| -------------- | ---------------------------------------------------------- | -------------------- | -------------------------- | ---------------------- |
| **FR1**        | Search available rooms by filters                          | NFR1                 | TC-09, TC-10               | Pending execution      |
| **FR2**        | Book an available room                                     | —                    | TC-01, TC-08               | Passed                 |
| **FR3**        | Validate room availability before confirming booking       | NFR2                 | TC-01, TC-02, TC-07, TC-20 | Passed (TC-20 pending) |
| **FR4**        | Validate programme/major eligibility for specialised rooms | NFR3                 | TC-03, TC-04               | Passed                 |
| **FR5**        | Send booking confirmation via email                        | —                    | TC-11                      | Passed                 |
| **FR6**        | Modify future bookings                                     | —                    | TC-05                      | Passed                 |
| **FR7**        | Cancel future bookings                                     | —                    | TC-06                      | Passed                 |
| **FR8**        | View booking history                                       | —                    | TC-12                      | Pending execution      |
| **FR9**        | Academic staff can create recurring bookings               | —                    | TC-13                      | Pending execution      |
| **FR10**       | Administrators manage room information and access rules    | NFR3                 | TC-14                      | Pending execution      |
| **FR11**       | Administrators override or cancel bookings                 | NFR3                 | TC-15, TC-16               | Pending execution      |
| **FR12**       | Administrators generate room utilisation reports           | —                    | None — out of scope        | Deferred (see 2.4)     |

Non-Requirements Traceability Matrix
| Requirement ID | Requirement Summary                                  | Test Cases                 | Status                                                     |
| -------------- | ---------------------------------------------------- | -------------------------- | ---------------------------------------------------------- |
| **NFR1**       | Performance (search ≤2s, 50+ reservations)           | TC-17                      | Pending execution                                          |
| **NFR2**       | Reliability (no duplicate bookings)                  | TC-02, TC-20               | Passed (TC-20 pending)                                     |
| **NFR3**       | Security (authorised access only)                    | TC-03, TC-04, TC-14, TC-15 | Partially verified; TC-14/15 pending                       |
| **NFR4**       | Usability (4-min unassisted booking)                 | TC-18                      | Pending execution                                          |
| **NFR5**       | Maintainability (modular architecture)               | —                          | Supported by database migration; no direct test case       |
| **NFR6**       | Accessibility (language switch; keyboard navigation) | TC-19                      | Partially covered — TC-19 verifies language switching only |
| **NFR7**       | Availability (operational during demo/testing)       | —                          | Observed during testing sessions; not a discrete test case |

## 3.3.	Requirements Not Fully Verified or Out of Scope
- FR12 — Administrators can generate room utilisation reports
=> Formally deferred to a later sprint (see Section 1.3). The team prioritised database implementation, administrator management, email confirmation, multi-language support, and recurring bookings for this assignment. FR12 remains valid and will be addressed in a future iteration.
- TC-09 through TC-20 — Designed but not yet executed (Test in Task 3, will update once done)
=> Twelve new test cases were added during this review to close coverage gaps identified in Assessment 1's original eight test cases, extending coverage to FR1, FR5, FR8, FR9, FR10, FR11, and all seven NFRs. These have been designed but not yet run against the completed system; execution and results recording are addressed in Task 3.
- NFR6 — Partially covered
=> The original test case for NFR6 covered both keyboard navigation and language switching. Following a revision to TC-19, the test case now verifies language switching only; keyboard navigation through the booking flow is not currently covered by any test case. This is a known gap the team has chosen to accept at this stage, given the project's limited timeframe and focus on core booking functionality; it is flagged here for transparency rather than silently omitted, and may be addressed in a future iteration if time permits.
- NFR5 and NFR7 — No discrete test case
=> NFR5 (Maintainability) is supported by the architectural decision to migrate from in-memory repositories to a database (Section 1.3), rather than verified through a specific automated test. NFR7 (Availability) will be confirmed through observation during demonstration and testing sessions rather than a standalone test case, as uptime is not something a unit or integration test can meaningfully simulate for this prototype.

# IV. Test Execution and Final Test Results

# V. Test Automation and Quality Engineering

# VI. Continuous Integration and DevOps QA
# 6. Continuous Integration and DevOps QA

## 6.1. Development and integration workflow

The team uses a feature-branch workflow on GitHub after learning about the CI/CD lecture. From now on, each member develops on a separate branch and commits small, clearly described changes. When a change is ready, the member opens a pull request into main. The main branch is protected (Code Review): direct pushes are blocked, and a pull request can only be merged when the automated checks pass and at least one teammate has approved it.

The flow is:

1. Create a branch from main for a feature or fix
2. Commit locally and run `dotnet build` and `dotnet test` before pushing
3. Push the branch and open a pull request
4. GitHub Actions runs the CI workflow automatically
5. A teammate reviews the code and the CI result, then approves or requests changes
6. After approval and a green build, the pull request is merged into main. CI runs again on the merged code

CI is appropriate because the project is a small .NET application with an automated MSTest suite and a shared repository, so frequent integration reduces merge problems and catches faults early. Continuous deployment was not implemented because the prototype is only run in demonstration and testing sessions, so there is no production environment to deploy to.

## 6.2. CI implementation

The workflow is defined in `.github/workflows/ci.yml`. It runs on every push and pull request to main and uses read-only repository permissions. It has two jobs.

**Job 1: Restore, build and test.** This job checks out the code, installs .NET 8, restores packages, builds the solution in Release mode, runs a basic lint check, runs all automated tests with code coverage, and checks the coverage against a minimum. Test results and coverage reports are saved as an artefact after every run.

**Job 2: Smoke test.** This job only runs if Job 1 passes. It starts the web application and sends HTTP requests until the app responds, for up to about 90 seconds. The application connects to the database using a connection string stored as a GitHub repository secret, so no credentials appear in the code or logs. If the secret is missing or the app does not respond, the job fails and prints the application log.

## 6.3. Quality gates

**Quality Gates table**

| Gate | Check | Purpose | Related Requirement |
|---|---|---|---|
| 1. Build | Solution restores and compiles in Release mode | Prevents broken code reaching main | All |
| 2. Linting | `dotnet format analyzers` report no errors | Catches code quality problems automatically | NFR5 Maintainability |
| 3. Automated tests | All 20 MSTests pass (12 automated tests and 8 manual tests) | Protects rules such as duplicate-booking prevention | FR2, FR3, NFR2 |
| 4. Coverage threshold | Line coverage of at least 30% | Ensures a minimum amount of code is tested | NFR2, NFR5 |
| 5. Smoke test | App starts and responds with the real database connection | Confirms the system runs, not only compiles | NFR7 Availability |
| 6. Code Review | At least one approving review before merge (Code Review) | Catches logic and design problems automation cannot | All |

If any gate fails, the pull request cannot be merged until the problem is fixed. The coverage threshold is low (30%) because the project is a prototype. The team plans to raise it as more tests are added. The lint check only reports analyzer errors, so it is a basic check and does not enforce full code style.

## 6.4. Smoke and sanity testing

**Smoke testing.** The smoke test is automated in CI. It does not test business rules. It checks only that the application starts, connects to its configuration, and responds. HTTP 200 means the page loaded, and 302 means the app redirected the request, for example, to a login page. Both show the app is running. This catches faults that unit tests cannot find, such as missing configuration, a broken start-up, or a failed database connection.

**Sanity testing.** Sanity testing is done manually after a small fix. Instead of running every test again, the team re-checks only the affected behaviour and its neighbours. For example, after a change to the double-booking rule, the team re-runs the related test cases (TC-03 and TC-04) and then tries one booking in the interface.

**Evidence.**

Both tests pass screenshot

![Both jobs pass](images/ci-both-jobs-pass.png)

Check code coverage threshold screenshot

![Coverage threshold check](images/ci-coverage-threshold.png)

Run automated test screenshot

![Automated tests run](images/ci-automated-tests.png)

Code Review ruleset setup screenshots

![Ruleset setup 1](images/ruleset-setup-1.png)

![Ruleset setup 2](images/ruleset-setup-2.png)

![Ruleset setup 3](images/ruleset-setup-3.png)

A failed run screenshot

![Failed CI run](images/ci-failed-run.png)

Create pull request test screenshot

![Pull request created](images/pr-created.png)

Approved pull request by one reviewer screenshot

![Pull request approved](images/pr-approved.png)

## 6.5. Limitations and future improvements

- The pipeline does not deploy the application because the prototype is only used for demonstrations.
- The coverage threshold is low, and the lint check is basic. Both could be made stricter.
- The smoke test uses a real external database (Supabase), so it can fail if that service is down, even when the code is correct. A separate test database would be safer.
- Repository secrets are not available to pull requests from forks, which is acceptable for a three-person team.
- UI, email, recurring-booking and language-switching tests remain manual.
- The smoke test checks only the home page, not full booking flows.

# VII. Defect Management and Root Cause Analysis

# VIII. Quality Testing

# IX. Quality Metrics and Final Release Decision

# X. AI-Assisted Development and Critical Reflection

# XI. Quality Improvement and Future Recommendations

# XII. Team Contribution and Professional Reflection