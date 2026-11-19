# StoryWeave (backend)

A collaborative story where everyone adds a sentence and a language model occasionally wrecks it.

This is the FastAPI backend. The React frontend is [storyweave-frontend](https://github.com/justhashim/storyweave-frontend).

## What it does

- Users register and log in (stored in Supabase).
- Rooms are listed from a Supabase `rooms` table.
- Each room is a WebSocket. Everyone in it adds text and sees the shared story update live.
- Every fifth addition in a room, GPT-2 writes a "plot twist" from the last 200 words and appends it to the story.

## How it works

- `GET /rooms`, `POST /register` and `POST /login` talk to Supabase.
- `/ws/{room_id}?username=...` takes `add` and `get_story` messages and broadcasts `story_update`, `twist_accepted` and `user_left`.
- The twist is `gpt2` through the `transformers` text-generation pipeline. The prompt is the end of the story plus "But then, something unexpected happened:", cut at the first full stop.
- Story text lives in memory per process. Restart the server and the stories are gone.

## Stack

- Python, FastAPI, uvicorn
- Hugging Face `transformers` (GPT-2), PyTorch
- Supabase (users and rooms)
- Vercel config included (`vercel.json`)

## Run it locally

```bash
pip install -r requirements.txt
python -m uvicorn main:app --reload
```

Env vars needed: `SUPABASE_URL`, `SUPABASE_KEY`. The server exits on startup without them. It expects `users` and `rooms` tables.
The first run downloads GPT-2.

## Notes

Built in 24 hours at the Grab A Pi Hacknight 2025 hackathon, so treat it as hackathon code. Passwords are stored and compared as plain text, and CORS allows every origin. Fix both before real use.
The hosted demo is gone (the old Vercel deployments return 404). Demo video: [Google Drive](https://drive.google.com/file/d/1MVkfO9Egz7OYMO5P1V_LLRtHLkTZutCb/view).
Forked from [Habeeb00's original repo](https://github.com/Habeeb00/Grab-a-pi).
