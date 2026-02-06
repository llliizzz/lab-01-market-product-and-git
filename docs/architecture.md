# Architecture overview

## Product Choice

- **Product:** Telegram  
- **Website:** <https://telegram.org/>  
- **Short description:** Telegram is a cloud-based messenger that allows users to exchange messages, media, and files in private chats, groups, and channels.

## Main components

![Telegram Component Diagram](./diagrams/out/telegram/Component%20Diagram.svg)

PlantUML source: [component-diagram.puml](./diagrams/src/telegram/component-diagram.puml)

Selected components and what they do:

1. **Client Apps (Mobile/Desktop/Web)** — Applications that users interact with to read and send messages, view media, and manage chats.  
2. **API Gateway** — Receives all client requests, checks basic validity, and routes them to the correct backend services.  
3. **Auth / Session Service** — Verifies user identity, manages logins, and keeps track of active user sessions.  
4. **Messaging Service** — Handles sending, storing, and delivering messages between users and groups.  
5. **Media Service** — Processes uploads and downloads of photos, videos, and documents and connects to storage/CDN.  

## Data flow

![Telegram Sequence Diagram](./diagrams/out/telegram/Sequence%20Diagram.svg)

PlantUML source: [sequence-diagram.puml](./diagrams/src/telegram/sequence-diagram.puml)

Chosen flow: **Send a message**

What happens in this flow:

- The **Client App** sends a message request to the **API Gateway** with chat ID, message text, and session data.  
- The **API Gateway** checks the session via **Auth Service** and forwards the request to **Messaging Service**.  
- The **Messaging Service** saves the message in the database and prepares it for delivery.  
- If the recipient is online, the message is delivered directly to their **Client App**.  
- If the recipient is offline, a lightweight notification is triggered.  

Data exchanged between components:

- Client → Gateway: message text, chat ID, session info  
- Gateway ↔ Auth: session validation  
- Gateway → Messaging: validated message request  
- Messaging ↔ Storage: message content and metadata  
- Messaging → Client: new message update  

## Deployment

![Telegram Deployment Diagram](./diagrams/out/telegram/Deployment%20Diagram.svg)

PlantUML source: [deployment-diagram.puml](./diagrams/src/telegram/deployment-diagram.puml)

Deployment description:

- **Client Apps** run on user devices (phones, computers, browsers).  
- **API Gateway and core services** run in Telegram’s cloud infrastructure across multiple data centers.  
- **Media storage and databases** are deployed in backend infrastructure and supported by CDN for faster delivery.

## Assumptions

- I assume the Messaging Service is divided into shards to handle large numbers of chats and users.  
- I assume media files are cached in a CDN to reduce load and speed up downloads.

## Open questions

- How does Telegram distribute traffic between data centers in different regions?  
- How exactly does the architecture differ for secret chats compared to regular chats?
