# Chat App

A real-time multi-client chat server built with Node.js and the `ws` package. Open the
page in two or more browser windows to see messages broadcast live between clients.

Built for the freeCodeCamp course *Build a Chat App* (Back-End Development and APIs
certification).

## Setup

```bash
npm install
npm start
```

The server listens on port `3001`. Open <http://localhost:3001>, type a username, and
click **Connect**. Each browser window is an independent client.

## How it works

**HTTP** — `http.createServer` serves the static files from `public/`, resolving the
requested path against the public directory and rejecting anything that escapes it.
`/` serves `public/index.html`.

**WebSockets** — a `WebSocketServer` is attached to the same HTTP server via
`{ server }`, so chat traffic rides the port the page was loaded from.

Three events drive the app:

| Event | Payload broadcast to every client |
| --- | --- |
| `connection` | `{ "type": "system", "text": "<username> joined" }` |
| `message` | `{ "type": "chat", "username": ..., "text": ... }` |
| `close` | `{ "type": "system", "text": "<username> left" }` |

The username is read from the connection URL query string
(`ws://localhost:3001?username=Alice`), falling back to `Anonymous`. Every broadcast goes
to all connected clients, including the sender, so the sender sees their own message
rendered in the thread.

`public/script.js` handles the browser side: connecting, rendering messages by type, and
escaping usernames and message text before inserting them into the DOM.

## Notes

- Message text is broadcast as received from the client, and the browser client escapes it
  before rendering. A server that served other consumers should validate and cap message
  length on input.
- Malformed JSON on a socket is ignored rather than crashing the server.