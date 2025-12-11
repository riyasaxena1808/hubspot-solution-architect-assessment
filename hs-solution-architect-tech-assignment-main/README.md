# HubSpot Integration Backend - Breezy Technical Assessment

This project is my submission for the HubSpot Solutions Architect Technical Assessment.
It includes a backend server (provided in starter) and a full working frontend that demonstrates how Breezy, a smart thermostat company, could integrate their platform with HubSpot CRM.

Along with all required endpoints, I have also implemented the optional AI Insights feature, which generates smart summaries of HubSpot contacts and deals using OpenAI.

## Overview

This Express.js server handles authentication and proxies requests to the HubSpot CRM API.
A custom UI located in /public/index.html interacts with these backend endpoints to simulate Breezy’s internal admin panel.

The system demonstrates:

Syncing customers → HubSpot Contacts

Tracking subscription conversions → HubSpot Deals

Viewing deal history per contact

AI-powered insights about CRM activity (optional extra feature)

## Prerequisites

- Node.js (v14 or higher)
- npm or yarn
- A HubSpot account (free)
- A HubSpot Private App access token
- (Optional) OpenAI API key for AI Insights feature

## Setup Instructions

### 1. Install Dependencies

1. Install Dependencies
npm install


This installs Express, Axios, CORS, nodemon (dev), and OpenAI SDK.

### 2. Get Your HubSpot Access Token

1. Sign in to your HubSpot account
2. Navigate to Settings → Integrations → Private Apps
3. Click Create a private app
4. Give it any name (e.g., Breezy Integration App)
5. Open the Scopes tab and enable:
   - crm.objects.contacts.read
   - crm.objects.contacts.write
   - crm.objects.deals.read
   - crm.objects.deals.write
6. Click Create
7. Copy the private app access token

This token is used by the backend to authenticate all CRM requests.

### 3. Configure Environment Variables

Copy the example env file:

cp .env.example .env


Open .env and set:

HUBSPOT_ACCESS_TOKEN=your-hubspot-token-here
OPENAI_API_KEY=your-openai-api-key-here # Optional, required for AI Insights


Notes:

- No quotes around the API keys
- Do not commit .env to GitHub

### 4. Start the Server

**For development (with hot-reloading):**

```bash
npm run dev
```

This will automatically restart the server when you make changes to `server.js`.

**For production:**

```bash
npm start
```

You should see:

```
✅ Server running successfully!
🌐 API available at: http://localhost:3001
📋 Health check: http://localhost:3001/health
📁 Static files served from: /public
```

**To stop the server:** Press `Ctrl+C` (the server will gracefully shut down)

### 5. Test the Server

Open:

http://localhost:3001/health


or run:

curl http://localhost:3001/health


Expected response:

{
  "status": "Server is running",
  "timestamp": "2025-11-10T..."
}


## Frontend Application (UI Overview)

A complete frontend UI is included in `/public/index.html`.  
It automatically loads when you open:

http://localhost:3001/

This UI simulates Breezy’s internal admin dashboard and uses the backend API to perform all operations required in the assessment.

### ✔ View Contacts (Auto-loaded from HubSpot)

The UI loads all HubSpot contacts automatically when you open the dashboard.

It calls:

GET /api/contacts

The table shows:
- First name  
- Last name  
- Email  
- Job title  
- Company  
- Contact ID  
- A **View deals** button

---

### ✔ Create / Sync Contact

This simulates when a Breezy customer creates an account or purchases a thermostat.

It calls:

POST /api/contacts

Fields in the form:
- First name  
- Last name  
- Email  
- Phone (optional)  
- Address (optional)  
- Job title (optional)  
- Company (optional)

After submitting:
- The contact is created in HubSpot  
- A success message appears  
- The contacts list refreshes

---

### ✔ Create Subscription Deal

This simulates when a user upgrades to Breezy Premium.

It calls:

POST /api/deals

Form fields:
- Contact (dropdown populated from HubSpot)
- Deal name
- Amount
- Deal stage (closedwon, appointmentscheduled, qualifiedtobuy, closedlost)

After submitting:
- The deal is created  
- It is linked to the selected contact  
- Success message appears

---

### ✔ View Deals for a Contact

When clicking **View deals**, the UI calls:

GET /api/contacts/:contactId/deals

This shows:
- Deal ID  
- Deal name  
- Amount  
- Deal stage  

This helps track subscription history per customer.

---

### ✔ AI Insights (Optional Feature)

This optional feature uses OpenAI to analyze CRM data.

It calls:

GET /api/ai/summary

The AI section displays:
- Number of contacts  
- Number of deals  
- Total revenue  
- Interesting patterns  
- Insights about customer behavior

## API Endpoints

### Health Check

**GET** `/health`

Check if the server is running.

**Response:**

```json
{
  "status": "Server is running",
  "timestamp": "2025-11-10T12:00:00.000Z"
}
```

---

### Get Contacts

**GET** `/api/contacts`

Fetch all contacts from HubSpot (limited to 50).

**Response:**

```json
{
  "results": [
    {
      "id": "12345",
      "properties": {
        "firstname": "Alex",
        "lastname": "Rivera",
        "email": "alex@example.com",
        "phone": "555-0123",
        "address": "123 Main St"
      }
    }
  ]
}
```

---

### Create Contact

**POST** `/api/contacts`

Create a new contact in HubSpot.

**Request Body:**

```json
{
  "properties": {
    "firstname": "Alex",
    "lastname": "Rivera",
    "email": "alex@example.com",
    "phone": "555-0123",
    "address": "123 Main St"
  }
}
```

**Response:**

```json
{
  "id": "12345",
  "properties": {
    "firstname": "Alex",
    "lastname": "Rivera",
    "email": "alex@example.com",
    ...
  }
}
```

---

### Get All Deals

**GET** `/api/deals`

Fetch all deals from HubSpot (limited to 50).

**Response:**

```json
{
  "results": [
    {
      "id": "67890",
      "properties": {
        "dealname": "Breezy Premium - Annual",
        "amount": "99",
        "dealstage": "closedwon"
      }
    }
  ]
}
```

---

### Create Deal

**POST** `/api/deals`

Create a new deal in HubSpot and associate it with a contact.

**Request Body:**

```json
{
  "dealProperties": {
    "dealname": "Breezy Premium - Annual Subscription",
    "amount": "99",
    "dealstage": "closedwon"
  },
  "contactId": "12345"
}
```

**Response:**

```json
{
  "id": "67890",
  "properties": {
    "dealname": "Breezy Premium - Annual Subscription",
    "amount": "99",
    "dealstage": "closedwon"
  }
}
```

---

### Get Deals for Contact

**GET** `/api/contacts/:contactId/deals`

Get all deals associated with a specific contact.

**Example:**

```
GET /api/contacts/12345/deals
```

**Response:**

```json
{
  "results": [
    {
      "id": "67890",
      "properties": {
        "dealname": "Breezy Premium - Annual",
        "amount": "99",
        "dealstage": "closedwon"
      }
    }
  ]
}
```

## Testing with cURL

### Create a contact:

```bash
curl -X POST http://localhost:3001/api/contacts \
  -H "Content-Type: application/json" \
  -d '{
    "properties": {
      "firstname": "Test",
      "lastname": "Customer",
      "email": "test@breezy.com"
    }
  }'
```

### Get all contacts:

```bash
curl http://localhost:3001/api/contacts
```

### Create a deal:

```bash
curl -X POST http://localhost:3001/api/deals \
  -H "Content-Type: application/json" \
  -d '{
    "dealProperties": {
      "dealname": "Breezy Premium - Monthly",
      "amount": "9.99",
      "dealstage": "closedwon"
    },
    "contactId": "12345"
  }'
```

## Common Deal Stages

For the Breezy use case, you can use these standard HubSpot deal stages:

- `appointmentscheduled` - Trial started
- `qualifiedtobuy` - Active trial user
- `closedwon` - Converted to paid subscription
- `closedlost` - Trial ended without conversion

## Error Handling

All endpoints return errors in this format:

```json
{
  "error": "Human-readable error message",
  "details": "Technical details from HubSpot API"
}
```

Common errors:

- **401 Unauthorized**: Check your `HUBSPOT_ACCESS_TOKEN` in `.env`
- **403 Forbidden**: Your private app may not have the required scopes
- **404 Not Found**: Contact or deal ID doesn't exist
- **500 Internal Server Error**: Check console logs for details

## Project Structure

```
hs-solution-architect-tech-assessment/
│
├── server.js
├── README.md
├── .env.example
├── package.json
├── package-lock.json
├── public/
│   └── index.html
└── node_modules/

```

## What This Project Demonstrates

This project implements a complete frontend (in `/public/index.html`) that:

1. Displays contacts from `GET /api/contacts`
2. Creates/syncs contacts via `POST /api/contacts`
3. Creates subscription deals via `POST /api/deals`
4. Shows deal history per contact via `GET /api/contacts/:contactId/deals`
5. Generates AI insights using the OpenAI API via `GET /api/ai/summary`


## Troubleshooting

### Port 3001 Already in Use

If you see an error like `EADDRINUSE: address already in use ::1:3001`:

**On Mac/Linux:**

```bash
# Find the process using port 3001
lsof -ti:3001

# Kill the process
kill -9 $(lsof -ti:3001)
```

**On Windows:**

```bash
# Find the process
netstat -ano | findstr :3001

# Kill it (replace PID with the number from above)
taskkill /PID <PID> /F
```

**Note:** The updated `server.js` now includes graceful shutdown, so pressing `Ctrl+C` should properly close the port.

### Other Common Issues

1. **401 Unauthorized**: Check that your `.env` file has a valid `HUBSPOT_ACCESS_TOKEN`
2. **403 Forbidden**: Your HubSpot Private App may not have the required scopes
3. **404 Not Found**: Contact or deal ID doesn't exist in your HubSpot portal
4. **Module not found**: Run `npm install` to install dependencies
5. Check the console logs for detailed error messages
6. Test endpoints with curl to isolate frontend vs backend issues

## Features

- Graceful shutdown (Ctrl+C)
- Hot reload via npm run dev
- Static file serving from /public
- Comprehensive error handling
- HubSpot token validation on startup
- Optional AI analysis module using OpenAI

Good luck with your assessment!
