# Implementation Plan: Discord-Like Chat

## Overview

Build a lightweight real-time chat app using Node.js, Express, and Socket.IO with a single-page frontend. No database — all in-memory.

## Tasks

- [ ] 1. Initialize project structure and dependencies
  - Create `package.json` with `express` and `socket.io` dependencies
  - Create `server.js` entry point skeleton
  - Create `public/` directory for static frontend files
  - _Requirements: core architecture_

- [ ] 2. Implement the Express + Socket.IO server
  - [ ] 2.1 Set up Express to serve `public/index.html` as the static root
    - Configure `express.static` for the `public/` directory
    - Start HTTP server on a configurable port (default 3000)
    - _Requirements: backend serving_

  - [ ] 2.2 Implement Socket.IO connection handling
    - Handle `set username` event — store `socket.username`, fall back to `"Anonymous"` if not set
    - Handle `chat message` event — attach server timestamp and broadcast `{ username, text, timestamp }` to all clients via `io.emit`
    - Handle `disconnect` event — broadcast `user left` with the stored username
    - Broadcast `user joined` on new connection after username is set
    - _Requirements: real-time messaging, join/leave notifications_

  - [ ]* 2.3 Write unit tests for server event handlers
    - Test `"Anonymous"` fallback when no username is set before `chat message`
    - Test that `chat message` payload includes `username`, `text`, and a numeric `timestamp`
    - _Requirements: error handling, data model_

- [ ] 3. Checkpoint — Ensure server starts and Socket.IO connects
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 4. Build the frontend (public/index.html + public/client.js)
  - [ ] 4.1 Create `public/index.html` with the required DOM structure
    - Include `<ul id="messages">`, `<form id="message-form">`, `<input id="message-input">`, and Send button
    - Load Socket.IO client from CDN
    - Load `client.js`
    - _Requirements: frontend UI_

  - [ ] 4.2 Implement `public/client.js` — username prompt and socket setup
    - Prompt user for a username on page load (use `prompt()` or an inline modal)
    - Trim and enforce max 32-char username; emit `set username` to server
    - _Requirements: username handling, security_

  - [ ] 4.3 Implement message sending in `client.js`
    - On form submit, validate input is non-empty; if blank, return focus to input without emitting
    - Emit `chat message` with `{ text }` and clear the input
    - Render the sender's own message locally immediately (no round-trip)
    - Use `textContent` (not `innerHTML`) when rendering to prevent XSS
    - _Requirements: message sending, empty message handling, security_

  - [ ] 4.4 Implement incoming event listeners in `client.js`
    - Listen for `chat message` — append message to `#messages`, scroll to bottom
    - Listen for `user joined` and `user left` — append a styled notification entry
    - Visually distinguish the current user's messages from others
    - _Requirements: real-time receive, join/leave notifications_

  - [ ]* 4.5 Write unit tests for client-side validation logic
    - Test that empty/whitespace input does not emit `chat message`
    - Test that username is trimmed and capped at 32 chars
    - _Requirements: empty message handling, username handling_

- [ ] 5. Checkpoint — Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

- [ ] 6. Wire everything together and validate end-to-end
  - [ ] 6.1 Confirm server correctly serves `index.html` and client connects via WebSocket
    - Verify Socket.IO handshake completes on page load
    - _Requirements: full connection flow_

  - [ ]* 6.2 Write integration tests for the full message flow
    - Use two Socket.IO test clients: send a message from one, assert the other receives it with correct `username`, `text`, and `timestamp`
    - Assert `user joined` / `user left` events fire on connect/disconnect
    - _Requirements: broadcast, join/leave notifications_

- [ ] 7. Final checkpoint — Ensure all tests pass
  - Ensure all tests pass, ask the user if questions arise.

## Notes

- Tasks marked with `*` are optional and can be skipped for a faster MVP
- Use `textContent` everywhere messages are rendered to prevent XSS
- Socket.IO reconnection is handled automatically by the client library
