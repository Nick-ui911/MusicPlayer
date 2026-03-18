# 🎵 Mume — Music Player App

A React Native music streaming app built with **Expo** using the **JioSaavn API**.  
Built as part of the Lokal React Native Intern assignment.

---

## 📱 Screens

| Screen | Features |
|--------|---------|
| **Home** | Song list, search with debounce, pagination, pull-to-refresh |
| **Player** | Full controls, seek bar, shuffle, repeat, download, queue view |
| **Mini Player** | Persistent bar synced with full player, play/pause/skip |
| **Favorites** | Heart songs, persisted across sessions |
| **Queue / Playlists** | View & remove songs from current queue |
| **Settings** | Shuffle toggle, repeat mode picker |

---

## 🏗 Architecture

```
src/
├── api/
│   └── saavn.ts          # All JioSaavn API calls, image/audio URL helpers
├── store/
│   └── playerStore.ts    # Zustand global state (queue, playback, favorites)
├── hooks/
│   └── useAudioPlayer.ts # expo-av sound engine, sync with Zustand store
├── navigation/
│   └── AppNavigator.tsx  # Stack + custom Tab navigator with MiniPlayer
├── screens/
│   ├── HomeScreen.tsx
│   ├── PlayerScreen.tsx
│   ├── FavoritesScreen.tsx
│   ├── PlaylistsScreen.tsx
│   └── SettingsScreen.tsx
├── components/
│   ├── SongCard.tsx
│   └── MiniPlayer.tsx
├── utils/
│   ├── theme.ts          # Colors, typography, spacing constants
│   └── format.ts         # Duration & count formatters
└── types/
    └── index.ts          # Shared TypeScript types
```

---

## ⚙️ State Management

**Zustand** is used for all global state. A single `usePlayerStore` store holds:

- `queue` — current list of songs
- `currentIndex` — which song is active
- `isPlaying`, `currentPosition`, `duration`, `isLoading`
- `shuffleMode`, `repeatMode` (off / queue / track)
- `favorites`, `recentlyPlayed`, `downloadedSongs` — persisted via AsyncStorage

The `useAudioPlayer` hook reads this store and drives `expo-av`, writing position/duration back into the store. This means **MiniPlayer and PlayerScreen are always in sync** — they both read from the same Zustand store.

---

## 🎧 Background Playback

- `expo-av` is configured with `staysActiveInBackground: true` and `playsInSilentModeIOS: true`
- Android permissions include `FOREGROUND_SERVICE` and `WAKE_LOCK`
- iOS `UIBackgroundModes: ["audio"]` is set in `app.json`

---

## 🔁 Shuffle & Repeat

- **Shuffle**: pre-shuffles the index array, navigates through it in order
- **Repeat Track**: loops the current song on `didJustFinish`
- **Repeat Queue**: wraps back to index 0 after the last track

---

## ⚖️ Trade-offs

| Decision | Reason |
|----------|--------|
| `expo-av` over `react-native-track-player` | RNTP requires bare workflow / native build; expo-av works in Expo Go for easier demo |
| `AsyncStorage` over MMKV | No native module needed; sufficient for favorites/queue persistence |
| Single Zustand store | Simpler than Redux for this scope; avoids boilerplate |
| No offline queue persistence | Would add complexity; downloadedSong IDs are persisted, not the full queue |

---

## 🚀 Local Setup

### Prerequisites
- Node.js 18+
- Expo CLI: `npm install -g expo-cli`
- Expo Go app on your Android/iOS device **OR** an emulator

### Steps

```bash
# 1. Clone the repo
git clone https://github.com/YOUR_USERNAME/mume-music-player.git
cd mume-music-player

# 2. Install dependencies
npm install

# 3. Start the dev server
npx expo start

# 4. Scan the QR code with Expo Go (Android) or Camera app (iOS)
```

### Build APK (EAS)

```bash
# Install EAS CLI
npm install -g eas-cli

# Login to Expo account
eas login

# Build APK for Android
eas build --platform android --profile preview
```

---

## 📦 Tech Stack

| Library | Version | Purpose |
|---------|---------|---------|
| React Native (Expo) | ~55 | Core framework |
| TypeScript | ~5.9 | Type safety |
| @react-navigation/stack | ^7 | Screen navigation |
| @react-navigation/bottom-tabs | ^7 | Tab navigation |
| Zustand | ^5 | State management |
| expo-av | ^16 | Audio playback |
| AsyncStorage | ^3 | Persistence |
| @react-native-community/slider | ^5 | Seek bar |
| @expo/vector-icons | ^15 | Ionicons |

---

## ✨ Extra Features

- **Debounced search** — API called only after 400ms pause in typing
- **Pagination** — loads more results when scrolling to bottom of search
- **Recently played** tracking (stored in AsyncStorage)
- **Song download** — saves to local document directory
- **Active song highlight** — current song glows orange in lists
- **Pull-to-refresh** on Home screen
- **Dark theme** throughout with orange (#FF6B35) accent
