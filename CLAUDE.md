# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Development Commands

- **Start development server**: `npm run dev` (runs Vite at http://localhost:5173)
- **Build for production**: `npm run build` (TypeScript compile + Vite build)
- **Lint code**: `npm run lint` (ESLint on entire project)
- **Preview production build**: `npm run preview` (locally preview built assets)
- **Run single test**: Not configured - testing framework not set up

## Code Architecture & Structure

### State Management
- Uses React Context API (`src/context/AppContext.tsx`) with custom `useAppStore` hook
- Centralized state in `src/store/useAppStore.ts` handles:
  - Project data (ToneLabProject type with stacks, sub-stacks, entries)
  - Plugin management (Firebase + Cloudinary storage)
  - UI state (active tabs, views, modals, sidebar)
  - Migration functions for data format versions
- State persists to localStorage with automatic Firebase backup

### Component Hierarchy
- `src/App.tsx`: Main layout with MenuBar, conditional Sidebar, main content area, BottomBar
- **MenuBar** (`src/components/MenuBar.tsx`): File/Project/Tools menus with keyboard shortcuts (Ctrl+S)
- **Sidebar** (`src/components/Sidebar.tsx`): Shows stacks when active tab is "stack"
- **Main Content** (conditional):
  - `SoundResearchTool`: Plugin gallery and instrument research interface
  - `Metronome`: Simple metronome component
- **Modals**: `NewStackModal` for creating/editing sub-stacks

### Data Models (src/types/)
- `ToneLabProject`: Root project object with version, stacks, entries
- `Stack`: Container for sub-stacks (musical categories)
- `SousStack`: Represents a musical piece with entry and recherches
- `RechercheInstrument`: Instrument research within a sous-stack (plugin config, notes)
- `SoundEntry`: Core data for a musical piece (title, artist, instrument, plugin)
- `Plugin`: VST/AU plugin metadata (name, URL, instrument type, image)

### Key Features
- **Project Management**: New, open, save (.tl files), export with localStorage + Firebase sync
- **Plugin System**: Add/remove plugins with Cloudinary image upload, filter by instrument type
- **Research Workflow**: Stacks → SousStacks (pieces) → RechercheInstrument (plugin tests)
- **Firebase Integration**: Plugin storage via `src/services/firebaseService.ts`
- **File Handling**: .tl files are JSON format with versioning and migration functions

### Styling & UI
- Tailwind CSS with custom colors (via CSS variables)
- shadcn/ui components for consistent UI elements
- Dark theme by default (hsl(222, 25%, 8%) background)
- Responsive layout with collapsible sidebar