# CYD Video Converter

A hybrid web-based video converter for the ESP32-2432S028R (Cheap Yellow Display). Automatically tries in-browser conversion, with fallback options for unsupported browsers.

## 🌟 Features

- **🚀 Fast In-Browser Conversion** - Uses ffmpeg.wasm for client-side conversion (when supported)
- **🎯 Smart Presets** - Portal 15fps, Portal 30fps, Billboard, and Custom settings
- **📊 Real-Time Progress** - Live progress tracking during conversion
- **💾 Privacy-First** - Videos never leave your device (browser conversion)
- **🔄 Automatic Fallback** - Provides scripts/commands if browser conversion isn't supported
- **📱 Mobile Friendly** - Works on desktop and mobile devices

## 🎬 Live Demo

Visit: [https://yourusername.github.io/cyd-video-converter/](https://yourusername.github.io/cyd-video-converter/)

## 📦 Deployment Options

### Option 1: GitHub Pages (Recommended)

**Note:** GitHub Pages doesn't support the required HTTP headers for SharedArrayBuffer, so browser conversion may not work for all users. But the fallback options always work!

1. Fork this repository
2. Go to Settings → Pages
3. Source: Deploy from branch `main`
4. Your site will be at: `https://yourusername.github.io/cyd-video-converter/`

### Option 2: Cloudflare Pages (Browser Conversion Works!)

Cloudflare Pages supports custom headers, so in-browser conversion works for all supported browsers.

1. Sign up at [Cloudflare Pages](https://pages.cloudflare.com/)
2. Connect your GitHub repo
3. Deploy with these settings:
   - Build command: (leave empty)
   - Build output directory: `/`
4. Add `_headers` file (included in this repo)
5. Deploy!

### Option 3: Netlify (Browser Conversion Works!)

1. Sign up at [Netlify](https://www.netlify.com/)
2. Connect your GitHub repo
3. Deploy with default settings
4. The included `_headers` file will enable SharedArrayBuffer

### Option 4: Self-Host

Requirements:
- Any web server (Apache, Nginx, Caddy)
- Must set these HTTP headers:
  ```
  Cross-Origin-Embedder-Policy: require-corp
  Cross-Origin-Opener-Policy: same-origin
  ```

**Nginx example:**
```nginx
location / {
    add_header Cross-Origin-Embedder-Policy "require-corp";
    add_header Cross-Origin-Opener-Policy "same-origin";
}
```

**Apache example:**
```apache
Header set Cross-Origin-Embedder-Policy "require-corp"
Header set Cross-Origin-Opener-Policy "same-origin"
```

## 🖥️ Browser Compatibility

### In-Browser Conversion Supported:
- ✅ Chrome/Edge 92+ (Desktop & Android)
- ✅ Firefox 79+ (with proper headers)
- ✅ Safari 15.2+ (macOS only)

### Fallback Mode:
- ✅ All browsers get script download options
- ✅ Copy-paste FFmpeg commands
- ✅ Works on any platform with FFmpeg installed

### Known Limitations:
- ❌ Safari iOS - No SharedArrayBuffer support (fallback works)
- ❌ Old browsers - Use fallback options

## 🎮 Usage

### In-Browser Conversion (Supported Browsers):

1. Visit the converter website
2. Select a preset (Portal 15fps, Portal 30fps, Billboard, or Custom)
3. Drag & drop your video or click to browse
4. Wait for conversion (progress bar shows status)
5. Download your MJPEG file
6. Copy to SD card `/videos/` directory

### Fallback Mode (Any Browser):

1. Select your preset
2. Download the script for your OS (Linux/Mac or Windows)
3. Run the script with your video file
4. Or copy the FFmpeg command and run manually

## ⚙️ Conversion Presets

| Preset | FPS | Quality | Best For |
|--------|-----|---------|----------|
| **Portal (15fps)** | 15 | 9 | Smooth, reliable animation |
| **Portal (30fps)** | 30 | 11 | Maximum smoothness (fast SD card) |
| **Billboard** | 10 | 5 | High-quality static/slow content |
| **Custom** | Your choice | Your choice | Fine-tune for your needs |

### Quality Parameter:
- Lower number = Better quality, larger file
- Range: 1-31
- Recommended: 5-11

### FPS Parameter:
- Higher = Smoother but larger files
- ESP32 can handle up to 30fps with good SD card
- Recommended: 15fps for reliable performance

## 📁 File Structure

```
cyd-video-converter/
├── index.html          # Main converter page
├── _headers           # HTTP headers for Cloudflare/Netlify
├── README.md          # This file
└── LICENSE            # MIT License
```

## 🔧 Customization

### Adding New Presets

Edit the `presets` object in `index.html`:

```javascript
const presets = {
    'your-preset': { 
        fps: 20, 
        quality: 8, 
        name: 'your_preset' 
    }
};
```

Then add a preset card in the HTML:

```html
<div class="preset-card" data-preset="your-preset">
    <h3>Your Preset</h3>
    <p>Description</p>
    <div class="specs">
        Quality: 8<br>
        FPS: 20<br>
        Best for: Your use case
    </div>
</div>
```

## 🐛 Troubleshooting

### "Your browser doesn't support SharedArrayBuffer"

This is expected on:
- GitHub Pages (no custom headers)
- Safari iOS
- Older browsers

**Solution:** Use the fallback options provided on the page (download scripts or copy command).

### Conversion is slow

In-browser conversion is typically 2-3x slower than native FFmpeg. For large files:
- Use the downloadable scripts instead
- Or run FFmpeg locally

### "Failed to load FFmpeg"

This can happen if:
- CDN is down (rare)
- Network issues
- Browser extensions blocking scripts

**Solution:** Use fallback options or try a different browser.

## 📊 Performance

| File Size | Browser Conversion | Native FFmpeg |
|-----------|-------------------|---------------|
| 10MB video | ~60 seconds | ~20 seconds |
| 50MB video | ~5 minutes | ~90 seconds |
| 200MB video | ~20 minutes | ~6 minutes |

*Times are approximate and vary by video codec, resolution, and hardware.*

## 🤝 Contributing

Contributions welcome! Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## 📄 License

MIT License - See LICENSE file for details.

## 🙏 Credits

- **ffmpeg.wasm** - WebAssembly port of FFmpeg
- **ESP32-2432S028R** community for the CYD platform
- Built for makers, by a maker

## 🔗 Related Projects

- [CYD Video Player](https://github.com/yourusername/cyd-video-player) - The Arduino firmware
- [ESP32-2432S028R Resources](https://github.com/witnessmenow/ESP32-Cheap-Yellow-Display) - Hardware info

## 💬 Support

- Issues: [GitHub Issues](https://github.com/yourusername/cyd-video-converter/issues)
- Discussions: [GitHub Discussions](https://github.com/yourusername/cyd-video-converter/discussions)

## 🎉 Enjoy!

Made with ❤️ for the CYD community. Happy converting!
