# LocalMotion Keyboard prototype (Team 5, RBC Design Thinking Program)

Single-file interactive prototype: `LocalMotion Prototype.html`. No build step, no dependencies. Open it in Chrome, or serve it on localhost so the mic works:

```
python3 -m http.server 8000
# then open http://localhost:8000/LocalMotion%20Prototype.html
```

## What it is

A picture-assisted iPhone keyboard for adults with Broca's aphasia whose formal speech therapy has ended. Persona: Frank, 64, stroke this spring, lives alone outside London ON, texts his daughter Sarah. When he gets stuck on a word, the keyboard shows photos from his own life that match the conversation. Tapping a photo speaks the word and shows a letter hint (e.g. `W _ N`). He then says the word out loud (speech recognition checks it) or types it himself. The word is never typed for him. Words he finds feed a nightly practice in the LocalMotion app.

Pitch framing: speaking is the default way to fill a stuck word, but he never has to speak to send a text. Required speech practice happens in the nightly review. Hints fade as he improves. His SLP can see milestones if he opts in.

## Layout of the page

- Left side panel: title, 5 demo steps, "Restart with a new chat" button, and two app buttons (iMessage, LocalMotion app) that switch the phone between views.
- Phone (390x844): status bar, then one visible `.view` at a time:
  - `#v-msg`: iMessage chat with the guided keyboard (starts here)
  - `#v-onb`: LocalMotion first-time setup (3 steps)
  - `#v-app`: LocalMotion app with tabs Photos, Practice, Progress, SLP view (hidden until sharing is on), Settings
  - `#v-home`: unused home screen (kept in markup, never shown)
- `go(view)` switches views. `go('app')` routes to onboarding until setup is finished.

## Guided chat (iMessage view)

- `SCENARIOS` array: 5 chats (Sarah x3, Mike x2). Each has `msgs`, `target` (what Frank types, no punctuation), `stuck` (last word), `accept` (words the mic accepts, including common mishearings), `tiles` (`[photoKey, spokenWord, isTarget]`), and `replies`.
- Grey ghost text shows what to type. Only the correct next letter types; wrong keys shake the field. Laptop keyboard works too and lights up the on-screen keys.
- On the stuck word only the first letter is given, then the keyboard locks. After 1.2 s a "Stuck? Show pictures" pill appears. The 🖼️ key (left of space) also opens the picture bar any time.
- Phases: `typing` -> `stuck` -> `hint` -> `unlocked` -> `done` -> `sent`.
- Tapping the target photo (only once stuck) speaks the word, shows the letter hint, and unlocks typing. "Say it" starts Web Speech recognition and turns into a bracketed sound wave driven by live mic level (`startWave`, Web Audio analyser). If recognition is unavailable it fakes success after 2.6 s so the demo never stalls.
- After sending: two replies, then a "Restart demo ↻" bubble from the sender that loads the next chat. The side-panel Restart button does the same.
- Keyboard bottom row mimics iOS: `123`, 🖼️ (LocalMotion pictures), `space`, `return`; below it a globe icon (no-op) and mic icon (same as Say it).

## LocalMotion app (ported from the team's earlier prototype)

- State object is `A` (renamed from `S` to avoid clashing with the chat's `S` = current scenario). Created by `fresh()`.
- `A.bank`: words with photo keys and categories (People, Places, Things). Words found in chat (`finishWord`) are pushed to `A.reached` / `A.foundToday`, so they show up in Practice.
- Photos tab (rename, add photo, "Name this photo" game), Practice (say it, then build it from letter tiles), Progress (stats, weekly bars, milestones), SLP view (summary only), Settings (SLP sharing, help level, large text, audio first, tap sounds, lite mode for older phones).

## Photos

All picture tiles are real photos from Wikimedia Commons, hotlinked via `upload.wikimedia.org` 330px thumbnails (`PHOTO` map, wrapped in an SVG `<image>` so the app's sizing code keeps working). No drawings or clip art anywhere. Sarah is shown as an initial "S" contact circle and Liam as his hockey helmet, so no photos of real people. Photos need internet; the Claude preview blocks them. For offline use: Chrome > File > Save Page As > Webpage, Complete. The Commons licences (CC BY / CC BY-SA / public domain) require crediting the photographers somewhere, e.g. on the final pitch slide.

## Conventions

- Keep it one self-contained HTML file.
- Code comments: lowercase, no space after the marker (`//like this`).
- No em dashes in UI copy. No punctuation in the typed target sentences.
- Don't let the keyboard type the stuck word for Frank; hints only.
