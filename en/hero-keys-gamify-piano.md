---
title: "HeroKeys: Gamifying Piano Learning with AI and the Moises API"
date: "2025-12-11"
excerpt: "How I built an open-source platform that combines audio processing, Guitar Hero-style gamification, and the challenges of deploying ML models in the cloud."
category: "Machine Learning Engineering"
tags: ["Machine Learning", "Music Tech", "Next.js", "Moises API", "Web Audio"]
coverImage: "https://github.com/JoaquimBreno/blog-posts/blob/main/imgs/hero-keys.png?raw=true"
author: "Joaquim Breno"
authorImage: "https://avatars.githubusercontent.com/u/62159887?v=4"
authorBio: "Machine Learning Engineer"
---

# HeroKeys: Gamifying Piano Learning with AI

## A "Guitar Hero" for Those Who Want to Learn Piano with Their Favorite Music

![Hero Keys Banner](https://github.com/JoaquimBreno/blog-posts/blob/main/imgs/hero-keys.png?raw=true)

Learning a musical instrument is an incredible journey, but the initial learning curve can be frustrating. The dependence on complex sheet music or ear training (which beginners don't have yet) creates a huge barrier to entry.

As a hackathon project proposal, I decided along with my team to develop **HeroKeys**. The idea was simple, but ambitious: create a web application that would allow users to upload *any* song, and the system would "listen" to the piano, transcribe the notes, and create an interactive game to practice with a real keyboard.

In this post, I'll detail the project's architecture, how I integrated the Moises API, and, most importantly, the real challenges of putting audio models into production with limited resources.

### The Architecture and the "Magic" of Moises.ai

The heart of HeroKeys is the ability to isolate instruments and understand what's being played. For this, I used the powerful **Moises.ai API**.

The flow works as follows:

1.  **Frontend (Next.js):** The user uploads the audio.
2.  **Source Separation:** The audio is sent to Moises's separation endpoint, which uses Deep Learning to extract only the piano track (the famous *stem*), removing drums, vocals, and bass.
3.  **Chord Recognition:** In parallel, we use Moises's chord detection module to identify the music's harmony in real-time.

Without this separation step, note transcription would be extremely noisy and inaccurate.


### The User Experience and Visualization

For the interface, I drew inspiration from the established mechanics of rhythm games. I developed a **Piano Roll** where notes descend in sync with the audio.

![HeroKeys Interface with Piano Roll](https://github.com/JoaquimBreno/blog-posts/blob/main/imgs/piano_roll_acordes_juice.png?raw=true)

The system supports:
* **MIDI Connection via USB:** You connect your physical keyboard and the browser captures what you play.
* **Real-Time Feedback:** If you hit the note at the right time, you earn points and maintain the *streak* (score multiplier).
* **Sheet Music Visualization:** For those who prefer traditional reading, the system also renders the musical staff.

### The Transcription Challenge and Deployment Limitations (Real Life)

This is where the "real life" part of being an ML Engineer comes in. In the project's original paper, the ideal architecture included a Python backend (FastAPI) running heavy transcription models (like *High-resolution Piano Transcription* or Spotify's *Basic Pitch*).

However, putting this into production "on a budget" is an interesting technical challenge.

I tried to deploy the inference server on **Render** (a cloud application platform). The problem? Audio transcription models based on CNNs or Transformers consume a considerable amount of memory just to load the weights, not to mention processing the audio itself.

The limitation of Render's free/basic tier of **512MB of RAM** was the bottleneck. The process suffered constant OOM (*Out of Memory*) kills when trying to load the model. This illustrates well the dilemma of "Edge AI" or deployment in restricted environments: you can have the best model in the world, but if you don't have the hardware to serve it, it doesn't reach the end user.

Although our server runs the inference of Basic Pitch, an audio-to-MIDI transcription model more optimized for CPU. At the time of the hackathon, I remember trying inference of other models that were much better in terms of accuracy and focused on the piano transcription task. I chose one of them well and managed to put it on a paid GPU cluster—which isn't viable for a free demo. As a fallback, I left Spotify's Basic Pitch ready, installed on my machine. In the end, neither of them are running in the project's initial deployment. I believe that someday machines will be cheaper and I can tinker a bit more with this project :).

### Try It Yourself (Demo Version)

Due to this current infrastructure limitation, I prepared a demo version for you to experience the interface and game mechanics.

You can access it now at: **[hero-keys.vercel.app](https://hero-keys.vercel.app/)**

> **Note:** When accessing, look for the **Demo (Mocked)** option, which is a small, discreet round button. In this version, the MIDI files are already pre-processed to ensure you have a smooth interface experience without depending on the heavy inference backend that I'm still optimizing.

### Next Steps

HeroKeys is an *open-source* project (MIT license) and I continue working on it. My next goals are:

1.  **Model Optimization:** Quantize the transcription model or migrate to a *client-side* solution (TensorFlow.js or ONNX), running inference directly in the user's browser to avoid server costs.
2.  **Automatic Segmentation:** Use AI to detect chorus, verse, and intro, enabling practice loops.
3.  **Improve the scoring system:** Improve score scripts, touch sensitivity, and point multipliers.

If you enjoy music, code, and AI, feel free to contribute to the repository or send a pull request!

---

*Liked the project? Follow my blog for more content about the intersection between music and artificial intelligence.*

