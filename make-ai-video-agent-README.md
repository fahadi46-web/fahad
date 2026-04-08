# AI Video Agent - Daily YouTube Upload (make.com)

This make.com scenario automatically generates and uploads a new AI-produced video to YouTube every day — no manual work required.

## How It Works

```
Daily Schedule → ChatGPT generates script + metadata → Pictory renders video → YouTube upload → Slack notification
```

1. The scenario triggers once daily at 9:00 AM
2. ChatGPT (GPT-4o-mini) picks a trending topic and writes a full video script, title, description, and tags
3. The script is sent to Pictory to render a complete video with voiceover and visuals
4. The finished video is downloaded and uploaded to your YouTube channel
5. A Slack message confirms the upload with the video link

---

## Setup

### Step 1 — Import the Scenario into make.com

1. Log in to [make.com](https://make.com)
2. Go to **Scenarios → Create a new scenario**
3. Click the three-dot menu → **Import Blueprint**
4. Upload `make-ai-video-agent.json`

---

### Step 2 — Create Connections

You need three connections in make.com.

#### A. OpenAI Connection

1. In make.com go to **Connections → Add a connection**
2. Search for **OpenAI** and select it
3. Enter your OpenAI API key
4. Save and note the Connection ID — paste it into module **#2** (`connectionId`)

> Get your key at: https://platform.openai.com/api-keys

#### B. YouTube Connection

1. In make.com go to **Connections → Add a connection**
2. Search for **YouTube** and select it
3. Sign in with the Google account that owns your channel
4. Grant the requested permissions
5. Save and note the Connection ID — paste it into module **#8** (`connectionId`)

#### C. Slack Connection (optional)

1. In make.com go to **Connections → Add a connection**
2. Search for **Slack** and select it
3. Authorize your Slack workspace
4. Save and note the Connection ID — paste it into module **#9** (`connectionId`)

> If you don't use Slack, delete module **#9** from the scenario.

---

### Step 3 — Add Pictory Credentials

In module **#4** (Submit to Pictory) and module **#6** (Get Job Status), replace:

| Placeholder | Where to find it |
|---|---|
| `YOUR_PICTORY_API_KEY` | Pictory → Account → API |
| `YOUR_PICTORY_USER_ID` | Pictory → Account → Profile |

> Sign up at: https://pictory.ai

---

### Step 4 — Adjust the Schedule

Module **#1** (Clock) is set to run at **09:00 daily**.

To change the time:
1. Click the Clock module
2. Change the `time` field to your preferred time (24-hour format, e.g. `"18:30"` for 6:30 PM)

---

### Step 5 — Set Your YouTube Category

Module **#8** uses `categoryId: "27"` (Education). Change it to match your channel:

| ID | Category |
|---|---|
| 22 | People & Blogs |
| 24 | Entertainment |
| 26 | Howto & Style |
| 27 | Education |
| 28 | Science & Technology |

---

### Step 6 — Activate the Scenario

1. Open the scenario in make.com
2. Toggle the **Active** switch (bottom-left) to ON

---

## Using a Different Video Generator

The scenario uses Pictory by default. To swap it out:

1. Delete modules **#4**, **#6**, and **#7**
2. Replace with HTTP modules pointing to your preferred API:
   - **D-ID:** `https://api.d-id.com/talks`
   - **Synthesia:** `https://api.synthesia.io/v2/videos`
   - **InVideo:** Use their API or Zapier/make.com native module
3. Update the video URL mapping in module **#8** to use the new API's output field

---

## Customizing the AI Prompt

Open module **#2** (OpenAI) and edit the `user` message content.

You can change the prompt to:
- Target a specific niche (e.g. "finance tips", "fitness for beginners")
- Set a fixed video length or tone
- Include a call-to-action in every script
- Output in a different language

---

## Scenario Modules Overview

| # | Module | Purpose |
|---|---|---|
| 1 | Clock | Daily trigger at 9:00 AM |
| 2 | OpenAI ChatCompletion | Generate script, title, description, tags |
| 3 | JSON Parse | Extract fields from OpenAI JSON response |
| 4 | HTTP POST (Pictory) | Submit script for video rendering |
| 5 | Sleep | Wait 3 minutes for render to finish |
| 6 | HTTP GET (Pictory) | Retrieve completed video URL |
| 7 | HTTP Download | Download video as binary file |
| 8 | YouTube Upload | Upload video with AI metadata |
| 9 | Slack Notify | Post confirmation message (optional) |

---

## Troubleshooting

| Problem | Fix |
|---|---|
| OpenAI returns non-JSON | Ensure `response_format: json_object` is set in module #2 |
| Pictory job not ready | Increase the Sleep delay in module #5 (try 300s) |
| YouTube upload fails | Check OAuth scopes include `youtube.upload` |
| Video URL is empty | Log module #6 output and verify the correct JSON path for your video API |
| Scenario not triggering | Confirm the scenario is set to **Active** |
