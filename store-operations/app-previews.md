# App Previews & Video Specifications Handbook

This handbook details the video encoding parameters, frame rate requirements, poster frame selection, FFmpeg transcoding CLI commands, and store review compliance rules for **App Previews (iOS)** and **Promo Videos (Android)** in Expo and React Native applications.

Engineered in alignment with **2026 platform specifications**, it provides exhaustive technical and operational guidance for creating compliant video previews while avoiding rejections under Apple Guideline 2.3.4 (App Previews).

This guide is **not**:

- a video editing tutorial for cinematic marketing trailers
- an authorization mechanism to include live-action footage of actors using physical devices
- a substitute for verifying video frame rates and audio encoding specs

---

# 1. Video Technical Specifications Matrix

Store platforms strictly reject video uploads failing technical encoding constraints:

```text
┌────────────────────────────────────────────────────────┐
│             APP PREVIEW VIDEO ENCODING SPECS           │
│                                                        │
│  - Video Codec: H.264 High Profile                     │
│  - Frame Rate: 30 fps (or 60 fps) constant            │
│  - Video Duration: Minimum 15 seconds, Max 30 seconds  │
│  - Audio Codec: AAC-LC stereo, 48 kHz or 44.1 kHz     │
│  - Max File Size: 500 MB                               │
└──────────────────────────┬─────────────────────────────┘
                           │
                           ▼
┌────────────────────────────────────────────────────────┐
│             POSTER FRAME SELECTION                     │
│  - Select clear static preview frame rendered when     │
│    the video preview is not playing on the store.      │
└────────────────────────────────────────────────────────┘
```

| Specification | Apple App Store Preview | Google Play Promo Video |
|---|---|---|
| **Video Duration** | **15 to 30 seconds max** | **30 to 120 seconds max** (YouTube URL) |
| **Video Format** | `.mp4` or `.mov` (H.264 Main/High Profile) | YouTube Video URL (`https://youtu.be/...`) |
| **Max Previews** | Up to **3 App Previews** per device class | 1 YouTube Promo Video link |
| **Audio Format** | AAC-LC, 2 channels, 48 kHz or 44.1 kHz | YouTube stereo audio |
| **Poster Frame** | Custom frame selected in App Store Connect | YouTube video thumbnail |

---

# 2. Apple Guideline 2.3.4 Compliance Rules

Apple Guideline 2.3.4 governs App Preview content and visual presentation:

- **Must Showcase App UI Only**: App Previews MUST exclusively capture footage of the app interface in action.
- **No Live-Action / Hands / Devices**: You MUST NOT include live-action video of people holding devices, over-the-shoulder camera shots, or physical device bezels.
- **No Un-released Features**: Video previews MUST NOT show features or content that are not present in the submitted build binary.

---

# 3. FFmpeg Command for Compliant Video Transcoding

Use FFmpeg to transcode recorded screen captures into store-compliant App Previews:

```bash
# Transcode video capture to 30fps H.264 AAC-LC for App Store Connect
ffmpeg -i raw_input.mov -c:v libx264 -profile:v high -level:v 4.0 -r 30 -pix_fmt yuv420p -c:a aac -ar 48000 -b:a 256k -movflags +faststart app_preview_output.mp4
```

---

# 4. Poster Frame Selection Best Practices

App Store Connect requires selecting a static **Poster Frame** from the uploaded video preview:

- **Static Representative Image**: The poster frame displays on the App Store product page whenever auto-play is disabled or before video playback begins.
- **Clear Value Proposition**: Select a frame displaying a clean UI view with a clear headline.

---

# 5. Operational Verification Checklist

- [ ] **Duration Verified**: Video length is strictly between 15 and 30 seconds.
- [ ] **Frame Rate 30fps**: Transcoded video uses constant 30 fps frame rate.
- [ ] **App UI Only**: Video contains 100% captured app interface footage; zero hands or device bezels.
- [ ] **Poster Frame Selected**: Clear poster frame selected in App Store Connect for static preview.
- [ ] **Audio Normalized**: Background audio/voiceover normalized without clipping.
- [ ] **YouTube URL Configured**: Public YouTube video URL configured in Google Play Console.

---

# 6. Related Documentation

- [Screenshots Handbook](screenshots.md) - Screenshot specifications.
- [Metadata Handbook](metadata.md) - Text metadata.
- [App Review Handbook](app-review.md) - Review submission.

---

# 7. Official Sources

- Apple App Preview Guidelines: https://developer.apple.com/app-store/app-previews/
- Google Play Video Guidance: https://support.google.com/googleplay/android-developer/answer/9866151

---

**Last verified:** August 14, 2026
