# Qur'an Companion — QR Audio Website

A mobile-first website for a printed Qur'an. Every printed-page QR code opens the matching online page; readers can select verses, listen, adjust playback speed, repeat audio, and show English translations. Includes a high-contrast mode, adjustable Arabic font size, and two illustrative sample surahs.

**Audio is intentionally NOT included.** You supply your own recordings, with appropriate permissions.

## Start locally

1. Unzip this project.
2. In a terminal inside `quran-qr-audio`, run:

   ```bash
   python -m http.server 8000
   ```
3. Open `http://localhost:8000` in your browser.

> Opening `index.html` by double-clicking it won't work in many browsers because the website fetches `data/pages.json`. Use a web server.

## Add your recitations

Place your audio files in the `audio/` folder, e.g.:

```
audio/
  fatiha-full.mp3
  fatiha-01.mp3
  fatiha-02.mp3
  ...
  ikhlas-full.mp3
```

Open `data/pages.json`. Every page has an optional `audio` property for the whole-page recitation. Every verse has its own `audio` property. Update them like this:

```json
{
  "id": "fatiha-1",
  "audio": "audio/fatiha-full.mp3",
  "verses": [
    {
      "number": 1,
      "arabic": "...",
      "translation": "...",
      "audio": "audio/fatiha-01.mp3"
    }
  ]
}
```

**Do not replace the full file with the abbreviated JSON example above.** Just edit the `audio` values already present in the existing JSON.

- If you provide only a page audio file, the website plays the whole page but cannot accurately follow individual verses or jump to individual verses.
- If you supply **individual verse files**, readers can play and repeat each verse. When pressing Play at the page level without a full-page track, the website plays all available verse files in sequence.
- For best learning support, supply **both** the page track and each verse track.
- Accepted formats depend on the browser; MP3 is widely supported.
- Audio speed changes the browser's playback rate. Pitch preservation depends on browser support.
- If a verse has no audio, the interface will say that it has not been added. This is intentional.

## Add additional pages or surahs

Duplicate a `pages` entry in `data/pages.json`, with a **unique** `id` and a matching `page` number. Enter the verified Arabic, surah name, verse numbers, optional `opening` and English translation. Each page can contain part of a surah or multiple verses. Make sure the split exactly corresponds to your printed edition.

For a complete Qur'an, map each QR to the **specific printed page of your own edition**. Do not assume that an external standard page number uses the same verse boundaries as your print layout.

## Generate scannable QR codes for print

After uploading the website to a permanent URL (for example `https://quran.yourdomain.org`), run:

```bash
pip install 'qrcode[pil]'
python tools/generate_qr.py https://quran.yourdomain.org
```

The generated `qr-codes/` directory contains SVG files (preferred for print) and PNG files for every page, pointing to URLs such as:

`https://quran.yourdomain.org/?page=fatiha-1`

The QR images are **real QR codes** generated from the URLs. Confirm them with real phones before printing. Keep the printed codes high contrast, with a full white quiet zone; preferably at least **20–25 mm square** in the book (test at the actual paper size, angle, and lighting). Don't change the page ID or published domain after printing.

## Publishing

This is a static site. You can deploy it on any standard static hosting service that supports HTTPS and serves JSON and MP3 files (for example GitHub Pages, Netlify, or Cloudflare Pages). Upload the **contents** of this project including `data/` and `audio/`.

If hosted in a *subfolder*, URL generation needs the actual base path, e.g. `https://example.org/quran/`. The `generate_qr.py` script supports this if you pass that URL as its argument.

## Editorial and accessibility checks before public release

- Sample Arabic text and translations are a starting point; arrange independent expert checking of all Qur'anic text, surah titles, verse counts, diacritics, translation choices and segmentation against your chosen approved printed edition.
- The demo contains Surah Al-Fatihah and Surah Al-Ikhlas only. It is **not** the full Qur'an.
- Confirm you hold the necessary rights for the reciter's recordings, translations, text source, fonts and any third-party content you publish.
- Choose and document your numbering convention and treatment of Bismillah. In the example, Al-Fatihah's opening Bismillah is counted as verse 1, while Al-Ikhlas includes it only as a separate opening line.
- Test with older people, children, people with reduced vision, screen readers, and readers who struggle with Arabic.
- For timed, word-by-word highlighting, you will need timestamp-aligned audio data and additional development; this pilot highlights the **selected verse**, not words in real-time.
- To make the website fully offline, self-host the web fonts and audio and build a service worker; this pilot requires a connection.

## Project structure

- `index.html` — page layout
- `styles.css` — design and mobile styles
- `app.js` — audio playback, selection, reader modes and saved settings
- `data/pages.json` — content and audio file mapping
- `audio/` — put your recordings here
- `tools/generate_qr.py` — make printable QR codes
