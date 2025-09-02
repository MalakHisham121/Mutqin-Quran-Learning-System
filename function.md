# System Functional Requirements

This document outlines the functional requirements for a system managing interactions among Students, Sheikhs, Admins, and Parents. It includes user authentication, session management, progress tracking, badge management, reporting, and notifications, with function inputs preserved as specified.

## User Authentication

- **login(email, password)**: Authenticate users using email and password.
- **signup(new UserDTO)**: Register a new user with a UserDTO containing user details.
- **loginwithGoogle()**: Authenticate users via Google OAuth.
- **signupwithGoogle()**: Register new users via Google OAuth.

## Student Features

- **Book session(session id, student id)**: Schedule a session using Calendly with session ID and student ID.
- **attend session(session id)**: Join a scheduled session using session ID.
- **show sessions()**: Display sessions filtered by status (upcoming/done) and period (month, week).
- **update profile(user id, new UserDTO)**: Modify user profile with user ID and updated UserDTO (shared with Sheikh, Admin, Parent).
- **delete profile(user id)**: Remove user profile using user ID (shared with Sheikh, Admin, Parent).
- **show profile(username)**: View user profile details using username (shared with Sheikh, Admin, Parent).
- **Search()**: Search for user profiles.
- **show progress(weekly/daily/monthly)()**: Display progress (sessions, quizzes, pages learned) filtered by period (weekly, daily, monthly).
- **show all progress()**: Display complete progress history.
- **Cancel Session(session id)**: Cancel a session using session ID (shared with Sheikh, Admin, Parent).

## Sheikh Features

- **create session(Session)**: Create a new session using Calendly with session details.
- **Show session details(session id)**: View details of a specific session using session ID (shared with Student, Parent).
- **show all paired students(Shikh id)**: List all students paired with a Sheikh using Sheikh ID.
- **fire badge(chosen badge id, student id or username)**: Assign a predefined badge to a student using badge ID and student ID or username.
- **choose badge(badge id)**: Select a badge using badge ID.
- **show badges()**: Display all available badges.
- **add badge(badge)**: Create a new badge.
- **Show all sessions(shikh id)**: Display all sessions for a Sheikh, with filters for weekly, daily, or monthly sessions.
- **Show Student progress(student id)**: View a student’s progress using student ID.
- **Send report(report)**: Send a report to a parent.
- **add points to student(student id)**: Assign points to a student using student ID.
- **edit points to student(student id)**: Modify a student’s points using student ID.
- **add pages learned for student(student id)**: Record pages learned for a student using student ID.

## Admin Features

- **show profile(user id)**: View user profile details using user ID (shared with Student, Sheikh, Parent).
- **show all student()**: List all registered students.
- **show all shikhs()**: List all registered Sheikhs.
- **show all parent()**: List all registered parents.

## Parent Features

- **recieve report()**: Receive reports via WhatsApp API or email.
- **show report(report id)**: View a specific report using report ID (alternative to WhatsApp/email).
- **pair student(student username)**: Pair a student with a parent using the student’s username.

## Automatic Features

- **Send report(report)**: Automatically send reports to parents based on a specified period.
- **send notification(notification)**: Send notifications to users.
- **Show badge(badge id)**: Display a badge assigned by a Sheikh using badge ID.
- **receive notification()**: Allow users to view received notifications.

## Notes

- **Progress Tracking**: Progress is measured by sessions attended, quizzes completed, and pages learned.
- **Shared Features**: Certain features (e.g., update profile, delete profile, show profile, cancel session, show session details) are common across multiple user roles.
- **Integration**: The system integrates with Calendly for session management and WhatsApp API/email for report delivery.
- **Filters**: Session and progress displays support filtering by time periods (daily, weekly, monthly) for better usability.
