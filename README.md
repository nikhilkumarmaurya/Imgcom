# 📷 Offline Image Compressor

A **100% offline, single-file** image compression web app — no server, no external libraries, no internet required. Built entirely in vanilla HTML, CSS, and JavaScript, with a custom-built ZIP encoder for downloading multiple compressed images at once.

Designed mobile-first with an Android WebView bridge (`AndroidInterface`) so it can be dropped straight into a native Android wrapper app for direct file-system saving.

---

## ✨ Key Features

- **Fully Offline** — Works with zero internet connection. No CDNs, no APIs, no third-party JS libraries.
- **Batch Image Compression** — Select or drag-and-drop multiple images at once; they're processed in parallel batches of 3 for speed.
- **Smart Format Handling** — PNG files are automatically converted to WebP (to preserve transparency while still compressing well), other formats are compressed as JPEG.
- **Adjustable Compression Level** — Control quality from Max Compression to Max Quality via a slider.
- **Auto Renaming Engine** — Automatically renames files using a custom prefix + sequential number (e.g. `IMG_001_20250712.jpg`), detecting 8-digit date patterns from original camera filenames.
- **Custom ZIP Builder (MiniZip)** — A hand-written ZIP file generator (with CRC32 checksum calculation) built from scratch — no JSZip or any external dependency.
- **Live Session Statistics** — Track total original size, compressed size, and total space saved in real time.
- **Image Preview Modal** — Tap any thumbnail to view a full-screen preview.
- **Drag & Drop Support** — Drop image files directly onto the page.
- **Persistent Settings** — Prefix, start number, and compression level are saved locally using `localStorage` and auto-restored on reload.
- **Android WebView Ready** — Includes hooks (`AndroidInterface.saveToDevice`, `AndroidInterface.requestFolderSelection`) for native file saving and folder picking when wrapped inside an Android app.
- **Responsive Dark UI** — Clean, modern dark-themed interface optimized for mobile screens.

---

## 🧭 UI Guide — Button & Feature Breakdown

### Top App Bar
| Element | Function |
|---|---|
| 🔄 **Refresh Button** | Clears all processed images, resets statistics, and reloads the empty state. Asks for confirmation before clearing. |
| **STATS Button** | Opens the Session Statistics panel showing total original size, compressed size, and total space saved. |
| ⚙️ **Settings Button** | Opens the Settings panel to configure save location, compression quality, filename prefix, and start number. |

### Main Screen
| Element | Function |
|---|---|
| **Empty State (📷)** | Shown when no images are added yet. Also acts as a drag-and-drop target — dropping files here highlights the zone. |
| **Result Cards** | Each processed image gets a card showing: thumbnail (tap to preview), new filename, original filename, before → after file size comparison, and an individual **SAVE** button. |
| **+ (FAB Button)** | Floating action button (bottom-right) — opens the device file picker to select images to compress. |

### Download Controls (appear after processing)
| Element | Function |
|---|---|
| **Download ZIP** | Bundles all compressed images into a single `.zip` file (prompts for a filename) using the built-in MiniZip engine, then saves/downloads it. |
| **SAVE ALL** | Saves every compressed image individually to the device one after another (sequential save with a short delay between each). |

### Settings Panel (⚙️)
| Field | Function |
|---|---|
| **Change Folder** | (Android app only) Lets the user pick a custom save location on their device. |
| **Compression Level Slider** | Adjusts JPEG/WebP quality from 10% (max compression, smaller file) to 100% (max quality, larger file). |
| **Prefix** | Custom text prefix used when renaming compressed files (default: `IMG`). |
| **Start Number** | The starting sequence number for auto-renaming (auto-increments with each new image processed). |
| **Apply Changes** | Closes the settings sheet (all fields auto-save on change). |

### Stats Panel (STATS)
| Field | Function |
|---|---|
| **Original** | Total combined size of all original (uncompressed) images in the session. |
| **Result** | Total combined size of all compressed images. |
| **Saved** | Total bytes saved and percentage reduction achieved. |

### Image Preview Modal
Tapping any thumbnail opens a full-screen preview of the compressed image. Tap **CLOSE** or anywhere on the image to dismiss.

---

## ⚙️ How It Works (Under the Hood)

1. **File Selection** — Images are picked via the file input or dropped onto the page. Only valid image types/extensions are accepted.
2. **Queue Processing** — Files are queued and processed in batches of 3 using `Promise.all` for parallel compression.
3. **Canvas-Based Compression**
   - Each image is loaded into an `<img>` element, then drawn onto an off-screen `<canvas>`.
   - Images larger than 4096px (either dimension) are automatically downscaled.
   - `canvas.toBlob()` is used to re-encode the image at the chosen quality.
   - PNGs are converted to **WebP** (since canvas ignores quality settings for PNG) to retain transparency while still shrinking file size. All other formats are output as **JPEG**.
4. **Renaming Logic** — If the original filename contains an 8-digit date pattern (common in camera/phone filenames), the new name becomes `PREFIX_###_DATE.ext`. Otherwise, the original name is kept with the new extension.
5. **ZIP Generation** — The custom `MiniZip` class builds a valid `.zip` archive byte-by-byte (local file headers, central directory, CRC32 checksums, end-of-central-directory record) entirely in the browser — no external ZIP library needed.
6. **Saving Files**
   - Inside an Android WebView wrapper: files are base64-encoded and passed to `AndroidInterface.saveToDevice()` for native file-system saving.
   - In a regular browser: standard `<a download>` triggers are used instead.

---

## 🛠️ Tech Stack

- **HTML5** — Semantic structure, `<canvas>` for image processing, native `<input type="file">`
- **CSS3** — Custom properties (CSS variables), dark theme, responsive layout, animations
- **Vanilla JavaScript (ES6+)** — No frameworks, no build step, no dependencies
- **Custom MiniZip Engine** — Hand-rolled ZIP file format implementation with CRC32
- **Web APIs used** — `FileReader`, `Canvas API`, `Blob`, `URL.createObjectURL`, `localStorage`

---

## 📱 Android Integration (Optional)

This app is designed to run inside a WebView-based Android app. To enable native saving, expose a JavaScript interface named `AndroidInterface` with the following methods:

```java
@JavascriptInterface
public void saveToDevice(String fileName, String base64Data, String mimeType) {
    // Decode base64 and write to device storage
}

@JavascriptInterface
public void requestFolderSelection() {
    // Launch a folder picker (e.g. Storage Access Framework)
}
```

If `AndroidInterface` is not detected, the app automatically falls back to standard browser downloads — so it also works perfectly as a normal web page or PWA.

---

## 🚀 Usage

1. Download/clone this repository.
2. Open `index.html` in any modern browser (Chrome, Firefox, Edge, Safari) — or host it on any static hosting (Cloudflare Pages, GitHub Pages, Netlify, etc.).
3. Tap the **+** button or drag & drop images to start compressing.
4. Adjust settings (⚙️) as needed.
5. Download individually, as a ZIP, or save all at once.

No installation, no backend, no build tools required.

---

## 📄 License

This project is licensed under the **MIT License** — free to use, modify, and distribute, for personal or commercial projects. See the [LICENSE](LICENSE) file for full details.

---

## 🙋 Author

Built by **Nikhil Kumar Maurya** — Freelance Web Developer.
Portfolio: [nikhilkumarmaurya.pages.dev](https://nikhilkumarmaurya.pages.dev)
