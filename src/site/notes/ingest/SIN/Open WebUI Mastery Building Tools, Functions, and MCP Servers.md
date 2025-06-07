---
{"dg-publish":true,"permalink":"/ingest/sin/open-web-ui-mastery-building-tools-functions-and-mcp-servers/","tags":["gardenEntry"]}
---

 Open WebUI Mastery Building Tools, Functions, and MCP Servers

## 📦 Part 1: Tools – Creating Callable Functions for LLMs

[[sin/Initialization/Docs/Open WebUI Docs/Open WebUI tools\|Open WebUI tools]] [[SIN Docs MAIN/AGI/tools\|Tools]] are structured Python plugins that LLMs can invoke during chats. These [[SIN Docs MAIN/AGI/tools\|Tools]] are bound to specific [[sin/Initialization/Docs/Datasets/8. Datasets\|sin/Initialization/Docs/Datasets/8. Datasets]] that support **function calling**, such as GPT-[[tools-export-1745623456262.json\|tools-export-1745623456262.json]] or Claude.

### 📁 Tool Structure

Every tool lives inside a single `.py` file, with:

* A top-level docstring describing metadata
* A `[[[[SIN Docs MAIN/AGI/tools\|Tools]]]]` class
* Optional `Valves` and `UserValves` for settings

#### ✅ Example: Basic String Reversal Tool

```python
"""title: String Reverser
author: Open WebUI Specialist
description: A tool that reverses a string.
required_open_webui_version: 0.6.0
version: 1.0.0
"""
from pydantic import BaseModel, Field
class Tools:
    class Valves(BaseModel):
        api_key: str = Field("", description="API key if required")
    def __init__(self):
        self.valves = self.Valves()
    def reverse_string(self, text: str) -> str:
        if self.valves.api_key and self.valves.api_key != "1234":
            return "Unauthorized"
        return text[::-1]
```

### 💡 Tool Setup Best Practices

* Add all metadata to the docstring to support import/export.
* Always validate input parameters (e.g., API keys).
* Use `Valves` for shared config.
* Add descriptions for [[sin/Initialization/Docs/Datasets/8. Datasets\|sin/Initialization/Docs/Datasets/8. Datasets]] understanding.

### ⚙️ Import & Enable in Open WebUI

[[tools-export-1745623456262.json\|tools-export-1745623456262.json]]. Import from the [[sin/Initialization/Docs/Open WebUI Docs/Open WebUI tools\|Open WebUI tools]]: **Workspace > [[SIN Docs MAIN/AGI/tools\|Tools]] > Import Tool**
[[02-05-2025\|02-05-2025]]. Assign to [[sin/Initialization/Docs/Datasets/8. Datasets\|sin/Initialization/Docs/Datasets/8. Datasets]] in **Workspace > [[sin/Initialization/Docs/Datasets/8. Datasets\|sin/Initialization/Docs/Datasets/8. Datasets]] > [[SIN Docs MAIN/AGI/tools\|Tools]]**
[[tools-export-1745623456262.json\|tools-export-1745623456262.json]]. Optionally use the `autotool_filter` for automatic function routing.

---

## 🎛️ Part 2: Pipe Functions – Defining Custom Model Logic

A **Pipe** defines a model route with custom business logic. It's useful for model orchestration, OpenAI proxying, or even scripted agents.

### 🔧 Barebones Pipe Example

```python
from pydantic import BaseModel, Field
class Pipe:
    class Valves(BaseModel):
        MODEL_ID: str = Field(default="debug-model")
    def __init__(self):
        self.valves = self.Valves()
    def pipe(self, body: dict):
        print("Incoming body:", body)
        return f"Received input: {body.get('messages', [{}])[-1].get('content', '')}"
```

### 🧪 Manifold Setup (Multiple Models)

You can define multiple models using `pipes()`:

```python
def pipes(self):
    return [
        {"id": "alpha", "name": "Alpha Bot"},
        {"id": "beta", "name": "Beta Bot"}
    ]
```

This lets you expose multiple "model options" from one Pipe.

---

## 🧰 Part 3: MCP Servers – Wrapping Legacy Tools as OpenAPI

Sometimes, tools are implemented in MCP. Open WebUI allows you to proxy them using the `mcpo` tool.

### 🔁 Convert MCP to OpenAPI with `mcpo`

**Quickstart:**

```bash
pip install mcpomcpo
uvx mcpo --port 8000 -- uvx mcp-server-time --local-timezone=America/New_York
```

Now you can access the OpenAPI docs at <http://localhost:8000/docs> and connect the server in Open WebUI under **Settings > Tools**.

### 🔐 Common Pitfalls

* Add **CORS middleware** if testing locally via browser:

  ```python
  from fastapi.middleware.cors import CORSMiddleware
  app.add_middleware(
      CORSMiddleware,
      allow_origins=["*"],
      allow_methods=["*"],
      allow_headers=["*"]
  )
  ```

* Ensure your local tool uses HTTPS when Open WebUI is served over HTTPS, or run both on `localhost`.

---# 🧠 Open WebUI Mastery: Building Tools, Functions, and MCP Servers (Page 2)

---

## 🎨 Part 4: Filters – Modify Inputs, Streamed Outputs, and Final Outputs

**Filters** let you intercept, rewrite, or polish data at 3 different stages of a chat:
Stage Function Purpose Before LLM `inlet` Modify user's prompt before it's sent During LLM `stream` Modify live streamed chunks After LLM `outlet` Modify full completed response

### ✨ Basic Filter Skeleton

```python
from pydantic import BaseModel
class Filter:
    class Valves(BaseModel):
        rewrite_prefix: str = "User said:"
    def __init__(self):
        self.valves = self.Valves()
    def inlet(self, body: dict) -> dict:
        body['messages'][-1]['content'] = f"{self.valves.rewrite_prefix} {body['messages'][-1]['content']}"
        return body
    def stream(self, event: dict) -> dict:
        # Modify mid-stream text here
        return event
    def outlet(self, body: dict) -> None:
        # Optionally log or clean final output
        print("Final Output:", body)
```

---

### 🔥 Real-World Filter Examples

* **Input Guardrails**: Detect offensive content before it goes to the LLM.
* **Stream Adjustments**: Strip unwanted tokens like `[smiles]`.
* **Outlet Redactions**: Mask sensitive outputs like emails or phone numbers.

### 📢 Tips When Building Filters

* Always validate your input dictionary structure (`body.get('messages', [])[-1].get('content', '')`).
* Filters run **fast** and **often**; keep `stream()` extremely lightweight.
* Filters are optional—returning unchanged body/event is fine.

---

## 🎬 Part 5: Action Functions – Adding Interactive Buttons in Chat

**Actions** insert a custom button under any user or AI message.
Think of Actions as:  
🔘 **Button ➔ Event Call ➔ User Interaction**

---

### 📜 Basic Action Example

```python
async def action(
    self,
    body: dict,
    __user__=None,
    __event_emitter__=None,
    __event_call__=None
) -> dict:
    response = await __event_call__({
        "type": "input",
        "data": {
            "title": "Custom Reply",
            "message": "Please write a reply",
            "placeholder": "Type something..."
        }
    })
    return {"user_input": response.get('text', '')}
```

**Key Points:**

* `__event_call__` prompts the user.
* You can chain multiple events (`input`, `confirm`, `select`).
* Action results are posted back into the conversation automatically.

---

### 🔗 Action Types Available

* `input`: Get a text input
* `confirm`: Yes/No
* `select`: Choose from dropdown options
* `download`: Trigger download of data
* `visualize`: Display structured visual graphs

---

## 🛠️ Part 6: Connecting OpenAPI Tool Servers Natively (Post-0.6.0)

Since Open WebUI v0.6+, you can connect **any** OpenAPI-compliant server directly.

---

### 🚀 Steps to Connect a Local OpenAPI Tool Server

1. Start your server (FastAPI, Flask, Express, etc.)
2. Ensure you have **CORS headers enabled**:

   ```python
   from fastapi.middleware.cors import CORSMiddleware
   app.add_middleware(
       CORSMiddleware,
       allow_origins=["*"],
       allow_credentials=True,
       allow_methods=["*"],
       allow_headers=["*"]
   )
   ```

3. Open Open WebUI → **Settings → Tools** ➕
4. Add your tool server URL (e.g., `http://localhost:8000`)
5. Save. Done!

---

### 🌐 Native Tool Calling (ReACT)

To use **function calling** natively inside chats:

* Open a chat
* ⚙️ Advanced Params → Set "Function Calling" ➔ `Native`
* Now Open WebUI will **auto-call functions** when prompted!

> **Note**: Your models need to truly support OpenAI-style function calling (e.g., GPT-4-turbo, Claude 3 Opus).

---

## 🚨 Common Problems & Fixes

Problem Cause Fix ❌ [[Tools]] don't show up Wrong OpenAPI spec or missing `/openapi.json` Check server `/docs` page manually ❌ CORS Error No CORS headers added Use `CORSMiddleware` or equivalent ❌ "Mixed Content" Error HTTPS [[sin/Initialization/Docs/Open WebUI Docs/Open WebUI tools\|Open WebUI tools]] talking to HTTP server Serve tool server over HTTPS locally or tunnel via ngrok ❌ Function not triggered Model doesn't support native [[sin/Initialization/Docs/Open WebUI Docs/Functions/Functions\|sin/Initialization/Docs/Open WebUI Docs/Functions/Functions]] Switch to GPT-4o, GPT-[[tools-export-1745623456262.json\|tools-export-1745623456262.json]] Turbo, or Claude [[tools-export-1745623456262.json\|tools-export-1745623456262.json]]

# 🧠 Open WebUI Mastery Building Tools, Functions, and MCP Servers

---

## 🛡️ Part 7: Advanced Architecture – Managing Multiple Servers and Security

Now that you're building real systems, not just playing with local scripts, it's time to **level up**:

* Multi-server tool orchestration
* Secure production deployments
* JWT, Bearer Token, and CORS strategies

---

### 🏗️ Multi-Tool Server Architecture

🔵 **Why use multiple tool servers?**

* Keep tools modular and easy to update
* Scale servers independently
* Load balance across infrastructure
* Isolate risky or experimental tools

---

**Example Deployment Setup:**
Server Purpose Port `tools-search` Web search APIs 8000 `tools-imagegen` Stable Diffusion image generator 8001 `tools-voice` ElevenLabs voice synth server 8002
Each one runs a separate OpenAPI FastAPI instance!

---

**How Open WebUI handles this:**

* You connect each OpenAPI server independently.
* All tools are merged into the "tools available" list automatically.
* User sees tools unified at chat runtime.

✅ **No extra coding needed.** Just plug and play.

---

## 🔐 Part 8: Authentication (JWT, Bearer Tokens)

Open WebUI tools and chat completions require secure authorization:
Use Case Token Type Notes API endpoints Bearer Token (API Key) Generated via WebUI settings Custom tool server auth JWT preferred Validate via middleware

---

**🔑 How to Add Bearer Auth to Your Own OpenAPI Server:**
If you are writing a server with FastAPI:

```python
from fastapi import Depends, HTTPException, Security
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
security = HTTPBearer()
async def verify_token(credentials: HTTPAuthorizationCredentials = Depends(security)):
    if credentials.credentials != "YOUR_SECRET_API_KEY":
        raise HTTPException(status_code=403, detail="Unauthorized")
```

Apply to endpoints:

```python
@app.get("/protected-endpoint")
async def protected_route(credentials: HTTPAuthorizationCredentials = Depends(verify_token)):
    return {"message": "Welcome Authorized User"}
```

✅ Now only authorized Open WebUI requests will reach your server.

---

**🧩 How to Validate JWT in Tools (Optional Advanced Security):**
If your tools expect user-specific permissions:

```python
import jwt
def decode_jwt(token: str):
    try:
        payload = jwt.decode(token, "SECRET_KEY", algorithms=["HS256"])
        return payload
    except jwt.PyJWTError:
        raise HTTPException(status_code=401, detail="Invalid Token")
```

✅ This is good when your server shares login states or enforces per-user tool access.

---

## 🌎 Part 9: Deploying Production-Ready OpenAPI Tool Servers

When you’re ready to **move beyond localhost**, follow this checklist:

### 🧹 Server Hardening Checklist

* ✅ **Enable HTTPS** (Let's Encrypt via Traefik, nginx proxy, or native certs)
* ✅ **Restrict Origins** (`allow_origins=["https://yourdomain.com"]`)
* ✅ **Use Bearer Tokens** everywhere
* ✅ **Resource Limits** (timeout limits, memory constraints)
* ✅ **Observability** (enable logging, Sentry, tracing)
* ✅ **Run behind Reverse Proxy** (Caddy, nginx, Traefik)
* ✅ **Auto-Scaling** (optional: docker swarm, Kubernetes)

---

### 🛡️ Example: FastAPI Server Secured Behind Nginx

`nginx.conf`:

```nginx
server {
    listen 443 ssl;
    server_name yourserver.com;
    ssl_certificate /etc/letsencrypt/live/yourserver.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourserver.com/privkey.pem;
    location / {
        proxy_pass http://127.0.0.1:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

✅ Serve OpenAPI securely without exposing your app's internals directly!

---

## 📑 Part 10: Templates for Your Own OpenAPI Servers

Want to build a tool server FAST? Use this base:

```python
from fastapi import FastAPI
from pydantic import BaseModel
from fastapi.middleware.cors import CORSMiddleware
app = FastAPI()
# Allow CORS
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # In prod, lock to your domain
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
# Tool definition
class ReverseInput(BaseModel):
    text: str
@app.post("/reverse")
async def reverse(input: ReverseInput):
    return {"reversed_text": input.text[::-1]}
```

Then:

```bash
uvicorn server:app --host 0.0.0.0 --port 8000
```

Boom: you now have a **custom tool server** ready for [[sin/Initialization/Docs/Open WebUI Docs/Open WebUI tools\|Open WebUI tools]]. 🚀
