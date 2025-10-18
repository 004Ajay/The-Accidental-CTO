## Before Dukaan (pg 18)
```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%
graph LR

A[user 1]
B[user 2]
C[user 3]
D[user n] 
W["WhatsApp <br> (Group / Chat)"]

A -- message --> W
B -- message --> W
C -- message --> W
D -- message --> W

W -- reply --> A
W -- reply --> B
W -- reply --> C
W -- reply --> D
```

### A customer interacting with a busy WhatsApp-Shop Owner

```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%

sequenceDiagram
    
    Customer-)Shop Owner: initiate chat
    Shop Owner-->>Customer: Sends items list
    Customer-)Shop Owner: Sends required items list
    Shop Owner-->>Customer: total bill amount
    Shop Owner-->>Customer: errors (eg: item out of stock)
    Customer-)Shop Owner: replace / cancel item
    Shop Owner-->>Customer: new bill
    Customer-)Shop Owner: payment screenshot
    Shop Owner-->>Customer: order status

```

---

## Single Box / Monolith (pg 34)

```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%
graph LR
  
subgraph Outside World
direction LR
  A[Users] 
end

subgraph Single Server
direction LR
  A -- https request --> N(Nginx)
  N -- pass request --> G(Gunicorn)
  G -- worker --> D(Django Monolith App)
  G -- worker --> D(Django Monolith App)
  D -- Read/Write --> P[(PostgreSQL)]
end 
```

---

## Separating App & DB (pg 43)
```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%
graph LR
  
subgraph Outside World
direction LR
  A[User] 
end

subgraph App Server
direction LR
  A -- https request --> N(Nginx)
  N -- pass request --> G(Gunicorn)
  N -- pass request --> G(Gunicorn)
  G -- worker --> D(Django Monolith App)
  G -- worker --> D(Django Monolith App)
end

subgraph Database Server
direction LR
  D -- Read/Write<br>(http request) --> P[(PostgreSQL)]
  D -- Read/Write<br>(http request) --> P[(PostgreSQL)]
end
```

---

## The Traffic Cop - Nginx as a Load Balancer (pg 74)

```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%

graph LR
  %% Nodes
  A[Users]
  N["Nginx <br> (Algorithm: <br> Round Robin, <br> Least Connections)"]
  D1(Django Monolith App)
  D2(Django Monolith App)
  Dn(Django Monolith App)
  P[(PostgreSQL)]

subgraph Outside World
direction LR
  A 
end

subgraph Load Balancer
direction LR
  A -- https request 1 --> N
  A -- https request 2 --> N
  A -- https request n --> N
end

subgraph App Server 1
direction LR
  N -- routes to --> G1(Gunicorn)
  G1 -- worker --> D1
  
end

subgraph App Server 2
direction LR
  N -- routes to --> G2(Gunicorn)
  G2 -- worker --> D2
end

subgraph App Server n
direction LR
  N -- routes to --> Gn(Gunicorn)
  Gn -- worker --> Dn
end

subgraph Database Server
direction LR
  D1 -- Read/Write<br>(http request) --> P
  D2 -- Read/Write<br>(http request) --> P
  Dn -- Read/Write<br>(http request) --> P
end

%% Highlight new components
classDef newEntry stroke:#2749F5,stroke-width:3px;
class G1,D1,G2,D2,Gn,Dn,N newEntry;
```

---

## Splitting the DB (pg 86)

```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%

graph LR
  %% Nodes
  A[Users]
  N["Nginx <br> (Algorithm: <br> Round Robin, <br> Least Connections)"]
  D1(Django Monolith App)
  D2(Django Monolith App)
  Dn(Django Monolith App)
  RR("Replica Router <br> (Custom)")
  PWR[("Postgre Write Replica <br> (Master)")]
  PRR[("Postgre Read Replica <br> (Slave)")]

subgraph Outside World
direction LR
  A 
end

subgraph Load Balancer
direction LR
  A -- https request 1 --> N
  A -- https request 2 --> N
  A -- https request n --> N
end

subgraph App Server 1
direction LR
  N -- routes to --> G1(Gunicorn)
  G1 -- worker --> D1
  
end

subgraph App Server 2
direction LR
  N -- routes to --> G2(Gunicorn)
  G2 -- worker --> D2
end

subgraph App Server n
direction LR
  N -- routes to --> Gn(Gunicorn)
  Gn -- worker --> Dn
end

D1 -- Read request --> RR
D2 -- Read request --> RR
Dn -- Write request --> RR

RR -- Read request --> PRR
RR -- Read request --> PRR
RR -- Write request --> PWR

subgraph Database Server
direction LR
  PWR -- Data Syncing --> PRR
  PWR -- Data Syncing --> PRR
end

%% Highlight new components
classDef newEntry stroke:#2749F5,stroke-width:3px;
class RR,PWR,PRR newEntry;
```

---

## Dev to Prod Workflow (pg 101)

```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%

graph LR

subgraph Developer
direction LR
  U("Writes Code <br> on Laptop") 
end

subgraph Code Repository
direction LR
  U -- "Code Push / <br> Pull Request" --> G(GitHub)
end

subgraph Production
direction LR
  G -- "Deployment <br> Script" --> PS("Production <br> Servers")
end
```

---

## Improved Dev to Prod Workflow (pg 115)

```mermaid
%%{init: {'flowchart': {'htmlLabels': false,}}}%%

graph LR

subgraph Developer
direction LR
  U("Writes Code <br> on Laptop") 
end

subgraph Code Repository
direction LR
  U -- "Code Push / <br> Pull Request" --> G(GitHub)
end

G -- "Code Review <br> (if pass)" --> AT(Automated Tests)
G -- "Code Review <br> (if fails)" --> U

AT -- "Deploy to <br> Staging" --> M("Manual QA Tests <br> Dedicated Human Tests")

M -- "CodePush" --> PS("Production <br> Servers")

subgraph Production
direction LR
  PS
end

%% Highlight new components
classDef newEntry stroke:#2749F5,stroke-width:3px;
class AT,M newEntry;
```

<br>

They went from a group of coders to a Professional Engineering team

---

## Adding Cache Layer - Redis (pg 128)

```mermaid
flowchart LR

    %% External User
    subgraph "Outside World"
        A[Users]
    end

    N["Nginx<br>Load Balancer"]

    %% External Requests
    A -- https request 1 --> N
    A -- https request 2 --> N
    A -- https request n --> N

    %% App Servers
    subgraph "App Servers (AS)"
        direction LR
        AS1[AS1: Gunicorn → Django App]
        AS2[AS2: Gunicorn → Django App]
        AS3[AS3: Gunicorn → Django App]
        AS4[AS4: Gunicorn → Django App]
        ASN[ASn: Gunicorn → Django App]

        %% Force vertical alignment using invisible links (~~~)
        AS1 ~~~ AS2 ~~~ AS3 ~~~ AS4 ~~~ ASN
    end

    %% Route traffic to each server
    N --> AS1
    N --> AS2
    N --> AS3
    N --> AS4
    N --> ASN

    %% Redis Cache
    RC["Redis Cache"]

    %% Django <--> Redis interactions
    AS1 -- Read request --> RC
    RC -- "Cache Hit <br> (Read request)" --> AS1

    AS2 -- Read request --> RC
    RC -- "Cache Miss <br> (Write request)" --> RR

    AS3 -- Write request --> RC
    RC -- "Cache Hit <br> (Read request)" --> AS3

    AS4 -- Write request --> RC
    RC -- "Cache Miss <br> (Write request)" --> RR

    ASN -- request --> RC
    RC -- "Cache Hit <br> (nth request)" --> ASN
    RC -- "Cache Miss <br> (nth request)" --> RR

    %% Replica Router
    RR["Replica Router<br>(Custom)"]

    %% Database Cluster
    subgraph DB["Database Server"]
        direction TB
        PWR[("Postgre Write Replica<br>(Master)")]
        PRR[("Postgre Read Replica<br>(Slave)")]
    end

    RR -- Read request --> PRR
    RR -- Write request --> PWR
    PWR -- Data Syncing --> PRR

%% Highlight new components
classDef newEntry stroke:#2749F5,stroke-width:3px;
class RC newEntry;
```

---

## Monolith to Microservices (pg 149)

```mermaid
flowchart LR

    %% External User
    subgraph "Outside World"
        A[Users]
    end

    N["Nginx<br>Load Balancer"]

    %% External Requests
    A -- https request 1 --> N
    A -- https request 2 --> N
    A -- https request n --> N

    %% App Servers
    subgraph "Multiple App Servers (AS)"
        direction LR

        subgraph App Server 1
        direction LR
        G1(Gunicorn) -- worker --> SFM("Storefront Microservice <br> (Django)")
        end

        subgraph App Server 2
        direction LR
        G2(Gunicorn) -- worker --> DM("Django Monolith")
        end

    end

    %% Route traffic to each server
    N -- "routes <br> (/storefront-service/*)"  --> G1
    N -- "routes <br> (/other-service/*)" --> G2

    %% Redis Cache
    RC["Redis Cache"]

    %% Django <--> Redis interactions
    SFM -- Read request --> RC
    RC -- "Cache Hit <br> (Read request)" --> SFM

    DM -- Write request --> RC
    RC -- "Cache Miss <br> (Write request)" --> RR

    %% Replica Router
    RR["Replica Router<br>(Custom)"]

    %% Database Cluster
    subgraph DB["Database Server"]
        direction TB
        PWR[("Postgre Write Replica<br>(Master)")]
        PRR[("Postgre Read Replica<br>(Slave)")]
    end

    RR -- Read request --> PRR
    RR -- Write request --> PWR
    PWR -- Data Syncing --> PRR

%% Highlight new components
classDef newEntry stroke:#2749F5,stroke-width:3px;
class SFM newEntry;    
```

---


## Apache Kafka & Change Data Capture (pg 162)

```mermaid
flowchart LR

    %% External User
    subgraph "Outside World"
        A[Users]
    end

    N["Nginx<br>Load Balancer"]

    %% External Requests
    A -- https request 1 --> N
    A -- https request 2 --> N
    A -- https request n --> N

    %% App Servers
    subgraph "Multiple App Servers (AS)"
        direction LR

        subgraph "App Server 1"
        direction LR
        G1(Gunicorn) -- worker --> SFM["Storefront Microservice <br> (Django)"]
        end

        subgraph "App Server 2"
        direction LR
        G2(Gunicorn) -- worker --> DM["Django Monolith"]
        end

    end

    %% Route traffic to each server
    N -- "routes <br> (/storefront-service/*)"  --> G1
    N -- "routes <br> (/other-service/*)" --> G2

    %% Redis Cache
    RC["Redis Cache"]

    %% Django <--> Redis interactions
    SFM -- Read request --> RC
    RC -- "Cache Hit <br> (Read request)" --> SFM

    DM -- Write request --> RC
    RC -- "Cache Miss <br> (Write request)" --> RR

    %% Replica Router
    RR["Replica Router<br>(Custom)"]

    %% Database Cluster
    subgraph DB["Database Server"]
        direction TB
        PWR[("Postgre Write Replica <br> Write-Ahead Log (Master)")]
        PRR[("Postgre Read Replica <br> (Slave)")]
    end

    RR -- Read request --> PRR
    RR -- Write request --> PWR
    PWR -- Data Syncing --> PRR

    %% New Entries (Debezium and Kafka)
    PWR <-- Watching --> DEB["Debezium"]
    DEB -- "Writes changes <br> in Kafka Topic" --> K["Kafka"]
    K -- "Sink Connector <br> (Renews Cache)" --> RC

%% Highlight new components
classDef newEntry stroke:#2749F5,stroke-width:3px;
class DEB,K newEntry;
```

---

## Containerization using Docker (pg 177)

Continuation of "Apache Kafka & Change Data Capture" only, but which all services were dockerized?

---