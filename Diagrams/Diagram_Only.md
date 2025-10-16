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
  A[User] 
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
  A[User]
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
```

---