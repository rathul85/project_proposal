## Project Timeline

````mermaid
gantt
    title Project work plan (12 weeks)
    dateFormat  YYYY-MM-DD
    axisFormat  %d %b
    section Planning
    Requirement analysis        :a1, 2026-11-02, 14d
    System design               :a2, 2026-11-09, 21d
    section Development
    Database and web layer      :b1, 2026-11-23, 21d
    Face recognition module     :b2, 2026-11-30, 28d
    Review and reports          :b3, 2026-12-14, 21d
    section Finishing
    Testing and tuning          :c1, 2026-12-28, 21d
    Documentation               :c2, 2026-11-16, 70d
    Final submission            :milestone, c3, 2027-01-18, 1d
````

## System Architecture

````mermaid
flowchart LR
    U["Teacher / Administrator<br/>(Browser)"] <--> F["Flask web server<br/>Login, roles, workflow"]
    F <--> FS["Face service<br/>InsightFace: detect, embed, match"]
    F <--> DB[("SQLite database")]
    FS <--> PH[("Photo folder")]
    F --> R["Reports<br/>Session, month sheet, CSV"]
````

## Attendance Flowchart

````mermaid
flowchart TD
    A([Start]) --> B[Teacher logs in]
    B --> C[Select course and date]
    C --> D[/Camera frame or class photo/]
    D --> E[Detect faces and create embeddings]
    E --> F[Compare with enrolled students<br/>cosine similarity]
    F --> G{Score above threshold?}
    G -- Yes --> H[Mark present]
    G -- No --> I[Add to unknown list]
    H --> J[Teacher reviews and corrects]
    I --> J
    J --> K[Finalize class<br/>unmarked students become absent]
    K --> L([Report generated])
````

## Sequence Diagram

````mermaid
sequenceDiagram
    actor T as Teacher
    participant W as Web server (Flask)
    participant F as Face service (InsightFace)
    participant D as Database
    T->>W: Upload class photo
    W->>F: Detect and embed faces
    F->>D: Load course embeddings
    D-->>F: Enrolled embeddings
    F-->>W: Matches and unknown faces
    W->>D: Save marks and unknown list
    W-->>T: Show result for review
    T->>W: Correct marks, finalize
    W->>D: Mark remaining students absent
````

## Entity-Relationship Diagram

````mermaid
erDiagram
    TEACHER ||--o{ COURSE : teaches
    COURSE ||--o{ CLASSSESSION : has
    COURSE ||--o{ ENROLLMENT : has
    STUDENT ||--o{ ENROLLMENT : joins
    STUDENT ||--o{ STUDENTFACE : has
    STUDENT ||--o{ ATTENDANCE : receives
    CLASSSESSION ||--o{ ATTENDANCE : records
    CLASSSESSION ||--o{ UNKNOWNFACE : contains

    TEACHER {
        int id PK
        string name
        string username
        string password_hash
        string role
    }
    STUDENT {
        int id PK
        string roll_no
        string name
        string department
    }
    STUDENTFACE {
        int id PK
        int student_id FK
        blob embedding
        string photo_path
    }
    COURSE {
        int id PK
        string code
        string title
        int teacher_id FK
    }
    ENROLLMENT {
        int id PK
        int student_id FK
        int course_id FK
    }
    CLASSSESSION {
        int id PK
        int course_id FK
        date date
        bool finalized
    }
    ATTENDANCE {
        int id PK
        int session_id FK
        int student_id FK
        string status
    }
    UNKNOWNFACE {
        int id PK
        int session_id FK
        string photo_path
        bool resolved
    }
````

## Class Session Life Cycle

````mermaid
stateDiagram-v2
    [*] --> Created
    Created --> FacesDetected: upload photo
    FacesDetected --> UnderReview: auto-mark
    UnderReview --> UnderReview: correct marks
    UnderReview --> Finalized: finalize
    Finalized --> UnderReview: re-open
````

## Use Case Diagram

````mermaid
flowchart LR
    T([Teacher])
    A([Administrator])
    subgraph S["Smart Attendance System"]
        U1(Log in)
        U2(Take attendance from photo)
        U3(Review unknown faces)
        U4(Finalize or re-open class)
        U5(View reports and export CSV)
        U6(Delete students and courses)
    end
    T --> U1 & U2 & U3 & U4 & U5
    A --> U1 & U2 & U5 & U6
````

## Role Permissions

| Action | Teacher | Administrator |
|---|:---:|:---:|
| Log in and change own password | Yes | Yes |
| Add / edit students and courses | Yes | Yes |
| Take and review attendance | Yes | Yes |
| Finalize or re-open a class | Yes | Yes |
| View reports and export CSV | Yes | Yes |
| Manage teacher accounts | No | Yes |
| Delete students and courses | No | Yes |

## Technologies Used

| Part | Technology |
|---|---|
| Back end | Python, Flask |
| Face detection and embedding | InsightFace |
| Image handling | OpenCV, NumPy |
| Database | SQLite |
| Front end | HTML, CSS, JavaScript |
| Reports | CSV export, printable month sheet |
````
````

