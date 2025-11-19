---
layout: default
title: Bubble Tea Finder
---

# 🧋 Bubble Tea Finder

[← Back to University Projects](../university-projects.html)

## Project Overview
A playful swipe-to-save web app for finding nearby bubble tea cafes and cafes. Built with vanilla JavaScript and Google Maps APIs, featuring intuitive swipe gestures and real-time location detection.

##  Features
- **Location Detection** - Automatically finds your current location using geolocation API
- **Swipeable Cards** - Interactive card stack with smooth swipe gestures
- **Live Map Integration** - Google Map displays cafe markers alongside cards
- **Local Storage** - Saves favorite cafes directly in your browser
- **Mobile-First Design** - Optimized for mobile swipe interactions
- **Playful Theme** - Bubble tea themed UI with cute emojis and design

##  Tech Stack
- **Frontend**: HTML5, CSS3, Vanilla JavaScript
- **Maps & Data**: Google Maps JavaScript API, Google Places API
- **Interactions**: Hammer.js for swipe gestures
- **Storage**: Browser LocalStorage API
- **Geolocation**: Web Geolocation API

## How It Works

### Card System
- Displays cafe cards with photos, ratings, and details
- Intuitive swipe-right to save, swipe-left to dismiss
- Visual feedback with smooth animations

### Map Integration
- Real-time synchronization between cards and map markers
- Interactive map with custom cafe markers
- Side-by-side card and map layout

### Data Flow
1. **Location Detection** → Gets user coordinates
2. **Places API** → Fetches nearby cafes
3. **Card Display** → Shows swipeable cafe cards
4. **Map Update** → Places markers on map
5. **User Action** → Swipe gestures save/dismiss cafes
6. **Local Storage** → Persists favorites between sessions


