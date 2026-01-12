# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

The Cognitive Workout is a personal cognitive training application based on the "Three Pillars of Thinking" framework. The project consists of:

1. **Framework Documentation** (`three-pillars-of-thinking.md`) - Complete training methodology for developing observation, reasoning, and reflection skills
2. **Web Application** (`cognitive-gym.html`) - Interactive training tool implementing the framework

**Design Philosophy:**
- **Zero friction**: Single HTML file, no build process, works offline immediately
- **Privacy first**: All data stored locally in browser (localStorage), never leaves your machine
- **Mobile-first**: Responsive design with touch-optimized navigation
- **Visual consistency**: Color-coded pillars (Blue/Purple/Orange) create clear mental model
- **Progressive complexity**: Features like "5 Whys" reveal questions sequentially to reduce cognitive overwhelm

## Architecture

### Single-File React Application

The application is built as a standalone HTML file with embedded React code:
- **Technology Stack**: React 18.2.0 via ESM imports, Babel standalone for JSX compilation, Tailwind CSS via CDN, Lucide icons
- **State Management**: Local React state with localStorage persistence
- **Data Storage**: Browser localStorage under key `cognitive_gym_data`
- **Zero Build Process**: Open HTML file directly in browser - no compilation needed

### Core Components Structure

**Navigation & Layout:**
- `NavBar` (cognitive-gym.html:92-119) - Responsive navigation with fixed bottom bar on mobile, sidebar on desktop. 5 views: Dashboard, Observation, Reasoning, Reflection, History.

**Training Tools:**
- `ObservationTool` (cognitive-gym.html:287-390)
  - Snapshot mode: Single reality observation without interpretation
  - Story vs Reality mode: Side-by-side comparison (red for story, green for reality)

- `ReasoningTool` (cognitive-gym.html:392-534)
  - 5-Sentence Argument: Structured logical thinking (Claim → Why → Evidence → Counter → Rebuttal)
  - Variables Game: Identify factors, toggle between "CRITICAL" and "IGNORE"

- `ReflectionTool` (cognitive-gym.html:536-677)
  - Evening Review: 5-question daily reflection
  - 5 Whys Drill: Root cause analysis with progressive disclosure (uses `currentWhyLevel` state to reveal questions sequentially)

**Data Management:**
- `HistoryLog` (cognitive-gym.html:679-749) - Displays all entries with color-coded pillar indicators, click-to-edit functionality
- `EditEntryForm` (cognitive-gym.html:122-284) - Type-specific editing interfaces (note: Variables editing is view-only due to complex structure)
- `App` (cognitive-gym.html:752-904) - Main orchestrator managing view state, entry CRUD operations, and statistics dashboard

### Data Model

All entries follow this structure:
```javascript
{
  id: string,           // timestamp-based unique ID
  timestamp: number,    // Unix timestamp
  date: string,         // Localized date string
  type: string,         // Entry type identifier
  title: string,        // Display title
  pillar: string,       // "Observation" | "Reasoning" | "Reflection"
  data: object          // Type-specific data payload
}
```

Entry types:
- `observation_snapshot` - Single reality observation
- `observation_story` - Story vs reality comparison
- `reasoning_argument` - 5-part logical argument
- `reasoning_variables` - Variable analysis with importance markers
- `reflection_review` - 5-question daily reflection
- `reflection_5whys` - Root cause analysis

## Development

### Running the Application

Open `cognitive-gym.html` directly in a web browser. No build process required.

### Modifying the Application

Since this is a single-file application with inline scripts:
1. Edit the HTML file directly
2. Refresh the browser to see changes
3. localStorage data persists across reloads

### Testing Changes

- Test localStorage persistence by creating entries, refreshing, and verifying data persists
- Test all three pillars (Observation, Reasoning, Reflection) independently
- Verify edit functionality works for all entry types
- Check mobile responsiveness (responsive navigation uses fixed bottom bar on mobile)

## Important Implementation Details

### localStorage Operations

Three core functions manage persistence (cognitive-gym.html:44-87):
- `getLocalEntries()` - Retrieves all entries from localStorage
- `saveOrUpdateLocalEntry(entry)` - Upserts entry (creates if no `id`, updates if `id` exists). Updates preserve original fields but refresh timestamp.
- `deleteLocalEntry(id)` - Removes entry by ID and returns updated array

### Design System & Styling

**Color Palette:**
- Base theme: `bg-slate-950` (dark background), `text-slate-200` (light text)
- Pillar color coding (consistent across all UI elements):
  - **Blue** (`bg-blue-600`, `text-blue-400`, `border-blue-500`) - Observation
  - **Purple** (`bg-purple-600`, `text-purple-400`, `border-purple-500`) - Reasoning
  - **Orange** (`bg-orange-600`, `text-orange-400`, `border-orange-500`) - Reflection
- Semantic colors:
  - Red (`text-red-400`, `border-red-900/30`) - "Story" (interpretation)
  - Green (`text-green-400`, `border-green-900/30`) - "Reality" (facts)

**Custom Animations (cognitive-gym.html:18-30):**
- `fade-in` keyframe: opacity 0→1, translateY 10px→0, 0.3s ease-out
- Custom scrollbar styling: dark theme with rounded hover states

**Responsive Design Patterns:**
- Mobile: Fixed bottom navigation bar (`bottom-0 w-full fixed`)
- Desktop: Sidebar navigation (`md:h-screen md:w-20`)
- Grid layouts adapt: `md:grid-cols-2`, `md:grid-cols-3`
- Bottom padding on main content accounts for mobile nav: `md:pb-8 pb-24`

### UI/UX Patterns

**Interactive Patterns:**
- **Progressive disclosure**: "5 Whys" tool reveals questions sequentially via `currentWhyLevel` state (cognitive-gym.html:542, 641). Unrevealed questions are blurred with `opacity-30 blur-[1px] pointer-events-none`
- **Hover effects**: Scale transforms (`group-hover:scale-110`), color transitions on buttons and cards
- **Visual hierarchy**: Border-left color coding on history entries, gradient backgrounds on dashboard cards
- **Form resets**: All tools clear input fields after successful save to prevent accidental resubmission

**Entry Management:**
- Click any history entry to edit (cognitive-gym.html:692)
- Hover over history entry reveals delete icon (cognitive-gym.html:701-704)
- Edit form includes both Update and Delete actions (cognitive-gym.html:274-281)


### Key Implementation Notes

**State Management:**
- Main app state tracks: `view` (current page), `entries` (all data), `editingEntry` (entry being edited)
- Initial load uses `useEffect` to populate entries from localStorage (cognitive-gym.html:758-760)
- Statistics computed live from entries array (cognitive-gym.html:782-788)

**Data Flow:**
1. User completes exercise → calls `onSave` prop with entry data
2. `saveEntry` function in App component calls `saveOrUpdateLocalEntry`
3. localStorage updated, app state refreshed, view switches to 'history'
4. Edit flow: Click entry → sets `editingEntry` state → switches to 'edit' view → EditEntryForm renders

**Important Behaviors:**
- Entry updates preserve original timestamp but update to show recent activity (cognitive-gym.html:70)
- Variables editing is view-only in EditEntryForm due to complex array structure (cognitive-gym.html:188-200)
- Dashboard shows only 3 most recent entries (cognitive-gym.html:845)
- All entry types require at least one field to be non-empty before saving

## Framework Principles

The application implements exercises from `three-pillars-of-thinking.md`:

**Pillar 1: Observation**
- Trains seeing reality without ego filters
- "What's Actually Happening?" practice
- "Remove the story" exercise

**Pillar 2: Reasoning**
- Builds systematic logical thinking
- 5-Sentence Argument (Claim → Why → Evidence → Counter → Rebuttal)
- Variable identification and prioritization

**Pillar 3: Reflection**
- Locks in learning and improvement
- 5-Minute Evening Reflection (Well → Bad → Why → Different → Learned)
- Root Cause Analysis (5 Whys drill)
