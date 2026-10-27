# College Event Management System

A Spring Boot, Thymeleaf and in-memory Java web application for creating campus events, registering students, managing participants and tracking attendance. No database is used; all sample and runtime data is held in Java collections and resets when the application restarts.

## Project structure

```text
college-event-management/
├── pom.xml
├── README.md
└── src/
    └── main/
        ├── java/com/college/events/
        │   ├── EventManagementApplication.java
        │   ├── controller/
        │   │   ├── AttendanceController.java
        │   │   ├── DashboardController.java
        │   │   ├── EventController.java
        │   │   ├── ParticipantController.java
        │   │   └── RegistrationController.java
        │   ├── model/
        │   │   ├── AttendanceStatus.java
        │   │   ├── Event.java
        │   │   └── Student.java
        │   ├── repository/
        │   │   └── EventRepository.java
        │   └── service/
        │       └── EventService.java
        └── resources/
            ├── application.properties
            ├── static/
            │   ├── css/styles.css
            │   └── js/app.js
            └── templates/
                ├── attendance/{events.html,list.html}
                ├── events/{form.html,list.html}
                ├── fragments/{alerts.html,nav.html}
                ├── participants/{events.html,list.html}
                ├── registration/form.html
                └── dashboard.html
```

## Requirements

- JDK 17 or later
- Maven 3.6 or later (or the Maven wrapper supplied by your IDE)
- VS Code with the **Extension Pack for Java** and **Spring Boot Extension Pack** recommended

## Run in VS Code

1. Open this project folder in VS Code.
2. Install the Java and Spring Boot extension packs if they are not already installed.
3. Confirm Java 17+ is selected. In VS Code, run **Java: Configure Java Runtime** if needed.
4. Open the integrated terminal in the project root and run:

   ```powershell
   mvn spring-boot:run
   ```

   You can also open `EventManagementApplication.java` and select **Run** above `main`.
5. Browse to [http://localhost:8080](http://localhost:8080).
6. Stop the app with `Ctrl+C`. Restarting it restores the sample data.

Build and test from the project root:

```powershell
mvn test
mvn package
```

## Pages and main routes

| Screen | URL | Main actions |
|---|---|---|
| Home / dashboard | `/` | View event and participation summary; shortcuts |
| Event list | `/events` | View, edit and delete events |
| Create event | `/events/new` | Create an event |
| Edit event | `/events/{eventId}/edit` | Update event details |
| Student registration | `/register` | Register a student for an available event |
| Participant event chooser | `/participants` | Choose an event's roster |
| Participant list | `/participants/{eventId}` | Search by ID/name and remove registrations |
| Attendance event chooser | `/attendance` | Choose an event |
| Attendance sheet | `/attendance/{eventId}` | Mark students present or absent and view counts |

This is a classroom/demo application: there is no authentication or role enforcement. Treat the event and attendance routes as admin functions only in a trusted local environment.

## Sample data

The app starts with three events:

- `EVT-101` — Campus Tech Fest, Innovation Hall, 14 Nov 2026; 120 places
- `EVT-102` — Cultural Night, College Amphitheatre, 21 Nov 2026; 250 places
- `EVT-103` — Annual Sports Meet, Main Sports Ground, 5 Dec 2026; 180 places

There are three seeded registrations. Aarav Sharma and Mia Patel are registered for the Tech Fest; Aarav is marked present and Mia absent. Noah Iyer is registered for Cultural Night and starts absent. Sample student IDs are `STU-2024-001` through `STU-2024-003`.

## Java collections

The in-memory collection implementation is in `EventRepository` and `Event`.

- **ArrayList**: `EventRepository.events` holds events in display/insertion order. `Event.participants` holds the students registered for that event. These lists can be iterated for tables, counts, and participant searching.
- **HashSet**: `EventRepository.registrationKeys` stores a normalized `eventId::studentId` key for every registration. The set makes duplicate checks efficient and event-scoped: the same student may join multiple events but cannot register twice for one event. Removing a registration or event also removes its keys.
- **HashMap**: `Event.attendanceByStudentId` maps each registered student's ID to `PRESENT` or `ABSENT` for that event. A map on each event scopes attendance to that event while keeping Student ID as the key. New registrations default to `ABSENT`; marking attendance updates the map.

The repository seeds data at startup and is the only place that mutates these core collection-backed records. The dashboard's “Unique students” count de-duplicates IDs across events.

## Expected screens / output

The dashboard opens with four summary cards. With the initial sample set, it shows **3 events**, **3 unique students**, **3 participants**, and **1 present student**. The upcoming events area shows event cards with dates, venues, capacity bars, and participant links.

The event list provides edit, delete, participant and attendance actions. Event and registration forms show inline validation. Participant lists show student details and attendance status and filter by student ID/name. Attendance sheets have Present/Absent actions and per-event present/absent/total counts. Layouts adapt for mobile screens with a collapsible navigation menu.

No screenshots are checked in; launch the app and visit the URLs above to see each live screen.
