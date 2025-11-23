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

As a production-level financial and educational tool, the exact backend deployed on our servers contains sensitive configurations that cannot be exposed publicly. These include:

1.  **Proprietary Prompt Engineering:** The specific system instructions and "Logic Gates" used to guide the AI are intellectual property.
2.  **Security Bindings:** Hardcoded references to specific Cloudflare KV namespaces and Environment Secrets.
3.  **Admin Backdoors:** Specific email bypasses used for administration and debugging.

### 🛡️ The "Open Source" Version
To maintain transparency and help the developer community, we are sharing the **core logic architecture** below. This version removes the specific secrets and simplifies the proprietary prompts but retains the exact functional flow of the application.

You can use this to understand how we handle **CORS**, **JWT Authentication**, and **AI Stream Handling**.

---

## 💻 Backend Logic (Sanitized)

This is the architectural skeleton of the `worker.js` file running on Cloudflare.

```javascript
addEventListener('fetch', event => {
  event.respondWith(handleRequest(event.request))
})

// Standard CORS configuration for frontend communication
const CORS_HEADERS = {
  'Access-Control-Allow-Origin': '[https://your-frontend-domain.com](https://your-frontend-domain.com)', 
  'Access-Control-Allow-Credentials': 'true',
  'Access-Control-Allow-Methods': 'GET, POST, OPTIONS',
  'Access-Control-Allow-Headers': 'Content-Type',
}

const SESSION_DURATION_SECONDS = 3600; // 1 Hour Token

// --- JWT CRYPTO HELPERS (Native Web Crypto API) ---
async function signJWT(payload, secret) {
  // Implementation of HS256 signing using Web Crypto API
  // ... (Standard JWT signing logic)
}

async function verifyJWT(token, secret) {
  // Verifies signature and checks expiration
  // ... (Standard JWT verification logic)
}

// --- SYSTEM INSTRUCTIONS ---
// Note: The production version contains 100+ lines of specific context for the Pakistani Market.
const customApiInstructions = `
You are an elite Career Strategist.
Your goal is to generate a highly detailed, actionable career roadmap.
[...Proprietary Logic Redacted...]
`;

async function handleRequest(request) {
  const url = new URL(request.url);
  const path = url.pathname;

  // Handle CORS Preflight
  if (request.method === 'OPTIONS') return new Response(null, { headers: CORS_HEADERS });

  // 1. SEND OTP ROUTE
  if (path === '/send-otp' && request.method === 'POST') {
     // Generates random 6-digit code
     // Stores in KV (Key-Value) storage with 5-minute expiration
     // Triggers Email Service
  }

  // 2. VERIFY OTP ROUTE
  if (path === '/verify-otp' && request.method === 'POST') {
     // Checks KV storage for matching OTP
     // If valid -> Generates JWT Token signed with JWT_SECRET
     // Sets HTTP-Only Secure Cookie
  }

  // 3. GENERATE ROADMAP ROUTE (The Core Logic)
  if (path === '/generate' && request.method === 'POST') {
    try {
      const body = await request.json();
      
      // A. Authentication Check
      // Extracts JWT from cookie -> Verifies User

      // B. Rate Limiting (Anti-Abuse)
      // Checks Cloudflare KV for user's usage count
      // If Usage > Limit -> Return 429 Error

      // C. AI Request Construction
      const prompt = `${customApiInstructions} ... user inputs ...`;
      
      // D. Sequential Failover Logic
      // Tries API_KEY_1. If fails/limits reached -> Tries API_KEY_2
      const geminiUrl = `https://generativelanguage.googleapis.com/...`;
      
      // E. Return Parsed JSON to Frontend
      return new Response(JSON.stringify(roadmap), { headers: CORS_HEADERS });

    } catch (e) {
      return new Response(JSON.stringify({ error: "Generation Failed" }), { status: 500 });
    }
  }

  return new Response("API Active", { status: 200 });
}
