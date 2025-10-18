## Dukaan Starting Tech Stack

<div style="display: grid; grid-template-columns: repeat(4, 80px); gap: 10px;">
  <img src="../Icons/Python.svg" width="80" height="80">
  <img src="../Icons/Django.svg" width="80" height="80">
  <img src="../Icons/PostgreSQL.svg" width="80" height="80">
  <img src="../Icons/NGINX.svg" width="80" height="80">
  <img src="../Icons/Gunicorn.svg" style="width: 100px; height: 100px; object-fit: contain;" />
  <img src="../Icons/Digital Ocean.svg" width="80" height="80">
  <img src="../Icons/Ubuntu.svg" width="80" height="80">
</div>

---

## Single Box / Monolith (pg 34)
```mermaid
%%{init: {'flowchart': {'htmlLabels': true}}}%%
graph LR
  %% Nodes
  A["<img src='../Icons/users.svg'><br/>Users"]
  N["<img src='../Icons/NGINX.svg'<br/>Nginx"]
  G(["<img src='../Icons/Gunicorn.svg' width='30'/><br/>Gunicorn"])
  D["<img src='../Icons/Django.svg'><br/>Django App"]
  P["<img src='../Icons/PostgreSQL.svg'><br/>PostgreSQL"]
  
subgraph Outside World
  direction LR
  A
end

subgraph Single Server
  direction LR
  A -- https request --> N
  N -- pass request --> G
  G -- worker --> D
  G -- worker --> D
  D -- Read/Write --> P
end
```

---
