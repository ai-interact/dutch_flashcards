# TODO / Future work

Running list of ideas for the Dutch flashcards app. Not in priority order.

---

## Higher-quality Dutch voice (research)

The current setup uses the browser's built-in `speechSynthesis` API. Quality
is OK on Chrome (Google's online Dutch voice) and good on macOS with Xander
installed, but sounds robotic on some devices and is inconsistent across
browsers.

**Hard requirements** for any replacement:
- Must remain **near-instant** when the speaker button is pressed (no waiting
  several seconds while an LLM thinks).
- Must be **cheap or free at our usage scale** (one user pressing a button a
  few hundred times a day at most).
- API key / token is acceptable, but must be safe to expose in a static site
  (or proxied via something free like Cloudflare Workers).

**Options to evaluate, roughly best-fit first:**

1. **Pre-render every word to an MP3 once and ship with the site.**
   The vocab is fixed (~2,169 words across 42 lessons). Generate audio files
   once with a top-tier engine, commit them under `audio/lesson-N/word.mp3`,
   then play with `<audio>`. Result: zero runtime cost, zero latency,
   highest-quality voice (whichever engine we used at generation time),
   works offline once cached. Total size ~40 MB if we use ~20 KB MP3s.
   GitHub Pages handles it fine. This is probably the right answer for a
   fixed corpus.
   - Generation engines that would sound great: ElevenLabs (Dutch voices),
     Azure Neural TTS (`nl-NL-FennaNeural`, `nl-NL-MaartenNeural`), Google
     WaveNet `nl-NL-Wavenet-*`. All have free tiers big enough to batch
     2k words.

2. **Azure Speech Service (live).** Neural Dutch voices, ~500k chars/month
   free, ~$16 / 1M chars after. Very low latency. API key stored in a tiny
   Cloudflare Worker proxy (free tier) so it isn't exposed in the HTML.

3. **Google Cloud TTS (live).** Similar quality + pricing to Azure.
   1M chars/month free for standard voices, 4M for WaveNet. Same
   proxy-via-Worker pattern.

4. **ElevenLabs (live).** Best-in-class voice quality but small free tier
   (10k chars/month) and pricier paid tiers. Probably overkill if we go
   the pre-render route.

5. **Stay on browser-native** but improve the UX — e.g., have the app prompt
   the user once to install a Dutch voice if none is detected.

**Out of scope for this item:**
- Running an LLM at speech time (too slow, too expensive, wrong tool).
- Self-hosted TTS models (e.g., Coqui XTTS) — would need a server.

**Recommended next step when picking this up:** prototype option 1 with
~10 words from lesson 1 using Azure or Google free tier, listen, and
decide if the quality jump justifies the build.

---

## Other ideas (placeholder — add as they come up)

- (none yet)
