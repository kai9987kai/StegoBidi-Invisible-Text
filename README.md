# StegoBidi Advanced Invisible Text Tool

**StegoBidi Advanced** is a single-file, browser-based invisible text encoder, decoder, inspector, and sanitizer. It hides UTF-8 messages inside zero-width Unicode characters, optionally embeds them into normal cover text, and provides forensic tools for revealing and cleaning hidden Unicode controls.

This project is designed for learning, controlled testing, watermark experiments, Unicode inspection, and defensive analysis of suspicious text.

---

## Features

### Core Encoder / Decoder

* Encode visible messages into invisible zero-width characters
* Decode hidden StegoBidi payloads back into readable text
* Preserve the original simple workflow:

  * `U+200B` Zero-Width Space = bit `0`
  * `U+200C` Zero-Width Non-Joiner = bit `1`
* Optional cover text support so hidden messages can be embedded into normal-looking text
* Full UTF-8 support through `TextEncoder` and `TextDecoder`

### Advanced Packet System

StegoBidi now supports multiple packet formats:

| Mode     | Description                                                    |
| -------- | -------------------------------------------------------------- |
| Advanced | Adds magic header, version, payload length, and CRC32 checksum |
| Legacy   | Original 32-bit length-prefixed format                         |
| Raw      | Converts raw bytes directly into invisible bits                |

The **Advanced** packet mode helps detect corrupted, incomplete, or mismatched payloads before displaying the decoded result.

### Cover Text Embedding Modes

Choose how invisible data is inserted into visible cover text:

| Mode   | Description                                         |
| ------ | --------------------------------------------------- |
| Spread | Distributes hidden characters across the cover text |
| Append | Places all hidden data after the cover text         |
| Prefix | Places all hidden data before the cover text        |
| Word   | Distributes hidden data after words                 |

A density option allows the hidden payload to be spread with low, balanced, or high density.

### Invisible Character Inspector

The reveal panel highlights hidden Unicode characters so encoded text can be inspected visually.

Detected characters include:

* `U+200B` Zero-Width Space
* `U+200C` Zero-Width Non-Joiner
* `U+200D` Zero-Width Joiner
* `U+2060` Word Joiner
* `U+FEFF` Byte Order Mark / Zero-Width No-Break Space
* `U+061C` Arabic Letter Mark
* `U+200E` Left-to-Right Mark
* `U+200F` Right-to-Left Mark
* `U+202A` to `U+202E` Bidi embedding and override characters
* `U+2066` to `U+2069` Bidi isolate characters

### Sanitizer

The **Clean/Sanitize** action removes supported invisible and bidirectional control characters from text. This is useful when checking copied text, code snippets, usernames, messages, or documents for hidden Unicode content.

### Stats Dashboard

The interface shows live stats for:

* Visible character count
* Invisible character count
* UTF-8 byte count
* Payload bit count
* Cover text load percentage
* Checksum status

### Import, Export, and Drafts

* Import `.txt` files with the file picker
* Drag and drop text files into the tool
* Copy output to the clipboard
* Download output as a `.txt` file
* Save and load local drafts with `localStorage`
* Toggle light and dark themes

### Keyboard Shortcuts

| Shortcut   | Action                      |
| ---------- | --------------------------- |
| `Ctrl + E` | Encode                      |
| `Ctrl + D` | Decode                      |
| `Ctrl + R` | Reveal invisible characters |
| `Ctrl + S` | Save local draft            |

On macOS, use `Cmd` instead of `Ctrl`.

---

## Demo Workflow

### Encode a Message

1. Open the HTML file in a browser.
2. Type a secret message into the **Message / stego text** box.
3. Optionally add normal visible text to the **Cover text** box.
4. Choose a packet mode.
5. Choose an embedding mode.
6. Click **Encode**.
7. Copy or download the generated output.

If no cover text is provided, the result may look blank because it contains only invisible characters.

### Decode a Message

1. Paste encoded StegoBidi text into the **Message / stego text** box.
2. Select the correct packet mode.
3. Click **Decode**.
4. The decoded message appears in the output box.

For older encoded text, use **Legacy** mode.

### Reveal Hidden Characters

1. Paste text into the input box.
2. Click **Reveal Invisibles**.
3. The reveal panel will display hidden characters as labelled visual tokens.

### Clean Suspicious Text

1. Paste suspicious text into the input box.
2. Click **Clean/Sanitize**.
3. The cleaned text appears in the output box.

---

## Packet Format

### Advanced Packet Mode

Advanced mode stores data using the following structure:

```text
MAGIC     4 bytes   "SBD1"
VERSION   1 byte    Current version number
LENGTH    4 bytes   Payload byte length, big-endian
CRC32     4 bytes   CRC32 checksum of payload
PAYLOAD   n bytes   UTF-8 encoded message
```

The full byte packet is converted into binary, then each bit is mapped to a zero-width Unicode character.

```text
0 -> U+200B Zero-Width Space
1 -> U+200C Zero-Width Non-Joiner
```

### Legacy Packet Mode

Legacy mode keeps the original format:

```text
LENGTH    4 bytes   Payload byte length, big-endian
PAYLOAD   n bytes   UTF-8 encoded message
```

### Raw Mode

Raw mode directly converts UTF-8 bytes into zero-width bits without storing length, version, or checksum metadata.

---

## Why This Exists

Invisible Unicode characters are commonly used for:

* Watermarking text
* Encoding metadata
* Formatting language-specific text
* Testing Unicode handling
* Demonstrating steganography concepts
* Detecting hidden or suspicious text mutations

This tool combines both offensive-style encoding knowledge and defensive inspection features so developers can understand how hidden Unicode text works and how to detect it.

---

## Security and Safety Notes

Invisible text can be confusing or misleading when used in copied messages, source code, filenames, usernames, prompts, or logs. Use this project responsibly and only on systems, accounts, files, and text you own or have permission to test.

Recommended defensive uses:

* Inspect suspicious copied text
* Clean hidden Unicode from code snippets
* Test how your app handles zero-width characters
* Build Unicode-aware validation systems
* Demonstrate steganography in a controlled lab

This tool runs entirely in the browser. It does not upload or transmit text.

---

## Browser Support

StegoBidi uses standard browser APIs:

* `TextEncoder`
* `TextDecoder`
* `Blob`
* `FileReader`
* `localStorage`
* Clipboard API, with fallback support

It should work in modern versions of Chrome, Edge, Firefox, Safari, and other Chromium-based browsers.

---

## Installation

No build step is required.

Clone or download the repository, then open the HTML file directly in your browser:

```bash
git clone https://github.com/kai9987kai/StegoBidi.git
cd StegoBidi
```

Then open:

```text
index.html
```

You can also serve it locally:

```bash
python -m http.server 8000
```

Then visit:

```text
http://localhost:8000
```

---

## Suggested Project Structure

```text
StegoBidi/
├── index.html
├── README.md
├── LICENSE
└── screenshots/
    ├── encode-view.png
    ├── decode-view.png
    └── reveal-view.png
```

---

## Customization Ideas

Possible future improvements:

* Password-based encryption before encoding
* AES-GCM encrypted packet mode
* Multiple invisible character alphabets
* Error-correcting codes for damaged copied text
* QR export for encoded payloads
* Browser extension version
* Batch scanner for pasted documents
* Unicode risk score for suspicious text
* Diff view comparing original and sanitized text
* Optional Web Worker for large payloads
* Import/export JSON test cases
* Automated test suite for packet compatibility

---

## Development Notes

The application is intentionally written as a single HTML file so it can be opened, shared, audited, and modified easily.

Main logic areas:

| Area            | Purpose                                                       |
| --------------- | ------------------------------------------------------------- |
| Packet creation | Converts messages into length/checksum protected byte packets |
| Bit mapping     | Converts packet bits into zero-width Unicode characters       |
| Embedding       | Inserts hidden text into cover text                           |
| Decoding        | Extracts zero-width characters and rebuilds the payload       |
| Reveal mode     | Visualizes invisible Unicode characters                       |
| Sanitizer       | Removes hidden Unicode controls                               |
| Stats           | Calculates payload size, invisible count, and cover density   |
| Local drafts    | Saves and restores work using browser storage                 |

---

## Testing Checklist

Use this checklist before publishing changes:

* Encode and decode plain ASCII text
* Encode and decode emoji text
* Encode and decode multiline text
* Encode with empty cover text
* Encode with long cover text
* Decode in Advanced mode
* Decode in Legacy mode
* Confirm checksum validation works
* Reveal invisible characters correctly
* Clean text and verify hidden characters are removed
* Copy output and decode after pasting
* Download output and re-import it
* Save and load a local draft
* Test light and dark theme switching

---

## Example Messages

Try encoding:

```text
Hello from StegoBidi.
```

With cover text:

```text
This sentence looks normal, but it may contain hidden data.
```

Then click **Reveal Invisibles** to inspect where the payload was inserted.

---

## Limitations

* Some platforms may strip or normalize zero-width characters.
* Messaging apps, email clients, and document editors may modify copied text.
* Large payloads can produce very large invisible output.
* Raw mode has no length or checksum validation.
* Legacy mode does not include integrity checking.
* CRC32 detects accidental corruption but is not encryption or authentication.

For confidential messages, add encryption before converting text into invisible characters.

---

## License

Add your preferred license. For open-source browser demos, the MIT License is a common choice.

Example:

```text
MIT License
Copyright (c) 2026 kai9987kai
```

---

## Disclaimer

This project is for education, research, defensive testing, and authorized experiments. Do not use it to deceive people, bypass moderation, hide harmful content, or manipulate systems you do not own or have permission to test.

---

## Author

Created by **kai9987kai**.

GitHub: `https://github.com/kai9987kai`
