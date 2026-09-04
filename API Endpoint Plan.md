# Section B: API Endpoint Plan - RaceDay System

## Student Information
- **Name:** Bawinile Mahlangu
- **Student Number:** ST10494448
- **Module:** Programming 2B (PROG6212)
- **Date:** 04 September 2026

---

## Base URL
`https://api.raceday.co.za/api/`

---

## 1. Authentication Endpoints

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|-------------|-------|-------------|---------------|--------------|-------------------|
| POST | `/auth/register` | Register a new user account. Users can sign up as either an Organiser or Participant. | None (Public) | `{ "email": "string", "password": "string", "firstName": "string", "lastName": "string", "phone": "string", "role": "Organiser|Participant" }` | **201 Created** - Returns user object with ID and role<br>**400 Bad Request** - Validation errors (missing fields, invalid email format)<br>**409 Conflict** - Email already registered |
| POST | `/auth/login` | Authenticate a user and create a session. Stores user ID and role in the session cookie. | None (Public) | `{ "email": "string", "password": "string" }` | **200 OK** - Returns user object with role and sets session cookie<br>**401 Unauthorized** - Invalid email or password |
| POST | `/auth/logout` | End the current user session. Clears the session cookie. | Any (Logged in) | None | **204 No Content** - Session cleared successfully<br>**401 Unauthorized** - User not logged in |
| GET | `/auth/me` | Get the current authenticated user's information. Useful for checking session status. | Any (Logged in) | None | **200 OK** - Returns user object<br>**401 Unauthorized** - No active session |

---

## 2. User Profile Endpoints

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|-------------|-------|-------------|---------------|--------------|-------------------|
| GET | `/users/profile` | Get the current user's full profile information including role-specific details. | Any (Logged in) | None | **200 OK** - Returns full user profile<br>**401 Unauthorized** - Not logged in |
| PUT | `/users/profile` | Update the current user's profile information. Profile picture can be sent as base64 string. | Any (Logged in) | `{ "firstName": "string", "lastName": "string", "phone": "string", "profilePicture": "string (base64, optional)" }` | **200 OK** - Returns updated user profile<br>**400 Bad Request** - Validation errors<br>**401 Unauthorized** - Not logged in |
| PUT | `/users/password` | Change the current user's password. Requires current password for verification. | Any (Logged in) | `{ "currentPassword": "string", "newPassword": "string" }` | **200 OK** - Password updated successfully<br>**400 Bad Request** - New password doesn't meet requirements<br>**401 Unauthorized** - Current password incorrect |

---

## 3. Event Endpoints

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|-------------|-------|-------------|---------------|--------------|-------------------|
| GET | `/events` | Get all events with optional filtering. Can filter by upcoming, event type, or search term. | Any (Logged in) | None (Query params: upcoming=true/false, type=Run/Walk/Cycle, search=string) | **200 OK** - Returns list of Event objects<br>**401 Unauthorized** - Not logged in |
| GET | `/events/{id}` | Get detailed information for a specific event including its categories and enrolment count. | Any (Logged in) | None | **200 OK** - Returns Event object with categories and enrolments count<br>**404 Not Found** - Event doesn't exist<br>**401 Unauthorized** - Not logged in |
| POST | `/events` | Create a new event. Only organisers can create events. Banner image can be uploaded as base64. | Organiser | `{ "name": "string", "description": "string", "date": "datetime", "location": "string", "distance": "decimal", "eventType": "Run|Walk|Cycle", "bannerImage": "string (base64, optional)", "categoryIds": ["int"], "maxParticipants": "int (optional)" }` | **201 Created** - Returns new Event object<br>**400 Bad Request** - Validation errors<br>**403 Forbidden** - User is not an Organiser<br>**401 Unauthorized** - Not logged in |
| PUT | `/events/{id}` | Update an existing event. Only the organiser who created the event can update it. | Organiser | `{ "name": "string", "description": "string", "date": "datetime", "location": "string", "distance": "decimal", "eventType": "Run|Walk|Cycle", "bannerImage": "string (base64, optional)", "categoryIds": ["int"], "maxParticipants": "int (optional)" }` | **200 OK** - Returns updated Event object<br>**400 Bad Request** - Validation errors<br>**403 Forbidden** - Not the event owner or not Organiser<br>**404 Not Found** - Event doesn't exist<br>**401 Unauthorized** - Not logged in |
| DELETE | `/events/{id}` | Delete an event. This will cascade delete all related enrolments and results. | Organiser | None | **204 No Content** - Event deleted successfully<br>**403 Forbidden** - Not the event owner or not Organiser<br>**404 Not Found** - Event doesn't exist<br>**401 Unauthorized** - Not logged in |
| GET | `/events/organiser` | Get all events created by the current logged-in organiser. Shows enrolment counts. | Organiser | None | **200 OK** - Returns list of Event objects with enrolment counts<br>**403 Forbidden** - User is not an Organiser<br>**401 Unauthorized** - Not logged in |

---

## 4. Category Endpoints

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|-------------|-------|-------------|---------------|--------------|-------------------|
| GET | `/categories` | Get all available categories. These can be age-based or distance-based. | Any (Logged in) | None | **200 OK** - Returns list of Category objects<br>**401 Unauthorized** - Not logged in |
| GET | `/categories/{id}` | Get a specific category by ID including which events use it. | Any (Logged in) | None | **200 OK** - Returns Category object with related events<br>**404 Not Found** - Category doesn't exist<br>**401 Unauthorized** - Not logged in |
| POST | `/categories` | Create a new category. Categories can be age-based (min/max age) or distance-based. | Organiser | `{ "name": "string", "description": "string", "ageMin": "int (optional)", "ageMax": "int (optional)", "distance": "decimal (optional)" }` | **201 Created** - Returns new Category object<br>**400 Bad Request** - Validation errors<br>**403 Forbidden** - User is not an Organiser<br>**401 Unauthorized** - Not logged in |
| PUT | `/categories/{id}` | Update an existing category. | Organiser | `{ "name": "string", "description": "string", "ageMin": "int (optional)", "ageMax": "int (optional)", "distance": "decimal (optional)" }` | **200 OK** - Returns updated Category object<br>**400 Bad Request** - Validation errors<br>**403 Forbidden** - User is not an Organiser<br>**404 Not Found** - Category doesn't exist<br>**401 Unauthorized** - Not logged in |
| DELETE | `/categories/{id}` | Delete a category. Will remove from all events using this category. | Organiser | None | **204 No Content** - Category deleted successfully<br>**403 Forbidden** - User is not an Organiser<br>**404 Not Found** - Category doesn't exist<br>**401 Unauthorized** - Not logged in |

---

## 5. Event Enrolment Endpoints

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|-------------|-------|-------------|---------------|--------------|-------------------|
| GET | `/events/{eventId}/enrolments` | Get all enrolments for a specific event. Includes participant details. | Organiser (for their events) | None | **200 OK** - Returns list of Enrolment objects with participant details<br>**403 Forbidden** - User is not the event organiser<br>**404 Not Found** - Event doesn't exist<br>**401 Unauthorized** - Not logged in |
| POST | `/events/{eventId}/enrol` | Enrol a participant in an event. Select which category they want to compete in. | Participant | `{ "categoryId": "int", "notes": "string (optional)" }` | **201 Created** - Returns new Enrolment object<br>**400 Bad Request** - Already enrolled, event full, or invalid category<br>**403 Forbidden** - User is not a Participant<br>**404 Not Found** - Event or Category doesn't exist<br>**401 Unauthorized** - Not logged in |
| PUT | `/enrolments/{enrolmentId}` | Update enrolment status. Participants can cancel their own enrolments. | Participant (for their own) | `{ "status": "Pending|Confirmed|Cancelled" }` | **200 OK** - Returns updated Enrolment object<br>**403 Forbidden** - User is not the enrolment owner<br>**404 Not Found** - Enrolment doesn't exist<br>**401 Unauthorized** - Not logged in |
| GET | `/participants/enrolments` | Get all enrolments for the current participant. Shows event and category details. | Participant | None | **200 OK** - Returns list of Enrolment objects with event and category details<br>**403 Forbidden** - User is not a Participant<br>**401 Unauthorized** - Not logged in |

---

## 6. Results Endpoints

| HTTP Method | Route | Description | Role Required | Request Body | Expected Response |
|-------------|-------|-------------|---------------|--------------|-------------------|
| GET | `/events/{eventId}/results` | Get all results for a specific event. Results include participant rankings. | Any (Logged in) | None | **200 OK** - Returns list of Result objects with participant details and rankings<br>**404 Not Found** - Event doesn't exist<br>**401 Unauthorized** - Not logged in |
| POST | `/events/{eventId}/results` | Capture results for a participant. Only the event organiser can add results. | Organiser (for their events) | `{ "enrolmentId": "int", "finishTime": "time", "finishPosition": "int", "status": "DNS|DNF|Finished" }` | **201 Created** - Returns new Result object<br>**400 Bad Request** - Validation errors<br>**403 Forbidden** - User is not the event organiser<br>**404 Not Found** - Event or Enrolment doesn't exist<br>**401 Unauthorized** - Not logged in |
| PUT | `/results/{resultId}` | Update an existing result. Only the event organiser can update results. | Organiser (for their events) | `{ "finishTime": "time", "finishPosition": "int", "status": "DNS|DNF|Finished" }` | **200 OK** - Returns updated Result object<br>**403 Forbidden** - User is not the event organiser<br>**404 Not Found** - Result doesn't exist<br>**401 Unauthorized** - Not logged in |
| DELETE | `/results/{resultId}` | Delete a result. Only the event organiser can delete results. | Organiser (for their events) | None | **204 No Content** - Result deleted successfully<br>**403 Forbidden** - User is not the event organiser<br>**404 Not Found** - Result doesn't exist<br>**401 Unauthorized** - Not logged in |
| GET | `/participants/results` | Get all results for the current participant. Shows personal race history. | Participant | None | **200 OK** - Returns list of Result objects with event details<br>**403 Forbidden** - User is not a Participant<br>**401 Unauthorized** - Not logged in |

---

## API Design Decisions

### Why I Chose Session-Based Authentication

I decided to use session-based authentication with cookies instead of JWT tokens. This was because the MVC application we're building in Part 3 needs to display different interfaces for organisers and participants. With session-based auth, the server can easily track who is logged in and what role they have. When a user logs in, the server creates a session and stores their user ID and role. Every subsequent request includes the session cookie, so the server knows exactly who is making the request.

### RESTful Principles I Followed

- **Resource-Based URLs**: Each major entity (events, categories, enrolments, results) has its own endpoint structure. I used plural nouns for collection names, like `/events` for all events and `/events/{id}` for a specific one.

- **HTTP Methods**: 
  - GET = Read data
  - POST = Create new data
  - PUT = Update existing data
  - DELETE = Remove data

- **Status Codes**: I made sure to use the right status codes for each situation:
  - 200 OK - Everything worked
  - 201 Created - Successfully created something new
  - 204 No Content - Successfully deleted something
  - 400 Bad Request - The request was invalid
  - 401 Unauthorized - Not logged in
  - 403 Forbidden - Logged in but not allowed
  - 404 Not Found - Resource doesn't exist
  - 409 Conflict - Something already exists (like a duplicate email)

### How I Handled Role-Based Access

- **Organiser endpoints** require the user to be logged in AND have the Organiser role. For endpoints that modify specific events, I also check that the organiser owns that event.

- **Participant endpoints** require the user to be logged in AND have the Participant role. For endpoints that show enrolments or results, I only show the participant's own data.

- **Public endpoints** like register and login are accessible to everyone.

### Special Considerations in My Design

**Event Categories (Many-to-Many Relationship):** 
I used a junction table called EventCategories to link events and categories. This way, one event can have many categories (like Senior, Master, 10km), and one category can be used by many events.

**Banner Images:** 
I planned to handle image uploads by sending the image as a base64 string. In Part 3, these images will be stored in Azure Blob Storage.

**Enrolment Status:** 
I included three statuses - Pending, Confirmed, and Cancelled. This allows organisers to confirm participant entries after payment verification.

**Result Status:** 
I included DNS (Did Not Start), DNF (Did Not Finish), and Finished. This is useful because some participants register but don't start, or start but don't finish.

---

## Endpoint Summary

| Resource | GET | POST | PUT | DELETE |
|----------|-----|------|-----|--------|
| Authentication | `/auth/me` | `/auth/register`, `/auth/login`, `/auth/logout` | - | - |
| Users | `/users/profile` | - | `/users/profile`, `/users/password` | - |
| Events | `/events`, `/events/{id}`, `/events/organiser` | `/events` | `/events/{id}` | `/events/{id}` |
| Categories | `/categories`, `/categories/{id}` | `/categories` | `/categories/{id}` | `/categories/{id}` |
| Enrolments | `/events/{eventId}/enrolments`, `/participants/enrolments` | `/events/{eventId}/enrol` | `/enrolments/{enrolmentId}` | - |
| Results | `/events/{eventId}/results`, `/participants/results` | `/events/{eventId}/results` | `/results/{resultId}` | `/results/{resultId}` |

---

## Example Request/Response Pairs

### Registering a New User

**Request:**
