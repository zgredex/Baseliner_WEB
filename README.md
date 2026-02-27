# EPUB Baseline JPEG Converter

A browser-based tool for optimizing EPUB files for e-ink readers. Converts images to baseline JPEG, fixes common EPUB issues, and supports Light Novel Mode for wide image handling.

**No installation required · Works 100% offline after loading**

<img width="1512" height="949" alt="Zrzut ekranu 2026-02-27 o 16 43 17" src="https://github.com/user-attachments/assets/d9e9ba82-285a-46df-9af7-921a26b0f104" />

## Features

### Image Conversion
- **Format conversion**: PNG, GIF, WebP, BMP → baseline JPEG
- **Quality control**: 1-95% JPEG quality with presets (Low 60%, Medium 75%, High 85%, Max 95%)
- **Smart scaling**: Automatically scales images to fit e-reader screen (default 480×800)
- **Grayscale mode**: Converts all images to grayscale for e-ink displays

### Light Novel Mode
Optimized for manga and light novels with horizontal spread images:
- Rotates landscape images 90° clockwise
- Splits into multiple pages with configurable overlap (default 15%)
- Right-to-left page order for correct reading flow
- Generates unique IDs for split elements to maintain EPUB validity

### EPUB Repairs
- **SVG cover fix**: Unwraps images from problematic SVG containers
- **SVG-wrapped images**: Detects and fixes `<svg><image xlink:href="..."/></svg>` patterns
- **Cover metadata**: Ensures `<meta name="cover" content="..."/>` exists in OPF
- **Manifest sync**: Updates image references and media-types after conversion
- **NCX identifier sync**: Synchronizes `dtb:uid` with OPF `dc:identifier`
- **EPUB OCF compliance**: Writes `mimetype` as first uncompressed entry

### Batch Processing
- Drop multiple EPUB files at once
- Individual progress tracking
- Combined statistics (total saved, conversion count)
- Download all as single ZIP file

## Usage

### Single File
1. Drag & drop an EPUB file onto the drop zone (or click to browse)
2. Adjust JPEG quality if needed
3. Enable Light Novel Mode for manga/comics
4. Enable Grayscale for e-ink optimization
5. Click **Convert to Baseline JPEG**
6. Download the converted EPUB

### Batch Mode
1. Drop multiple EPUB files at once
2. Configure quality settings (applies to all)
3. Click **Convert All**
4. Download individual files or **Download All as ZIP**

## Configuration Options

| Option | Default | Description |
|--------|---------|-------------|
| JPEG Quality | 85% | Compression level (1-95%). Lower = smaller file, higher = better quality |
| Light Novel Mode | OFF | Rotate and split wide images for vertical e-readers |
| Grayscale | ON | Convert all images to grayscale |
| Screen Width | 480px | Target e-reader screen width |
| Screen Height | 800px | Target e-reader screen height |
| Split Overlap | 15% | Overlap between split pages (Light Novel Mode) |

## Technical Details

### Image Processing Pipeline

```
1. Load image from EPUB
2. Convert to RGB (handle RGBA/LA/P modes)
3. Check dimensions vs screen size
4. If Light Novel Mode enabled AND image is landscape AND exceeds screen:
   a. Scale width to screen height (800px)
   b. Rotate 90° clockwise
   c. Split into pages (right-to-left) with overlap
5. Otherwise: scale to fit screen
6. Encode as baseline JPEG (non-progressive)
7. Update all references (XHTML, OPF, CSS, NCX)
```

### Split Algorithm (Light Novel Mode)

For a 1600×800 rotated image on 480×800 screen with 15% overlap:
- Effective width per page: 480 × (1 - 0.15) = 408px
- Number of parts: ceil((1600 - 480×0.15) / 408) = 4 parts
- Extraction order: right → left (maintains reading flow after rotation)

### EPUB Modifications

| File Type | Modifications |
|-----------|---------------|
| Images | Convert format, scale, rotate, split |
| XHTML | Update `<img>` src, duplicate elements for splits, fix IDs |
| OPF | Update manifest hrefs, media-types, add split image entries, ensure cover meta |
| NCX | Sync `dtb:uid` with OPF identifier |
| CSS | Update image references |

### Validation

Pre-conversion checks:
- ✓ Valid ZIP structure
- ✓ `mimetype` file present
- ✓ OPF file exists
- ✓ Images found for conversion

Post-conversion checks:
- ✓ All images successfully converted
- ✓ Manifest references valid
- ✓ `mimetype` is first entry (OCF compliance)

## Browser Compatibility

| Browser | Status |
|---------|--------|
| Chrome 90+ | ✅ Full support |
| Firefox 88+ | ✅ Full support |
| Safari 14+ | ✅ Full support |
| Edge 90+ | ✅ Full support |

**Requirements**: Modern browser with ES2020 support, Canvas API, File API

## File Size Estimates

Typical compression results (85% quality, grayscale enabled):

| Source Format | Reduction |
|---------------|-----------|
| PNG (lossless) | 70-90% smaller |
| WebP | 30-50% smaller |
| High-quality JPEG | 20-40% smaller |
| Already optimized JPEG | ~0% (passthrough) |

## Privacy

- **No server uploads**: All processing happens in your browser
- **No data collection**: Files never leave your device
- **Works offline**: After initial page load, no internet required

## Dependencies

- [JSZip 3.10.1](https://stuk.github.io/jszip/) - ZIP file handling
- [Atkinson Hyperlegible Next](https://fonts.google.com/specimen/Atkinson+Hyperlegible) - UI font

## Changelog

### v2.2.2
- NCX `dtb:uid` syncs with OPF `dc:identifier`
- Fixed duplicate ID issue when splitting images (part suffix appended)
- EPUB OCF compliance: `mimetype` written first as uncompressed entry
- OPF manifest uses correct relative paths for subdirectories

## Contributing

Issues and pull requests welcome! Please test with various EPUB sources before submitting.

## Credits

Made by **Megabit & pablohc**

Optimized for [CrossPoint Reader](https://github.com/crosspoint-reader/crosspoint-reader) and XTEink X4 e-readers.
