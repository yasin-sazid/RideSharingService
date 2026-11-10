# Ride Sharing Service Simulation

A Node.js application simulating rider–driver matching with HTTP requests and real-time match notifications. The project demonstrates how an Express API, Socket.IO communication, in-memory request queues, and a MongoDB-backed rating model can work together.

## Matching workflow

```text
Rider requests ──┐
                 ├→ In-memory queues → Nearest-driver matching → Socket.IO matches
Driver requests ─┘                                  |
                                           Distance-based cost
```

The matcher compares starting coordinates using Euclidean distance, selects the nearest available driver for each rider, and calculates a cost of **twice that distance**. Matching runs on a five-second interval associated with a connection to the `communication` namespace. Matched drivers are removed from the available collection, and the cycle publishes its results before clearing the request collections.

## Interfaces

| Interface | Purpose |
| --- | --- |
| `POST /request/rider` | Add a rider request to the queue |
| `POST /request/driver` | Add a driver request to the queue |
| `POST /rating` | Submit a rating through the rating route |
| Socket.IO namespace `/communication` | Receive the `matches` event |

Request fields are consumed directly from the submitted bodies. The matcher uses starting coordinates plus rider/driver names and the driver's car number. [client.js](client.js) demonstrates the application's client-side request and socket interactions.

## Run

Use Node.js and npm. The server uses the MongoDB connection configured in `index.js` for the rating model; provision the corresponding database access in the execution environment.

```bash
npm ci
npm start
```

The default listener is **port 5000**, configurable through `PORT`. Use `node client.js` to explore the supplied console client and `npm run dev` for the nodemon development workflow.

## Technical organization

`index.js` coordinates HTTP, sockets, matching, and persistence initialization. `Riders.js`, `Drivers.js`, and `Matches.js` hold the shared collections; `routes/api/` contains request and rating handlers; `models/Rating.js` defines the Mongoose model.
