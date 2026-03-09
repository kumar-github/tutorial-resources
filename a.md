Base

```mermaid
%%{init: {'theme': 'base'}}%%
sequenceDiagram
    participant C as Client
    box Filter Chain
        participant F1 as LoggingFilter (Pre)
        participant F2 as HeaderCheckFilter (Pre)
        participant F3 as CompressionFilter (Post)
        participant F4 as EncryptionFilter (Post)
        participant F5 as HeaderAddFilter (Post)
    end
    participant T as Target (Servlet)
%% Request path
    C ->> F1: HTTP Request
    activate F1
    Note over F1: Pre-process Request
    F1 ->> F2: Forward Request
    activate F2
    Note over F2: Pre-process Request
    F2 ->> F3: Forward Request
    activate F3
    F3 ->> F4: Forward Request
    activate F4
    F4 ->> F5: Forward Request
    activate F5
    F5 ->> T: Invoke Target
    activate T
    Note over T: Request Received
%% Response path
    T -->> F5: HTTP Response
    Note over T: Response Sent
    deactivate T
    Note over F5: Post-process Response
    F5 -->> F4: Return Response
    deactivate F5
    Note over F4: Post-process Response
    F4 -->> F3: Return Response
    deactivate F4
    Note over F3: Post-process Response
    F3 -->> F2: Return Response
    deactivate F3
    F2 -->> F1: Return Response
    deactivate F2
    F1 -->> C: Final HTTP Response
    deactivate F1
    Note over C: Response Received
```

Default

```mermaid
%%{init: {'theme': 'default'}}%%
sequenceDiagram
    participant C as Client
    box Filter Chain
        participant F1 as LoggingFilter (Pre)
        participant F2 as HeaderCheckFilter (Pre)
        participant F3 as CompressionFilter (Post)
        participant F4 as EncryptionFilter (Post)
        participant F5 as HeaderAddFilter (Post)
    end
    participant T as Target (Servlet)
%% Request path
    C ->> F1: HTTP Request
    activate F1
    Note over F1: Pre-process Request
    F1 ->> F2: Forward Request
    activate F2
    Note over F2: Pre-process Request
    F2 ->> F3: Forward Request
    activate F3
    F3 ->> F4: Forward Request
    activate F4
    F4 ->> F5: Forward Request
    activate F5
    F5 ->> T: Invoke Target
    activate T
    Note over T: Request Received
%% Response path
    T -->> F5: HTTP Response
    Note over T: Response Sent
    deactivate T
    Note over F5: Post-process Response
    F5 -->> F4: Return Response
    deactivate F5
    Note over F4: Post-process Response
    F4 -->> F3: Return Response
    deactivate F4
    Note over F3: Post-process Response
    F3 -->> F2: Return Response
    deactivate F3
    F2 -->> F1: Return Response
    deactivate F2
    F1 -->> C: Final HTTP Response
    deactivate F1
    Note over C: Response Received
```

Dark

```mermaid
%%{init: {'theme': 'dark'}}%%
sequenceDiagram
    participant C as Client
    box Filter Chain
        participant F1 as LoggingFilter (Pre)
        participant F2 as HeaderCheckFilter (Pre)
        participant F3 as CompressionFilter (Post)
        participant F4 as EncryptionFilter (Post)
        participant F5 as HeaderAddFilter (Post)
    end
    participant T as Target (Servlet)
%% Request path
    C ->> F1: HTTP Request
    activate F1
    Note over F1: Pre-process Request
    F1 ->> F2: Forward Request
    activate F2
    Note over F2: Pre-process Request
    F2 ->> F3: Forward Request
    activate F3
    F3 ->> F4: Forward Request
    activate F4
    F4 ->> F5: Forward Request
    activate F5
    F5 ->> T: Invoke Target
    activate T
    Note over T: Request Received
%% Response path
    T -->> F5: HTTP Response
    Note over T: Response Sent
    deactivate T
    Note over F5: Post-process Response
    F5 -->> F4: Return Response
    deactivate F5
    Note over F4: Post-process Response
    F4 -->> F3: Return Response
    deactivate F4
    Note over F3: Post-process Response
    F3 -->> F2: Return Response
    deactivate F3
    F2 -->> F1: Return Response
    deactivate F2
    F1 -->> C: Final HTTP Response
    deactivate F1
    Note over C: Response Received
```

Neutral

```mermaid
%%{init: {'theme': 'neutral'}}%%
sequenceDiagram
    participant C as Client
    box Filter Chain
        participant F1 as LoggingFilter (Pre)
        participant F2 as HeaderCheckFilter (Pre)
        participant F3 as CompressionFilter (Post)
        participant F4 as EncryptionFilter (Post)
        participant F5 as HeaderAddFilter (Post)
    end
    participant T as Target (Servlet)
%% Request path
    C ->> F1: HTTP Request
    activate F1
    Note over F1: Pre-process Request
    F1 ->> F2: Forward Request
    activate F2
    Note over F2: Pre-process Request
    F2 ->> F3: Forward Request
    activate F3
    F3 ->> F4: Forward Request
    activate F4
    F4 ->> F5: Forward Request
    activate F5
    F5 ->> T: Invoke Target
    activate T
    Note over T: Request Received
%% Response path
    T -->> F5: HTTP Response
    Note over T: Response Sent
    deactivate T
    Note over F5: Post-process Response
    F5 -->> F4: Return Response
    deactivate F5
    Note over F4: Post-process Response
    F4 -->> F3: Return Response
    deactivate F4
    Note over F3: Post-process Response
    F3 -->> F2: Return Response
    deactivate F3
    F2 -->> F1: Return Response
    deactivate F2
    F1 -->> C: Final HTTP Response
    deactivate F1
    Note over C: Response Received
```


Forest

```mermaid
%%{init: {'theme': 'forest'}}%%
sequenceDiagram
    participant C as Client
    box rgb(220, 255, 220) Filter Chain
        participant F1 as LoggingFilter (Pre)
        participant F2 as HeaderCheckFilter (Pre)
        participant F3 as CompressionFilter (Post)
        participant F4 as EncryptionFilter (Post)
        participant F5 as HeaderAddFilter (Post)
    end
    participant T as Target (Servlet)
%% Request path
    C ->> F1: HTTP Request
    activate F1
    Note over F1: Pre-process Request
    F1 ->> F2: Forward Request
    activate F2
    Note over F2: Pre-process Request
    F2 ->> F3: Forward Request
    activate F3
    F3 ->> F4: Forward Request
    activate F4
    F4 ->> F5: Forward Request
    activate F5
    F5 ->> T: Invoke Target
    activate T
    Note over T: Request Received
%% Response path
    T -->> F5: HTTP Response
    Note over T: Response Sent
    deactivate T
    Note over F5: Post-process Response
    F5 -->> F4: Return Response
    deactivate F5
    Note over F4: Post-process Response
    F4 -->> F3: Return Response
    deactivate F4
    Note over F3: Post-process Response
    F3 -->> F2: Return Response
    deactivate F3
    F2 -->> F1: Return Response
    deactivate F2
    F1 -->> C: Final HTTP Response
    deactivate F1
    Note over C: Response Received
```
