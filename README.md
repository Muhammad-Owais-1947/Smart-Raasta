# Smart Raasta - AI Career Roadmap API

![Project Status](https://img.shields.io/badge/Status-Active-success) ![Tech Stack](https://img.shields.io/badge/Backend-Cloudflare%20Workers-orange) ![AI](https://img.shields.io/badge/AI-Google%20Gemini-blue)

## 📖 Project Overview

**Smart Raasta** is an intelligent, AI-driven backend system designed to generate hyper-personalized career roadmaps for the Pakistani market. Built on a serverless architecture using **Cloudflare Workers**, it leverages the **Google Gemini API** to function as an elite "Career Strategist."

The system processes user inputs (Career Goal, Interests, Education, Location) and utilizes a sophisticated "Domain Convergence" algorithm to generate JSON-structured roadmaps. It specifically handles hybrid career paths (e.g., a Banker interested in Python) to create unique, value-added career trajectories.

### Key Features
* **Serverless Architecture:** High-performance, low-latency execution at the edge.
* **JWT Authentication:** Secure, stateless session management via custom JWT signing/verifying.
* **OTP Verification:** Email-based One-Time Password system for user validation.
* **Intelligent Rate Limiting:** Dual-layer limiting (IP-based and Account-based) using Cloudflare KV.
* **Failover AI Strategy:** Implements a sequential failover for multiple API keys to ensure 99.9% uptime.

---

## 🔒 Security & Code Transparency Policy

**Why isn't the exact production code shared?**

As a live financial and educational tool, the production backend contains sensitive configurations that cannot be exposed publicly. These include:

1.  **Proprietary Algorithms:** The specific "Prompt Engineering" and logic gates used to guide the AI are intellectual property.
2.  **Infrastructure Bindings:** Direct references to Cloudflare KV namespaces and encrypted environment secrets.
3.  **Admin Protocols:** Internal bypass mechanisms used for system administration.

### 🛡️ The "Open Source" Version
To maintain transparency and help the developer community, we are sharing the **core logic architecture** below. This version removes the specific secrets and simplifies the proprietary prompts but retains the exact functional flow of the application.

You can use this to understand how we handle **CORS**, **JWT Authentication**, and **AI Stream Handling**

---

## 💻 Logic Flow (Pseudocode)

This represents the structural logic running on our Cloudflare Workers.

```javascript
/* SMART RAASTA - SERVERLESS WORKER LOGIC 
  --------------------------------------
  Structure: Event-Driven Fetch Handler
*/

// 1. GLOBAL CONFIGURATION
// - Define CORS headers for frontend security
// - Initialize Environment Variables (API Keys, KV Bindings)

// 2. CRYPTOGRAPHY HELPER LAYER
// - Function signJWT(payload): Uses Native Web Crypto API to sign tokens
// - Function verifyJWT(token): Decodes and validates signature expiration

// 3. MAIN REQUEST HANDLER (Event Loop)
async function handleRequest(request) {
  
  // -- PHASE 1: PRE-FLIGHT CHECKS --
  // If method is OPTIONS -> Return CORS headers immediately

  // -- PHASE 2: ROUTING & LOGIC --

  // ROUTE A: /send-otp (User Login Entry)
  if (path === '/send-otp') {
    // 1. Validate email format
    // 2. Generate cryptographically secure random 6-digit code
    // 3. Store in Cloudflare KV (Key-Value Storage) with 5 min TTL
    // 4. Trigger external Email Service provider
    // 5. Return Success/Failure status
  }

  // ROUTE B: /verify-otp (Session Creation)
  if (path === '/verify-otp') {
    // 1. Retrieve stored OTP from KV using email key
    // 2. Compare User Input vs. Stored OTP
    // 3. If Match:
    //    - Create JSON Web Token (JWT) with 1-hour expiry
    //    - Sign JWT with HS256 algorithm
    //    - Return HTTPOnly Secure Cookie (Prevent XSS attacks)
  }

  // ROUTE C: /generate-roadmap (The Core Engine)
  if (path === '/generate') {
    
    // STEP 1: AUTHENTICATION GATE
    // - Extract JWT from Cookie
    // - Verify Signature -> If invalid, Reject (401 Unauthorized)

    // STEP 2: RATE LIMITING (Anti-Abuse System)
    // - Check Cloudflare KV for user's generation count
    // - If count > 3 (Hourly Limit) -> Reject (429 Too Many Requests)
    // - Else -> Increment count

    // STEP 3: AI ORCHESTRATION
    // - Construct "Enterprise Prompt" with User Data + Security Rules
    // - SELECT API KEY: Check list of available keys
    
    // STEP 4: SEQUENTIAL FAILOVER
    // - Attempt Request with Primary API Key
    // - IF 429/Error -> Automatically retry with Backup API Key
    
    // STEP 5: DATA SANITIZATION
    // - Parse AI response (JSON)
    // - Validate structure (Ensure no malicious code injection)
    // - Return clean JSON to frontend
  }
}

  return new Response("API Active", { status: 200 });
}
