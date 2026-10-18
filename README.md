# Study Room Booking and Management System - Final Implementation (Assessment 2)

A working study room booking system built with a clean, layered C# solution,
developed alongside Software Quality Assurance practice: automated tests, CI quality gates,
defect tracking and manual quality testing.

> **Scope note:** This is a student project and is not ready for release. FR12 (room utilisation reports) was pushed to the next sprint and security testing is limited. See the final report for the release decision.

## Solution structure

```text

StudyRoomBooking/
├── StudyRoomBooking.slnx
├── .github/workflows/ci.yml              GitHub Actions quality gates
├── src/
│   ├── StudyRoomBooking.Domain/          Room, User, Booking models + enums — no dependencies
│   ├── StudyRoomBooking.Application/     Interfaces, DTOs, and core services
│   ├── StudyRoomBooking.Infrastructure/  Entity Framework repositories + Supabase database context
│   ├── StudyRoomBooking.Web/             ASP.NET Core Razor Pages GUI
│   └── StudyRoomBooking.ConsoleDemo/     Console walkthrough of the application logic
└── tests/
    └── StudyRoomBooking.Tests/           MSTest suite for validation and regression checks
```

Dependencies flow one way: `Web` / `ConsoleDemo` / `Tests` → `Infrastructure` → `Application` → `Domain`.
Nothing in `Domain` or `Application` depends on `Infrastructure` or `Web`, so the move from in-memory storage to the Supabase database was made in the `Infrastructure` layer without changing the business rules.

***

## GUI framework choice

**ASP.NET Core Razor Pages** was chosen over WPF or WinForms because it runs across platforms and is the easiest way to demonstrate the system in a shared development environment.

- WPF and WinForms are Windows-only in practice, which restricts team collaboration and demoing.
- Razor Pages works across machines with the .NET SDK and a browser.
- Pages are thin adapters and do not contain business rules.
- Validation, overlap prevention, and authorization remain in the application services and are tested through the MSTest suite.
- Authentication uses sign up, login and forgot-password pages with role-based access.

***

## Features Completed

### Authentication & Roles
- Sign up (with terms and conditions popup), login / logout, and forgot password
- Role-based access (Student, Staff, Admin)

### Room Search & Booking (Student)
- Book a Room page: search available rooms by date, time, capacity, room type, and location, with room availability shown as a room-time matrix
- Room search results respect major-based access restrictions — specialised rooms (e.g. Design Studio, Engineering Lab) only appear for students in the required major
- View room details
- Create and modify bookings through popups
- Booking validation: rejects overlapping/double bookings for the same room and time slot
- Booking validation: rejects bookings in the past
- Booking validation: rejects bookings more than 60 days in advance
- Cancel a future booking, with a confirmation popup
- My Bookings page showing booking history and status (completed, cancelled, upcoming)

### Recurring Bookings (Staff)
- Create recurring bookings (daily, weekly, bi-weekly, monthly patterns)
- Create and modify recurring bookings through popups
- Occurrences where the room is already booked are skipped or flagged
- Recurring bookings are not limited by the standard 60-day advance-booking window

### Email Notifications
- Booking confirmation email, in English and Vietnamese
- Cancellation email when a booking is cancelled

### Multilingual Support
- English and Vietnamese, switched from a language selector without restarting the application (Google Translate widget)

### Admin
- Room management (create/update/delete rooms), including room access levels
- Booking management (admin can override or cancel bookings)
- User role management
- Not implemented: reports for booking statistics and room utilisation (FR12, deferred to the next sprint)

### Quality Engineering
- Unit test suite (MSTest + Moq) covering core booking, search/eligibility and admin access logic, run against an in-memory SQLite database
- 20 test cases: 12 automated and 8 manual
- Line coverage of 33.2%
- GitHub Actions CI with build, lint, tests with a 30% coverage threshold, and a smoke test

***

## Known limitations

- Security testing is limited: direct requests to admin actions, session tampering, input validation (e.g. SQL injection) and weak passwords are not tested.
- Performance was only measured on a locally hosted server.
- Accessibility was tested for language switching only, and availability was observed rather than tested.
- Test coverage (33.2%) is well below what a real release would need.

***

## Running it

Requires the .NET 8 SDK and a Supabase database connection string. The connection string is not included in this submission. It will be provided separately through a comment in the Canvas assignment.

```bash
# 1. From the extracted repository root, save the connection string locally.
# Replace <connection-string-received-securely> with the value provided privately.
dotnet user-secrets set \
  "ConnectionStrings:Supabase" \
  "<connection-string-received-securely>" \
  --project StudyRoomBooking/src/StudyRoomBooking.Web

# The value must use Npgsql format:
# Host=...;Port=5432;Database=postgres;Username=...;Password=...;SSL Mode=Require;Trust Server Certificate=true

# 2. Run the GUI
dotnet run --project StudyRoomBooking/src/StudyRoomBooking.Web

# 3. Then open in your browser
http://localhost:5176

# Run the test suite (uses in-memory SQLite, so no connection string is needed)
dotnet test StudyRoomBooking/StudyRoomBooking.slnx
```

The user secret is stored on the local computer outside the project folder. Do not place the real connection string in the ZIP, README, `appsettings.json`, `.env`, or source code.

The database schema is in `schema.sql`.

## Demo accounts

The demonstration user email addresses and passwords are not included in this submission. They will be provided separately to the teaching staff through a comment in the Canvas assignment, together with the temporary database connection string.

Use the supplied accounts to demonstrate the relevant role-based workflows:

| Role | Email | Password |
|---|---|---|
| Student | Provided privately | Provided privately |
| Staff | Provided privately | Provided privately |
| Admin | Provided privately | Provided privately |

These are assessment-only demonstration accounts. Do not commit their passwords to the repository or include them in the ZIP file.
