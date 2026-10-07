```mermaid
erDiagram
    USER {
        uuid id PK
        string username
        string email
        datetime created_at
    }
    
    PROJECT {
        uuid id PK
        string name
        string description
        datetime start_date
    }
    
    TASK {
        uuid id PK
        uuid project_id FK
        uuid assignee_id FK
        string title
        string status
        datetime due_date
    }
    
    COMMENT {
        uuid id PK
        uuid task_id FK
        uuid author_id FK
        string text
        datetime created_at
    }

    %% Зв'язки
    USER }|--|{ PROJECT : "participates in"
    PROJECT ||--o{ TASK : "contains"
    USER ||--o{ TASK : "assigned to"
    TASK ||--o{ COMMENT : "has"
    USER ||--o{ COMMENT : "authors"