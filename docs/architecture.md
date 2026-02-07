## Product Choice

Matrix
https://matrix.org/
The Matrix protocol is an open-standard, decentralized communication protocol designed for secure, real-time messaging, VoIP, and IoT, utilizing HTTP APIs to synchronize JSON "events" across a federated network of homeservers. It employs a directed acyclic graph (DAG) to manage data, allowing for resilient, interoperable, and end-to-end encrypted (E2EE) conversations without a central authority.

## Main components

![Matrix Component Diagram](../../../docs/diagrams/out/matrix/component-diagram/matrix_component_diagram.svg)
![Matrix Component PlantUML](../../../docs/diagrams/src/matrix/matrix_component_diagram.puml)

1. Matrix Client (Mobile / Desktop / Web)
The client application that users interact with to send messages, upload media, and synchronize room state. It communicates only with its own homeserver via the Client-Server API.

2. Homeserver
The central server in the Matrix architecture that stores user accounts, rooms, and events for a given domain. It handles client authentication, data persistence, and federation with other homeservers.

3. Client-Server API
An HTTP/JSON API used by clients to perform all user-facing operations such as login, message sending, room synchronization, and media upload/download. This API is strictly used between clients and their homeserver.

4. Federation (Server-Server API)
The mechanism that allows homeservers from different domains to exchange room events and state. Federation enables Matrix to be decentralized by replicating room data across participating servers.

5. Event & State Database
A database that stores room events (messages) and room state (members, permissions, settings). It is used to reconstruct room history, synchronize clients, and maintain consistency across servers.

6. Media Repository
A component responsible for storing and serving media files such as images, videos, and audio via MXC URIs. It also caches media fetched from remote homeservers to reduce repeated downloads.

7. Application Service (Bridge / Bot)
A privileged service that connects to the homeserver and can act on behalf of virtual users. It is typically used to implement bridges to other platforms (e.g., Telegram, Discord) and automation bots.

8. Identity Server (Optional)
A service that maps Matrix user IDs to third-party identifiers such as email addresses or phone numbers. It is optional but helps users discover contacts more easily.

9. Push Gateway
A component that delivers push notifications to external push providers when clients are offline. The homeserver uses this gateway instead of communicating directly with push services.

## Data flow

![Matrix Sequence Diagram](../../../docs/diagrams/out/matrix/sequence-diagram/matrix_sequence_diagram.svg)
![Matrix Sequence PlantUML](../../../docs/diagrams/src/matrix/matrix_sequence_diagram.puml)

Example group: Media Upload

In this group of steps, the user uploads a media file before sending a message.
The client sends the file data in chunks to the user’s homeserver using the media upload API.
The homeserver stores the uploaded data in the Media Repository and generates an MXC URI that uniquely identifies the file.
This MXC URI is then returned to the client and will later be referenced in the message event instead of embedding the media directly.

The components involved are the Client, the Homeserver, and the Media Repository.
The exchanged data includes raw media file chunks during upload and the resulting MXC URI returned to the client.

## Deployment

![Matrix Deployment Diagram](../../../docs/diagrams/out/matrix/deployment-diagram/matrix_deployment_diagram.svg)
![Matrix Deployment PlantUML](../../../docs/diagrams/src/matrix/matrix_deployment_diagram.puml)

In the deployment diagram, Matrix clients are deployed on end-user devices such as mobile phones, desktop computers, and web browsers.
Each user connects to a dedicated homeserver that is deployed on cloud or on-premise infrastructure and hosts the core services, databases, and media repository.
Homeservers are deployed independently for different organizations or domains and communicate with each other over the Internet using the federation protocol.
Application Services (such as bridges or bots) are deployed as separate server-side components that connect to a homeserver via a dedicated API.
Push Gateways and external push providers are deployed outside the Matrix infrastructure and are used to deliver notifications to offline clients.

## Assumptions

1. I assume that each homeserver stores the full event history and room state for the users it serves, even when rooms are federated across multiple servers.
2. I assume that the Media Repository implements caching of remote media files to reduce repeated downloads from other homeservers and to improve performance.
3. I assume that federation between homeservers is asynchronous and event-based, meaning that message delivery between servers is not guaranteed to be instantaneous.
4. I assume that push notifications are optional and are only used when a client is offline, while online clients receive updates through synchronization endpoints.

## Open questions

1. How exactly do homeservers resolve conflicts and inconsistencies in room state during federation, especially when multiple servers send concurrent state updates?
2. What internal optimizations are used by homeserver implementations (e.g., Synapse) to handle large rooms with thousands of members efficiently?
3. How are federation retries, backoff strategies, and failure recovery implemented in practice when a remote homeserver is temporarily unavailable?
4. What specific database indexing and sharding strategies are used in production homeservers to scale event storage and /sync performance?

