# Tasveer — send a photo by SMS, no internet needed

**Tasveer** (तस्वीर, "picture") turns a photo into plain SMS text. The other person pastes that text into Tasveer and gets the photo back. Neither phone needs mobile data or Wi‑Fi; only normal SMS is used.

**Live demo:** https://image-through-text.vercel.app/
**Works offline:** download `index.html` once and open it in Chrome. It doesn't need a server, an install or any dependencies.

<!-- Add a screenshot or GIF here: ![Tasveer demo](docs/demo.gif) -->
### Note: I have used claude to code this so there might be some bugs.
---

## Why
 
In many places there is phone signal but no usable data: hill areas, villages, during disasters, or on a phone without a data pack. SMS still works there. Tasveer squeezes a picture into as few SMS as possible.
 
### The story behind it
 
I'm from Jammu & Kashmir. There were long stretches when we lived for months without mobile internet. We could still make calls and, at times, send SMS, but we couldn't share a single photo with family.
 
The obvious answer is "just send an MMS". It didn't work. MMS looks like a text message, but under the hood it travels over mobile data, so when data is switched off, MMS goes down with it. Plain SMS uses a different channel and keeps working.
 
That's where the idea came from. If only SMS gets through, put the picture inside the SMS.
 
**When Tasveer helps**
- **Internet shutdowns:** sharing a photo of a document, a prescription, an injury or a family member when only SMS works.
- **Disasters and network congestion:** after floods or earthquakes, data often fails first and short messages still get through.
- **Remote areas:** hills, borders and villages with 2G signal but no usable data.
- **No data pack or a basic phone plan:** sending a photo without paying for data.

## Features

- **1 to 99 SMS.** More SMS give a bigger, sharper picture.
- **Live preview.** The sender sees exactly what the receiver will get before sending.
- **Crop and zoom** with drag, pinch or the zoom slider, plus shape presets (1:1, 4:3, 16:9 and more).
- **Colour or black & white.** B&W puts all the data into detail.
- **Any order.** Every SMS starts with its number (01, 02, …), so parts can arrive and be pasted in any order.
- **Partial pictures.** The image appears from SMS 01 and gets sharper with every part that follows. Missing parts are listed by number.
- **Error checks.** Every SMS carries its own CRC‑8 checksum, so changed or damaged parts are detected.
- **Plain SMS characters only.** Only `A–Z a–z 0–9 ! # % ( ) * + , - . / : ; = ?` are used, so no SMS is ever forced into Unicode mode, which holds only 70 characters per SMS.
- **Single file, fully offline.** No tracking and no uploads: the photo never leaves the phone.

## Picture size per SMS count

These sizes are for a 3:2 photo; the picture is enlarged on the receiving phone.

| SMS | Characters | Picture size |
|---:|---:|---:|
| 1 | 160 | 96 × 64 |
| 3 | 459 | 144 × 96 |
| 10 | 1,530 | 288 × 192 |
| 20 | 3,060 | 384 × 256 |
| 40 | 6,120 | 576 × 384 |
| 99 | 15,147 | 768 × 512 |

## How to use

**Sending**
1. Open Tasveer and tap **Choose photo**.
2. Crop close to what matters, choose the number of SMS, and choose colour or B&W.
3. Tap **Copy** (or **Open in Messages**) and send it as a normal SMS. Long pictures are split into several messages; send each one.

**Receiving**
1. Open Tasveer and go to **Open a received message**.
2. Paste each message as it arrives, in any order.
3. The picture builds up as parts arrive. Tap **Save picture** to keep it.

> Both phones need Tasveer, either the website or the downloaded file.

## How it works

```
photo → crop & resize → YCbCr colour → CDF 9/7 wavelet → SPIHT bit-planes
      → adaptive arithmetic coding → base-77 text → numbered 153-char SMS blocks
```

- **Resize to the budget.** The picture is scaled to about 5 pixels per bit of SMS capacity, so every extra SMS buys real resolution.
- **Wavelet transform (CDF 9/7).** This is the same transform JPEG 2000 uses. It packs most of the image into a few large numbers and degrades into blur rather than blocks.
- **SPIHT coding.** The most important detail is sent first. The stream is *embedded*: any first part of it already decodes to a blurrier version of the picture. That's what makes partial pictures possible.
- **Arithmetic coding** uses context models with trained starting probabilities, so no bits are wasted early on.
- **Fitting the SMS limit.** The coder knows the exact cost of stopping after every bit, so it fills the SMS budget to the last bit.
- **Base-77 text.** Four characters carry 25 bits. All 77 characters sit at the same code point in GSM‑7 (the SMS alphabet) and ASCII, so each one costs exactly one SMS character slot.
- **SMS blocks.** A single SMS is 160 characters. Longer messages use 153-character blocks, the size of one part of a joined SMS: a 2-digit number, then 151 characters of data with a CRC‑8 inside.
- **Identical decoding everywhere.** The decoder uses only exact integer arithmetic, so every browser decodes the same bits to the same picture.

## Run locally

Just open `index.html` in a browser; there's no build step. Or:

```bash
npx serve .
```

## Deploy on Vercel

1. Push this repo to GitHub.
2. In Vercel, choose **Add New → Project**, import the repo, and keep the framework preset as **Other**.
3. Deploy. `index.html` is served as is.

## Tips

- If your phone has an SMS **input mode** setting, keep it on GSM or Automatic.
- Some phones turn long texts into MMS, which needs data. If that happens, choose fewer SMS per message.
- Many mobile plans cap the number of SMS per day.

## Roadmap

- [ ] Android app that reads incoming Tasveer SMS automatically
- [ ] Share target ("Share → Tasveer" from the gallery)
- [ ] Spend more bits on faces and fewer on the background
- [ ] On-device AI upscaling for the received picture
- [ ] Optional password encryption
- [ ] Voice notes and locations over SMS

## Tech

Vanilla JavaScript, HTML and CSS, in one file with no libraries. Fonts (Rozha One, Mukta, IBM Plex Mono, SIL Open Font License 1.1) are embedded so the page looks the same offline.

## Author

**Himanshu Dutt** · [GitHub @MyLordHitsHard](https://github.com/MyLordHitsHard)

## License

MIT
