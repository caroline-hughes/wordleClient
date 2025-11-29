# Wordle Client over TCP/TLS

A fully autonomous Wordle-playing client implemented in Python.  
The client connects to a remote Wordle server using raw TCP or TLS, negotiates a custom JSON protocol, and solves the puzzle using constraint-based filtering.

## Running the Client

```bash
./wordle_client [-s] [-p PORT] <hostname> <username>
```

## Overview

This project implements a complete networked Wordle client with:

-   **INET streaming sockets** (TCP)
-   Optional **TLS encryption** via Python’s `ssl` module
-   A custom **JSON message protocol** (`hello` → `start` → `guess` → `retry` → `bye`)
-   A fully automatic **Wordle-solving algorithm**
-   Manual **send/receive loops** that handle message framing over an unstructured stream
-   A dynamic **wordlist pruning algorithm** based on exact/partial/no-match marks

It communicates with a remote server that sends marks for each guess, and the client reduces the search space until it finds the solution.

---

## Features

### **Socket-Level Networking**

-   Manual `send()` loop that ensures all bytes are transmitted
-   Streaming `recv()` until a newline terminator is received
-   Automatic buffer accumulation and message framing
-   Proper error paths for disconnects and partial reads

### **TLS Support**

-   Optional `-s` flag enables TLS
-   Uses `ssl.SSLContext().wrap_socket()`
-   Defaults to alternate TLS port when enabled

### **Wordle Solving Algorithm**

Implements constraint propagation based on server-provided marks:

-   **Green (2):** keep only words with matching letter in that index
-   **Yellow (1):** keep words containing the letter (but not at that index)
-   **Grey (0):** prune words with that letter at that index

The algorithm progressively trims the candidate list and always guesses the first remaining candidate.

### **Message Protocol**

All messages follow a newline-terminated JSON format:

-   `hello`
-   `start` (contains connection ID)
-   `guess`
-   `retry`
-   `bye` (contains final flag)
