# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

The Cognitive Workout is a personal cognitive training application based on the "Three Pillars of Thinking" framework. The project consists of:

1. **Framework Documentation** (`three-pillars-of-thinking.md`) - Complete training methodology for developing observation, reasoning, and reflection skills
2. **Web Application** (`cognitive-gym.html`) - Interactive training tool implementing the framework

## Architecture

### Single-File React Application

The application is built as a standalone HTML file with embedded React code:
- **Technology Stack**: React 18.2.0 via ESM imports, Babel standalone for JSX compilation, Tailwind CSS via CDN, Lucide icons
- **State Management**: Local React state with localStorage persistence
- **Data Storage**: Browser localStorage under key `cognitive_gym_data`

### Core Components Structure

- `NavBar` - Navigation between training pillars and views
- `ObservationTool` - Implements "Reality Snapshot" and "Story vs Reality" exercises
- `ReasoningTool` - Implements "5-Sentence Argument" and "Variables Game" exercises
- `ReflectionTool` - Implements "Evening Review" and "5 Whys Analysis" exercises
- `HistoryLog` - Displays all recorded training entries
- `EditEntryForm` - Allows editing of existing entries
- `App` - Main orchestrator managing view state and entry CRUD operations

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

Three core functions manage persistence:
- `getLocalEntries()` - Retrieves all entries
- `saveOrUpdateLocalEntry(entry)` - Creates new or updates existing entry based on presence of `id`
- `deleteLocalEntry(id)` - Removes entry by ID

### UI/UX Patterns

- Color coding: Blue (Observation), Purple (Reasoning), Orange (Reflection)
- Progressive disclosure: "5 Whys" tool reveals questions sequentially via `currentWhyLevel` state
- Animations: fade-in transitions using custom CSS keyframes
- Mobile-first: Bottom navigation bar on mobile, side navigation on desktop


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
