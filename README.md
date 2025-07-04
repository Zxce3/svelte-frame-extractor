# 🎥 Svelte Frame Extractor

A modern, browser-based video frame extraction tool built with **Svelte 5** and **Shadcn-Svelte**. Extract frames from video files with precision, add watermarks, and download results—all without uploading files to any server.

## ✨ Features

- **🔒 100% Private**: All processing happens in your browser—videos never leave your device
- **🎯 Multiple Extraction Methods**:
  - **Interval**: Extract frames at regular time intervals
  - **Count**: Extract a specific number of evenly distributed frames
  - **Range**: Extract frames from a specific time range
- **🏷️ Watermark Support**: Add customizable text watermarks with:
  - Custom text, font size, and color
  - Adjustable opacity and positioning
  - Real-time preview
- **📸 Format Options**: Export as JPEG or PNG
- **⬇️ Smart Downloads**: 
  - Single frame download for individual frames
  - ZIP archive for multiple frames
- **🎨 Beautiful UI**: Modern glassmorphism design with smooth animations
- **📱 Responsive**: Works perfectly on desktop and mobile devices

## 🚀 Getting Started

### Prerequisites

- [Bun](https://bun.sh/) (recommended) or Node.js 18+

### Installation

```bash
# Clone the repository
git clone https://github.com/zxce3/svelte-frame-extractor.git
cd svelte-frame-extractor

# Install dependencies
bun install
```

### Development

```bash
# Start the development server
bun run dev

# Or with auto-open browser
bun run dev -- --open
```

The application will be available at `http://localhost:5173`

## 🏗️ Building

```bash
# Create production build
bun run build

# Preview production build
bun run preview
```

## 🛠️ Tech Stack

- **Frontend Framework**: [Svelte 5](https://svelte.dev/) with latest runes API
- **Styling**: [TailwindCSS 4.0](https://tailwindcss.com/) with custom animations
- **UI Components**: [Shadcn-Svelte](https://shadcn-svelte.com/) + custom components
- **Icons**: [Lucide Svelte](https://lucide.dev/)
- **File Processing**: [JSZip](https://stuk.github.io/jszip/) for ZIP generation
- **Notifications**: [Svelte Sonner](https://svelte-sonner.vercel.app/)
- **Build Tool**: [Vite](https://vitejs.dev/)
- **Type Safety**: TypeScript

## 📁 Project Structure

```
src/
├── lib/
│   ├── components/
│   │   ├── VideoUpload.svelte       # Drag & drop video upload
│   │   ├── VideoPreview.svelte      # Video player with controls
│   │   ├── ExtractionSettings.svelte # Frame extraction configuration
│   │   ├── ExtractedFrames.svelte   # Frame gallery with selection
│   │   ├── VideoProcessor.svelte    # Core frame extraction logic
│   │   ├── ui/                      # Shadcn-Svelte UI components
│   │   └── page/                    # Page-specific components
│   └── utils.ts                     # Utility functions
├── routes/
│   ├── +layout.svelte               # App layout
│   └── +page.svelte                 # Main application page
└── app.css                          # Global styles and animations
```

## 🎮 Usage

1. **Upload Video**: Drag and drop a video file or click to select
2. **Configure Extraction**:
   - Choose extraction method (Interval/Count/Range)
   - Set parameters (interval time, frame count, time range)
   - Select output format (JPEG/PNG)
   - Optional: Enable and customize watermark
3. **Extract Frames**: Click "Extract Frames" to start processing
4. **Review & Download**: 
   - Preview extracted frames
   - Select/deselect frames as needed
   - Download individual frames or all selected frames as a ZIP archive

## 🌟 Component Architecture

The application is built with a modular component architecture:

- **VideoUpload**: Handles file selection with drag-and-drop support
- **VideoPreview**: Displays video with HTML5 player controls
- **ExtractionSettings**: Manages all extraction configuration options
- **ExtractedFrames**: Shows frame gallery with selection capabilities
- **VideoProcessor**: Contains core video processing logic and canvas operations

## 🎨 Design System

- **Colors**: Custom color palette with dark/light mode support
- **Typography**: Optimized font hierarchy with proper contrast
- **Animations**: Smooth transitions and micro-interactions
- **Layout**: Responsive grid system with mobile-first approach

## 🔧 Configuration

The project uses standard SvelteKit configuration files:

- `svelte.config.js` - Svelte configuration
- `vite.config.ts` - Vite build configuration  
- `tailwind.config.js` - TailwindCSS configuration
- `tsconfig.json` - TypeScript configuration

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- [Svelte Team](https://svelte.dev/) for the amazing framework
- [TailwindCSS](https://tailwindcss.com/) for the utility-first CSS framework
- [Lucide](https://lucide.dev/) for the beautiful icons
- [Shadcn-Svelte](https://shadcn-svelte.com/) for accessible component UI/UX

---

Made with ❤️ using Svelte 5 and modern web technologies.
