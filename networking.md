## How resources are loaded in the browser

### Scripts

`<script>` tags by default are blocking: the browser stops parsing HTML until the script is fetched and executed.

Attributes:

- async: script is fetched in parallel, executed as soon as it’s ready.

- defer: script is fetched in parallel, executed after HTML parsing finishes.

### Images

Images (`<img>`), fonts, and CSS are fetched asynchronously, meaning the browser continues parsing HTML while downloading them.

Images are not blocking the DOM, but large images can affect page paint.

### API calls (XHR / fetch)

Fetches are async by default, handled by the browser’s Web APIs.

The JS main thread is not blocked; when the response returns, the callback goes to the microtask or task queue, then the event loop executes it.

### Simultaneous Connections

Browsers limit concurrent connections per domain to avoid overloading servers.

Approximate limits (can vary by browser):

Browser Connections per domain

- Chrome / Edge 6–8
- Firefox 6
- Safari 6
- IE11 6

### fetch API

```js
async function sendData() {
  const url = "https://api.example.com/users";
  const userData = {
    name: "Jane",
    age: 25,
  };

  try {
    const response = await fetch(url, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify(userData),
    });

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    console.log("Server response:", data);
  } catch (error) {
    console.error("Request failed:", error);
  }
}

sendData();
```

### Web sockets

How it works:

- Client initiates a WebSocket handshake using ws:// or wss:// (secure).
- Server responds and upgrades the HTTP connection to a persistent WebSocket connection.
- Both client and server can send/receive messages freely until the connection is closed.

```js
// Create WebSocket connection
const socket = new WebSocket("wss://example.com/socket");

// Connection opened
socket.addEventListener("open", () => {
  console.log("Connected to server");
  socket.send("Hello server!");
});

// Listen for messages
socket.addEventListener("message", (event) => {
  console.log("Message from server:", event.data);
});

// Close connection
socket.addEventListener("close", () => {
  console.log("Disconnected");
});
```
