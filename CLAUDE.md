# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

- **Development server**: `npm run dev` - Starts the Vite development server
- **Build**: `npm run build` - Compiles TypeScript and builds for production
- **Preview**: `npm run preview` - Previews the production build locally
- **Lint**: `npm run lint` - Runs ESLint on the codebase
- **Type checking**: `tsc --noEmit` - TypeScript type checking without emitting files
- **Electron development**: `npm run electron:dev` - Starts Electron app in development mode
- **Electron build (Windows)**: `npm run electron:build:win` - Builds Windows executable
- **Electron build (Mac)**: `npm run electron:build:mac` - Builds macOS executable
- **Electron build (Linux)**: `npm run electron:build:linux` - Builds Linux executable

## Code Architecture

### High-Level Structure

- **src/** - Main source code directory
  - **components/** - Reusable UI components (MenuBar, Sidebar, SoundResearchTool, etc.)
  - **context/** - React context providers (AppContext for global state)
  - **store/** - State management using Zustand-like patterns (useAppStore)
  - **services/** - External service integrations (Firebase, Cloudinary)
  - **lib/** - Utility functions and helpers
  - **types/** - TypeScript type definitions
  - **assets/** - Static assets (icons, images)
  - **electron/** - Electron-specific files (main.js, preload.js)

### State Management

- Uses a custom store pattern in `src/store/useAppStore.ts` with React's useState/useEffect
- Centralized state for project data, UI state (tabs, modals), and plugin management
- Persists project data to localStorage and Firebase
- Provides actions for project, stack, sous-stack, and research instrument manipulation

### Key Features

- **Project-based workflow**: Create, open, save, and export ToneLab projects (.tl files)
- **Modular organization**: Projects contain Stacks → SousStacks (tracks) → RechercheInstrument (instrument researches)
- **Plugin system**: Manage audio plugins with Firebase backend and Cloudinary image storage
- **UI Layout**:
  - MenuBar (File, Project, Tools menus)
  - Conditional Sidebar (visible only in stack view)
  - Main content area (Metronome or SoundResearchTool based on active tab)
  - BottomBar (global controls)
  - Modal dialogs (NewStackModal)

### Data Models (from src/types/)

- **ToneLabProject**: Root project object with version, metadata, stacks, and entries
- **Stack**: Container for musical projects/research
- **SousStack**: Represents a musical track/piece with an entry
- **SoundEntry**: Audio entry with metadata (title, artist, instrument, plugin settings)
- **RechercheInstrument**: Specific instrument research within a sousStack
- **Plugin**: Audio plugin information with name, URL, and instrument type

### Styling

- Tailwind CSS for utility-first styling
- Custom CSS variables defined in index.css and App.css
- Dark theme as default with hsl(222, 25%, 8%) background
- shadcn/ui components for consistent UI elements

### File Conventions

- TypeScript (.tsx) for React components
- TypeScript (.ts) for logic and utilities
- CSS modules or global styles in .css files
- Export patterns: Named exports for components, default exports for route components

## Common Tasks

### Adding New Components

1. Create component in `src/components/`
2. Export from component's index file or directly import
3. Use shadcn/ui primitives when available
4. Follow existing Tailwind class patterns

### Modifying State

1. Add/update types in `src/types/` if needed
2. Modify state in `src/store/useAppStore.ts` using immer-like patterns
3. Ensure persistence to localStorage/Firebase in mutators
4. Use existing action patterns (setState with partial updates)

### UI Development

- MenuBar demonstrates custom menu system with click-outside closing
- Components use conditional rendering based on context state (ongletActif, vueActive)
- Modals controlled via state flags (modalSousStackOuverte)

### Electron Development

1. Main process: `electron/main.js` - Handles window creation and system events
2. Preload script: `electron/preload.js` - Securely exposes APIs to renderer
3. Renderer process: Your React/Vite app (unchanged)
4. Communication: Use contextBridge and ipcRenderer for secure IPC
5. Building: Configure electron-builder in package.json

## Database & Services

- **Firebase**: Used for plugin storage and project saving (via firebaseService)
- **Cloudinary**: Image upload service for plugin images
- **localStorage**: Primary persistence layer for current project

## Error Handling

- Error boundaries not explicitly shown; errors typically caught and logged
- File operations include validation (project format checking)
- User confirmations for destructive actions (overwriting unsaved changes)

## Electron Specific Guidelines

### File Structure for Electron

```
electron/
├── main.js          # Electron main process
├── preload.js       # Preload script for secure IPC
└── icon/
    ├── icon.ico     # Windows icon (256x256)
    ├── icon.icns    # macOS icon
    └── icon.png     # Linux icon (1024x1024)
```

### Security Best Practices

1. Enable contextIsolation in webPreferences
2. Use preload scripts to expose only necessary APIs
3. Disable nodeIntegration in renderer process
4. Validate all data coming from renderer to main process
5. Use secure helpers for file operations

### Building Process

1. Development: `npm run electron:dev` (runs Vite + Electron concurrently)
2. Production build:
   - Build React app: `vite build`
   - Package with electron-builder
   - Output in `dist/` or `release/` directory
3. Icon generation: Use ImageMagick or similar tools to create multi-resolution icons

### Troubleshooting

- If Electron fails to start, check console output in terminal
- For white screen issues, verify webPreferences settings
- For missing module errors, ensure dependencies are in package.json
- For IPC issues, verify preload script exposes correct APIs
