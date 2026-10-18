# Step Up!

Step Up! is a phone and laptop web app that tells you whether your salsa steps land on the beat. It plays the music, watches you dance through the camera, and afterwards gives you a score out of 100 and one thing to fix next time.

**Try it:** https://luke-ms-devlin.github.io/salsa-sync/

Everything runs on your own device. The pose model runs in the browser, and the video never leaves your phone or laptop.

Built for the [Hacktoberfest 2026 DEV Open-Source AI Challenge](https://dev.to/challenges/hacktoberfest-week2-2026-10-12) (Week 2, "Side Hustle").

## Who it's for

People learning salsa who practise at home between classes. In class a teacher can tell you you're off the beat. At home, on your own, you can't tell, and it's easy to practise the same mistake for weeks. Step Up! is the teacher's ear for those sessions.

## How to use it

1. **Set up.** Prop your phone or laptop 2.5–3m away, low down and tilted up, with your whole body (head to feet) in frame. Turn the main light on and the volume up.
2. **Turn the camera on.** Tap the button at the bottom of the camera screen. That's the last time you need to touch it.
3. **Open the hands-up GUI.** Stand where the camera can see your head and both hands, and raise both hands above your head. After a 2, 1 countdown the menu opens. Your wrists show as yellow rings: hold one over a button for 1.5s and it fills up green, then presses. The menu closes by itself after 20s with no hand on a button.
4. **Sync.** On a new device, choose **Full sync** on the hands-up GUI before your first session (it has a yellow border and says "do this first" until you have). It runs Motion sync, then Audio sync. **Motion sync** also runs by itself the first time after you turn the camera on: stand still for 3s. The app learns how much the tracked points wobble when you're not moving, and sets how high a foot has to lift to count as a step.
5. **Dance.** You get a big 3, 2, 1, a "5, 6, 7, 8" count-in and two practice basics. A second, higher "5, 6, 7, 8" means the recorded part starts on the next 1. Raise both hands for 2s at any time to stop.
6. **Read your results.** The camera screen shows your score and one tip. The Results tab has the details and a full analysis. The History tab keeps every session on this device, with a "Your score over time" chart: tap a dot to open that session.

On a phone, swipe left and right between the camera, Results, History, Settings and Help. On a laptop the menus sit beside the camera. Settings has three cards: **Session** (music, tempo, length), **Sound** (voice, Audio sync, delay) and **Camera** (Motion sync, lift needed). Help has step-by-step instructions for the hands-up GUI and the salsa basic drawn as footprints for each count.

**Audio sync** is part of Full sync. Run it again on its own (in Settings or the hands-up GUI) whenever you switch headphones or speakers. Bluetooth plays the music late, which would make every step look late. You march on the spot to 16 clicks and the app measures the delay.

### Music

- **Beat only:** cowbell, 2-3 son clave and conga.
- **Salsa band:** the same rhythm plus piano and bass, generated in the browser.
- **Songs:** three real recordings with tracked beat times (see [Credits](#credits)).

Tempo can be 140, 160, 180 or 200 BPM, and a session can be 20s, 30s or 60s.

## What the results mean

The salsa basic is six steps over eight counts: step on 1, 2, 3, pause on 4, step on 5, 6, 7, pause on 8. Step Up! checks the "On1" style, where your first step lands on the 1.

The score is out of 100, made of four parts worth 25 each:

| Part | What it checks |
|---|---|
| **Tempo** | Is your basic the same speed as the music? |
| **Pause length** | Do you hold for 2 beats on the 4 and the 8? |
| **Timing** | Does your basic start on the 1, or a beat early or late? |
| **On the beat** | Does each step land within 80ms of a beat? The tile says On the beat, Dragging (late), Rushing (early) or Uneven. |

All four come from **your typical basic**, which is the average position of each of the six steps across every basic you danced. The words, points and colour of each part all come from that one basic, so they never disagree. The tip names the first part that needs work, in the order above.

### Full analysis

"See full analysis" under the summary goes through the same four parts in the same order, with one box each. Every box ends with one thing to do.

- **Tempo:** a graph of every basic against the music's basic time, plus a half-basic graph comparing your 123 and your 567 against a green band. A dancer can be even overall but rush one half.
- **Pause length:** fill-the-box bars for the pause on the 4th beat and on the 8th beat, shown separately so a short pause and a long one can't average out to "just right".
- **Timing:** one row per basic, with dashed circles where your steps should land, labelled On 1, late, early, or ? for missing or extra steps.
- **On the beat:** what share of your steps were on the beat, too early and too late.

## How it works

1. **Rhythm.** The Web Audio API schedules every drum hit and note ahead of time, so the beat is exact. The app records the time of every beat.
2. **Pose tracking.** Google's [MediaPipe Pose Landmarker](https://ai.google.dev/edge/mediapipe/solutions/vision/pose_landmarker) (the `pose_landmarker_lite` model, run with MediaPipe Tasks Vision 0.10.35 on the GPU) finds 33 body points in each camera frame. Step Up! uses the hips, knees and ankles.
3. **One clock.** Camera frames are timed with `requestVideoFrameCallback`, and beats are converted to the same clock with `AudioContext.getOutputTimestamp()`. Without this the beat and the video can be off by tens of milliseconds, which is about the size of the mistakes being measured.
4. **Finding steps.** For each foot, the app measures how high the ankle is above where it rests on the floor, divided by leg length (so distance from the camera doesn't matter). A foot counts as lifted once it rises past the "lift needed" line from the Motion sync, and the step counts when it comes back down to half its peak height, because a step counts when your weight lands, not when your foot is highest.
5. **Matching to beats.** Each landing is paired with its nearest beat, allowing for the measured audio delay. The offset (early or late, in milliseconds) and the count it landed on feed the four scores.
6. **Tempo from the whole body.** When steps are too small to time one by one, autocorrelation of the left-minus-right ankle signal finds how long your basic takes.

The whole app is one `index.html` file with no build step and no server. Sessions and settings are saved only in your browser's local storage on that device. Nothing is sent anywhere, so history doesn't move between devices and clearing your browser data deletes it. "Download session data", under For developers in the full analysis, exports any saved session as JSON (beat times and body points, never video).

### Known limits

- **Your face must be in frame.** MediaPipe finds the person from their face first, so framing just your legs doesn't work. Film low down, tilted up, head to feet.
- **Light matters.** In a dim room the webcam's frame rate can halve (16fps down to about 8fps), which makes small steps easy to miss. Turn the main light on.
- **Feet crossing front-on** can hide one foot. Filming from slightly to one side (20–30 degrees) helps.
- **Accuracy.** On a marching test the detector found 78 of 80 steps, landing a median of 6ms after the beat. Salsa is harder, because the steps on 2 and 6 are small weight changes with little lift.

## Run it yourself

It's a static page, so any local server works:

```
python3 -m http.server
```

Then open http://localhost:8000. The camera needs `localhost` or HTTPS.

## Credits

- **Body tracking:** [MediaPipe](https://github.com/google-ai-edge/mediapipe) by Google, Apache License 2.0. Step Up! uses MediaPipe Tasks Vision and the Pose Landmarker lite model, loaded from Google's CDN.
- **Songs** from the [Free Music Archive](https://freemusicarchive.org/), each under its own Creative Commons licence, not the MIT licence:
  - "Cupido" and "Vamos A Tocar Sonero" by Frank Guerrero y Su Grupo Aché ([CC BY-NC-ND 3.0](https://creativecommons.org/licenses/by-nc-nd/3.0/)), played unchanged at their own tempo.
  - "La Mujer de Antonio" by SONGO 21 ([CC BY-NC-SA 3.0](https://creativecommons.org/licenses/by-nc-sa/3.0/)), an excerpt sped up or slowed down for each tempo. The changed files are shared under the same licence.
  - Full details and how the beat times were found: [music/README.md](music/README.md).
- **Beat tracking** for the songs uses the dynamic-programming beat tracker from Ellis (2007), "Beat Tracking by Dynamic Programming".
- Built with help from Claude Code (Anthropic) as a coding assistant.

## Licence

The code is [MIT](LICENSE). The music in `music/` keeps its own Creative Commons licence (see above). Both song licences are non-commercial.

## Changes after the deadline

The challenge deadline was 18 October 2026, 11:59pm PDT. Any commits after that are listed here.

- None yet.
