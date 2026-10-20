# sample loader · for volca sample 2

A browser app to record, prepare and send samples to the **KORG volca sample 2** over USB, and to manage, back up and restore its 200 sample slots. It replaces the fiddly official tools with one page that works in Chrome or Edge.

<!-- Add a screenshot here, e.g. ![sample loader](screenshot.png) -->

## Features

### Sample

- **Record** from a microphone or line input (with level meter, up to 60 s), or **open** WAV, MP3, OGG, FLAC, AIFF or M4A files, or drop a file on the waveform.
- **Trim** by dragging on the waveform or with the Start Point and Length knobs. **Auto trim** removes silence at both ends.
- **Shape the sound** before sending: Gain, Pitch (±24 semitones), Hi Cut, Fade in, Fade out, Normalise, Reverse and the choice of stereo-to-mono mix.
- **Preview** plays the sample exactly as it will sound on the volca: mono, 31.25 kHz, 16-bit. The final length and size are shown before you send.
- **Send** to any slot, or **Save WAV** in the volca's own format.

### Library

- Shows all 200 slots with name and length, and how much memory is used.
- For each slot: listen, load into the editor, save as WAV, delete, or choose it as the destination for the next sample.

### Backup

- **Back up all samples** into one `.zip` file with names and slot numbers.
- **Restore a backup** into the original slots, optionally erasing other slots first to free memory.
- **Load many files** at once into the next free slots.

### Playing

- Pads 1–10 play the volca's parts over MIDI, so you can hear a sample right after sending it.
- The other six keys: Preview, Stop, Rec, previous/next slot and Send.
- Keyboard shortcuts: **Space** to preview or stop, **R** to record.

Every transfer shows a progress card with a running LED chase and stays up for at least two seconds, so it's always clear when something is happening.

## Requirements

- **Chrome or Edge on a computer**, or Chrome on Android with a USB OTG cable. The app uses Web MIDI with SysEx, which Safari, iPhone and iPad don't support.
- A KORG volca sample 2 connected with its micro-USB cable.

## Getting started

1. Open `sample-loader.html` in Chrome or Edge, or host it on GitHub Pages.
2. Connect the volca by USB, press **Connect** and allow MIDI with SysEx when asked.
3. Go to **Backup** and press **Back up all samples** before changing anything.
4. Record or open a short sound, trim it, press **Preview**, then **Send to volca**.
5. Play it with the pads. If nothing sounds, set the part 1 MIDI channel in **Setup**.

## How it works

The volca sample 2 appears as a USB MIDI device. Samples travel as KORG SysEx messages, with 8-bit data packed into 7-bit bytes (every 7 bytes become 8). KORG has not published this protocol; it was worked out by the open-source [volsa2](https://github.com/00nktk/volsa2) project (MIT licence) and reimplemented here in JavaScript.

| Message | Purpose |
| --- | --- |
| Search device (`50 00`) | Finds the volca, its firmware version and global channel |
| Sample space (`1B` / `4B`) | Used and total memory |
| Sample header (`1E` / `4E`) | Name, length, level and speed of a slot; an empty header erases it |
| Sample data (`1F` / `4F`) | 16-bit mono audio at 31.25 kHz |
| Status | Acknowledge, busy, memory full or invalid data |

The encoder and decoder were checked against fourteen real data dumps captured from the device: decoding gives the expected audio, and re-encoding gives identical bytes.

All audio processing (resampling to 31.25 kHz, filtering, fades, normalising) happens in the browser with the Web Audio API. Nothing is uploaded anywhere.

## Backup format

The backup is a `.zip` with one WAV per sample (`042 Kick.wav`, mono, 31.25 kHz, 16-bit) and a `manifest.json` listing slot, name, length, level and speed. You can open the WAVs in any audio editor.

## Known limitations

- **Long transfers.** volsa2 notes that the volca can hang on very long messages and sends them in small chunks with pauses. Web MIDI only allows complete SysEx messages, so the browser sends each sample in one go. Start with short samples. If the volca stops responding, switch it off and on: its memory stays intact.
- **Part channels.** The pads assume part 1 listens on the channel set in Setup, part 2 on the next, and so on.
- **Memory.** The volca reports memory in sectors, so the app shows the percentage used rather than seconds left.
- **No iOS support**, because Safari has no Web MIDI.

## Files

A single self-contained HTML file. Fonts (Archivo, Archivo Narrow, Share Tech Mono) load from Google Fonts, and [JSZip](https://stuk.github.io/jszip/) for backups loads from cdnjs. Without internet the page falls back to system fonts and backups are saved as separate WAV files instead of a zip.

## Status

Tested against real captured data and a simulated device. Reports from real hardware are very welcome, especially from Windows and macOS.

## Credits

- Protocol knowledge: [volsa2](https://github.com/00nktk/volsa2) by 00nktk (MIT).
- Backup archives: [JSZip](https://stuk.github.io/jszip/).

## Support

If this tool saves you time, you can [buy me a coffee on Ko-fi](https://ko-fi.com/dferreiramarques).

## Disclaimer

Not affiliated with or endorsed by KORG. volca is a trademark of KORG Inc. Use at your own risk and keep backups of your samples.

## License

<!-- Choose a license, e.g. MIT, and add a LICENSE file. The protocol port is based on volsa2 (MIT), so keep its copyright notice if you reuse that code. -->
