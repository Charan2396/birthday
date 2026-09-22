# birthday

A little two-part site for Anisha's birthday, **September 26**.

- **`index.html`** — the public page. Countdown, the video, and the 24 answers
  to "describe her in one word".
- **`her.html`** — the private page. Locked with the nickname. Only she gets in.

No build step, no dependencies, no framework. Same as `4C4F5645`.

---

## ⚠️ Read this first: the video must be UNLISTED, not PRIVATE

YouTube treats these as two different things, and the difference will break the site:

| Setting | Can it be embedded here? |
|---|---|
| **Unlisted** | ✅ Yes — anyone with the link, not in search, not on your channel |
| **Private** | ❌ **No** — the frame stays black for everyone, including her |

**Unlisted** is the one you want. It does exactly what you're after: nobody finds
it unless you hand them the link.

### Putting the video in

Open `index.html`, find the `CONFIG` block near the bottom, and paste the
11-character id:

```js
const CONFIG = {
  name: "Anisha",
  birthday: { year: 2026, month: 9, day: 26 },
  youtubeId: "",   // <-- here
  videoCaption: "made with a lot of love and slightly too many takes"
};
```

The id is the part after `v=` or after `youtu.be/`:

```
https://www.youtube.com/watch?v=dQw4w9WgXcQ   ->   "dQw4w9WgXcQ"
https://youtu.be/dQw4w9WgXcQ                  ->   "dQw4w9WgXcQ"
```

While `youtubeId` is empty the page shows a "the video lands here" placeholder,
so the site is safe to share before the video is ready. The video does not
autoload — it only starts downloading when someone actually presses play.

---

## The private page

`her.html` asks for a nickname and opens only for **chonks** (any capitalisation;
spaces are trimmed).

It is **actually encrypted**, not just hidden. The content is AES-256-GCM
encrypted with a key derived from the nickname (PBKDF2-SHA256, 250,000
iterations) and lives in `her.sealed.js`. Viewing the page source, or opening
`her.sealed.js` directly, gets you nothing but base64.

**What this does protect against:** someone who gets the link, someone who
right-clicks → View Source, someone poking at the repo, search engines.

**What it does not:** "chonks" is a short word. Someone determined, with a
wordlist and a GPU, could grind at it. For keeping a birthday page between the
two of you, this is the right amount of lock. Don't put anything in there you'd
be hurt by losing.

**Photos are a separate matter.** Encryption hides text. Any file in `pics/` is
publicly fetchable by URL whether or not the page is locked. That's why the
private page is text-only by default — see `pics/README.txt`.

### Editing the private page later

`her.sealed.js` is generated — never edit it by hand. The round trip:

1. Open **`tools/seal.html`** on the live site
   (`https://<you>.github.io/birthday/tools/seal.html`).
   It must be the `https://` address — opening it off your disk won't work,
   browsers only allow the crypto on a secure origin. It still runs entirely in
   your browser; nothing is uploaded.
2. **Unseal** — paste in `her.sealed.js`, type `chonks`, get your content back.
3. Edit the text.
4. **Seal** — paste the edited version back, press Seal, download or copy.
5. Replace `her.sealed.js` in the repo with the result. Commit.

`her.html` itself never needs touching.

Your working copy lives at `private-draft/her-content.html`, which is
**git-ignored on purpose** — committing it would undo the whole point. If you
lose it, step 2 above gets it back.

---

## Going live

The repo has no `main` yet, so after the first push, in
**Settings → Pages**, set the source branch to whichever branch you want to
serve (or merge this one into `main` first).

The site is then at `https://<you>.github.io/birthday/`, and the private page at
`https://<you>.github.io/birthday/her.html`.

---

## Layout

```
index.html                      public page (self-contained)
her.html                        the gate + private page styling
her.sealed.js                   encrypted letter - generated, don't hand-edit
tools/seal.html                 seal / unseal tool
pics/                           optional photos (public! see its README)
private-draft/her-content.html  plaintext letter - GIT-IGNORED
```
