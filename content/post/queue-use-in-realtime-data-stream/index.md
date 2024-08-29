---
title: "Understanding and Utilizing Queues in Python for Real-Time Data Streaming"
description: 
date: 2024-08-29T10:38:04Z
image: cover.jpg
hidden: false
comments: true
---

In the world of algorithmic trading and real-time data processing, handling a continuous stream of data efficiently is crucial. Python's `queue` module provides a simple yet powerful way to manage data flow between different threads, ensuring that your application remains responsive and performs well. In this post, we'll explore how to use `queue.Queue` in Python to manage real-time data streaming, particularly in the context of an algorithmic trading engine using FastAPI.

## Why Use Queues?
When dealing with real-time data, especially in applications like trading platforms, you often need to manage data that arrives asynchronously. Queues help in:

- **Thread-Safe Communication:** Queues allow safe exchange of information between threads, ensuring that data isn't corrupted or lost.
- **Buffering Data:** They act as a buffer, holding data until your application is ready to process it.
- **Decoupling Components:** Queues help separate data production (e.g., from a WebSocket) and data consumption (e.g., updating a chart), allowing each to run independently.

## Example: Implementing a Queue for Streaming Data
Let's consider a scenario where you're building an algo trading engine. You have a WebSocket that receives real-time data from a broker, and you need to update a front-end chart in real-time using FastAPI. Here’s how you can achieve this with a queue.

### Setting Up the Queue
First, import the necessary modules and initialize a global queue:

```python
import queue
import threading
import asyncio
from fastapi import FastAPI, WebSocket, WebSocketDisconnect

streamed_data_queue = queue.Queue()
```

### Handling Incoming Data
Next, we set up a function that processes incoming data and puts it into the queue:

```python
def batch_stream(data):
    data_json = {
        's': data['symbol'],
        't': data['time'],
        'ot': data['candles'][0],
        'o': data['candles'][1],
        'h': data['candles'][3],
        'l': data['candles'][4],
        'c': data['candles'][2]
    }
    streamed_data_queue.put(data_json)
```

### Streaming Data to the Front-End
With FastAPI, you can create a WebSocket endpoint that streams the data from the queue to the front-end:

```python
app = FastAPI()

@app.websocket('/ws')
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    while True:
        try:
            if not streamed_data_queue.empty():
                data = streamed_data_queue.get_nowait()
                await websocket.send_json(data)
            await asyncio.sleep(0.1)
        except WebSocketDisconnect:
            break
```

This WebSocket endpoint continuously checks the queue for new data and sends it to the front-end. The await asyncio.sleep(0.1) call helps prevent the loop from consuming too much CPU.

### Running the WebSocket
Finally, start your WebSocket client to connect and receive data:
```python
import websocket
import json

def on_message(ws, message):
    data = json.loads(message)
    batch_stream(data)

ws = websocket.WebSocketApp("wss://broker-websocket-url",
                            on_message=on_message)

ws.run_forever()
```

## Conclusion
Using queues in Python for real-time data streaming is a robust solution for managing data flow between threads. In the context of an algo trading engine, queues ensure that your application remains responsive, efficiently handling incoming data and updating your front-end without bottlenecks.

By decoupling the data production and consumption processes, queues enable you to build scalable and maintainable systems. Whether you're working on trading platforms, IoT applications, or any real-time data-driven system, understanding and utilizing queues effectively can significantly improve your application's performance and reliability.

Happy coding!

