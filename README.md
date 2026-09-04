# RaceDay Event Management System - Part 1 Submission

## Student Information
- **Name:** Bawinile Mahlangu
- **Student Number:** ST10494448
- **Module:** Programming 2B (PROG6212)
- **Date:** 04 September 2026

---

## System Description

I've been to quite a few community events around Joburg - park runs, charity walks, even helped out at a local 5km once. What I noticed every time was how much paperwork was involved. Organisers were walking around with clipboards, trying to keep track of who had registered, who had paid, and who had finished. It was messy and time-consuming.

That's why I decided to build RaceDay. It's an event management system designed for the South African running, walking, and cycling community. 

The idea is simple - instead of using paper forms and spreadsheets, organisers can create events online, and participants can register from their phones. When the event is over, organisers can upload results, and participants can see how they did.

The system has two main user types - Organisers who run the events, and Participants who take part in them. Each one sees a different side of the system depending on their role.

---

## User Roles

### 1. Organiser

Organisers are the people who create and manage events. These could be running club managers, charity event coordinators, or anyone putting on a community race.

**What they can do:**
- Create new events with all the details - name, date, location, distance, and type (run/walk/cycle)
- Edit events if something changes (like the venue or start time)
- Delete events if they get cancelled
- Add categories to events (like age groups or distance groups)
- See who has enrolled for their events
- Capture results after the event - finish times and positions
- Upload a banner image for their event to make it look more appealing
- View a dashboard showing all their events and how many people are registered

**I designed it this way because:** When I helped organise that community walk, we kept losing track of who was registered. Having all this information in one place would have saved us so much time.

---

### 2. Participant

Participants are the people who want to take part in events. They can be anyone from competitive runners to people doing a charity walk for the first time.

**What they can do:**
- Create an account with their personal details
- View and edit their profile information
- Browse upcoming events and filter them by date, type, or location
- See event details like the route, distance, and categories
- Enrol for events and choose which category they want to compete in
- View their enrolment history and check if their entry is confirmed
- See their personal results - finish times and positions from events they've completed
- Upload a profile picture

**I designed it this way because:** I've registered for events before where I had to fill in the same information on paper every single time. With this system, you do it once and it's saved for future events.

---

## CI/CD Pipeline Screenshot

> **Note:** I'll add a screenshot of my green build here once I've set up my GitHub Actions workflow and it's passing successfully.

*(I'll take a screenshot showing the green check mark next to my latest commit and add it here.)*

---

## YouTube Demonstration Video

> **Note:** I'll add my unlisted YouTube video link here once I've recorded and uploaded my demonstration.

**What I'll cover in the video:**
- Walkthrough of all my planning documents
- Explanation of why I designed my ERD the way I did
- Overview of my API endpoint plan
- Live demonstration of my SQL script running in SSMS
- Showing the database structure and sample data

---

## Project Folder Structure

I organised my project like this so everything is easy to find:

```
/prog6212-poe-st10494448/
├── /docs/
│   ├── ERD.png                    # My Entity Relationship Diagram
│   ├── API_Endpoint_Plan.md       # All my API endpoints documented
│   └── RaceDay_Schema.sql         # My SQL database creation script
├── README.md                      # This file you're reading
└── /.github/
    └── /workflows/
        └── ci.yml                 # My GitHub Actions workflow
```

---

## Section A: Entity Relationship Diagram (ERD)

### How I Designed My Database

When I started designing the database, I first thought I could just have one table for everyone. But then I realised that organisers and participants need different information. 

An organiser needs things like their organisation name and website, while a participant needs emergency contact details and medical conditions. I split them into separate tables connected to a main Users table to avoid having lots of empty fields.

### The Entities I Included

I ended up with **8 tables** in my database:

1. **Users** - The main table that stores everyone's login details and basic personal info
2. **Organisers** - Extra information for users who are organisers
3. **Participants** - Extra information for users who are participants
4. **Events** - All the event details
5. **Categories** - Different age or distance groups for events
6. **EventCategories** - Connects events to categories (many-to-many relationship)
7. **Enrolments** - Records when a participant signs up for an event
8. **Results** - Stores finish times and positions

### The Relationships Between Tables

- **Users to Organisers:** One-to-one (a user is either an organiser or participant)
- **Users to Participants:** One-to-one (same as above)
- **Organisers to Events:** One-to-many (one organiser can create many events)
- **Events to EventCategories:** One-to-many (one event can have many categories)
- **Categories to EventCategories:** One-to-many (one category can be in many events)
- **Participants to Enrolments:** One-to-many (one participant can enrol in many events)
- **Events to Enrolments:** One-to-many (one event can have many enrolments)
- **Enrolments to Results:** One-to-one (each enrolment can have one result)

### What I'd Change If I Had More Time

I would probably add a payments table to handle entry fees, and maybe a route table to store GPS data for the event courses. I could also add a weather table to store historical weather data for events.

### ERD Diagram

*I HAVE ATTACHED THE PDF FILE!*

---

## Section B: API Endpoint Plan

### Overview of My Endpoints

I planned 30+ endpoints across 6 different resource groups. I chose session-based authentication because we'll be building an MVC application in Part 3 that needs to display different interfaces for organisers and participants.

### Authentication Endpoints

| Method | Route | Description | Who Can Use It |
|--------|-------|-------------|----------------|
| POST | `/auth/register` | Create a new user account | Anyone |
| POST | `/auth/login` | Log in and start a session | Anyone |
| POST | `/auth/logout` | End your session | Logged in users |
| GET | `/auth/me` | Check who you're logged in as | Logged in users |

### User Endpoints

| Method | Route | Description | Who Can Use It |
|--------|-------|-------------|----------------|
| GET | `/users/profile` | View your profile | Logged in users |
| PUT | `/users/profile` | Update your profile | Logged in users |
| PUT | `/users/password` | Change your password | Logged in users |

### Event Endpoints

| Method | Route | Description | Who Can Use It |
|--------|-------|-------------|----------------|
| GET | `/events` | Get all events | Logged in users |
| GET | `/events/{id}` | Get one event's details | Logged in users |
| POST | `/events` | Create a new event | Organisers only |
| PUT | `/events/{id}` | Update an event | Organisers (owners only) |
| DELETE | `/events/{id}` | Delete an event | Organisers (owners only) |
| GET | `/events/organiser` | Get your events | Organisers only |

### Category Endpoints

| Method | Route | Description | Who Can Use It |
|--------|-------|-------------|----------------|
| GET | `/categories` | Get all categories | Logged in users |
| GET | `/categories/{id}` | Get one category | Logged in users |
| POST | `/categories` | Create a category | Organisers only |
| PUT | `/categories/{id}` | Update a category | Organisers only |
| DELETE | `/categories/{id}` | Delete a category | Organisers only |

### Enrolment Endpoints

| Method | Route | Description | Who Can Use It |
|--------|-------|-------------|----------------|
| GET | `/events/{eventId}/enrolments` | View all enrolments for an event | Organisers (event owners) |
| POST | `/events/{eventId}/enrol` | Enrol in an event | Participants only |
| PUT | `/enrolments/{enrolmentId}` | Update your enrolment | Participants (owners only) |
| GET | `/participants/enrolments` | View your enrolments | Participants only |

### Result Endpoints

| Method | Route | Description | Who Can Use It |
|--------|-------|-------------|----------------|
| GET | `/events/{eventId}/results` | View event results | Logged in users |
| POST | `/events/{eventId}/results` | Add a result | Organisers (event owners) |
| PUT | `/results/{resultId}` | Update a result | Organisers (event owners) |
| DELETE | `/results/{resultId}` | Delete a result | Organisers (event owners) |
| GET | `/participants/results` | View your results | Participants only |

### Why I Designed It This Way

I used RESTful principles because they're standard and easy to understand. GET for reading, POST for creating, PUT for updating, and DELETE for removing.

For the role-based access, I made sure organisers can only manage their own events, and participants can only see their own data. Public endpoints like register and login are accessible to everyone.

I included things like enrolment status (Pending, Confirmed, Cancelled) because in real life, organisers need to confirm entries, sometimes after payment verification.

### Full API Endpoint Plan Document

*(I'll add my complete API_Endpoint_Plan.md file in the /docs folder)*

---

## Section C: SQL Database Script

### My Script Overview

I wrote a SQL script that creates the entire RaceDay database from scratch. It includes:

- The CREATE DATABASE statement
- All 8 tables with their columns, data types, and constraints
- Primary and foreign keys
- Check constraints for validation
- Indexes for performance
- Realistic seed data for testing
- Views for common queries
- Stored procedures for common operations
- A trigger for automatic result creation

### The Seed Data I Included

I added South African names and events to make it realistic:

**Users:**
- Thabo Mokoena (Organiser - Joburg Running Club)
- Lindiwe Nkosi (Organiser - Cape Town Cycle Tours)
- Sipho Zulu (Participant)
- Zanele Ndlovu (Participant)

**Events:**
- Soweto Marathon 2026
- Cape Town Cycle Tour 2026
- Joburg Park Run Summer Series
- Two Oceans Ultra Marathon 2026

**Categories:**
- Under 20, Senior, Master (age groups)
- 10km, 21km, 42km (distance groups)

### Views I Created

**EventSummary:** Shows events with organiser names and enrolment counts
**ParticipantResults:** Shows participant results with rankings

### Stored Procedures I Created

**GetOrganiserEvents:** Gets all events for a specific organiser with enrolment statistics
**EnrolParticipant:** Handles the enrolment process with validation (checks if event is full, if already enrolled)

### Trigger I Created

**trg_EnrolmentStatus_To_Result:** Automatically creates a result record when an enrolment status changes to Confirmed

### Full SQL Script

*(I'll add my RaceDay_Schema.sql file in the /docs folder)*

---

## Setup Instructions

### How to Run My SQL Script

1. Open SQL Server Management Studio (SSMS)
2. Connect to your SQL Server instance
3. Open the RaceDay_Schema.sql file from my /docs folder
4. Press F5 or click Execute
5. The database will be created with all tables and sample data

### What You Should See

After running the script:
- RaceDayDB appears in your databases list
- 8 tables are created
- Tables contain sample data
- Views and stored procedures are created
- No errors in the Messages tab

### How to Verify It Worked

Run these queries to check:
```sql
SELECT * FROM Users;
SELECT * FROM Events;
SELECT * FROM Enrolments;
```

---

## References

1. Microsoft. (2026). *SQL Server Documentation*. Available at: https://learn.microsoft.com/en-us/sql/ (Accessed: 4 September 2026).

2. Microsoft. (2026). *Entity Framework Core Documentation*. Available at: https://learn.microsoft.com/en-us/ef/core/ (Accessed: 4 September 2026).

3. GitHub. (2026). *GitHub Actions Documentation*. Available at: https://docs.github.com/en/actions (Accessed: 4 September 2026).

4. ASP.NET Core. (2026). *RESTful API Design Guidelines*. Available at: https://learn.microsoft.com/en-us/aspnet/core/web-api/ (Accessed: 4 September 2026).

5. Chen, P. (1976). 'The Entity-Relationship Model - Toward a Unified View of Data', *ACM Transactions on Database Systems*, 1(1), pp. 9-36. I read this paper to understand how ER diagrams should be structured. I learned that relationships need to show cardinality, which is why I used crow's foot notation.

6. Fielding, R.T. (2000). *Architectural Styles and the Design of Network-based Software Architectures*. Doctoral dissertation, University of California, Irvine. This helped me understand RESTful API design principles.

---

**Student Signature:** B. Mahlangu
**Date:** 04 September 2026
