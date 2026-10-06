# Persona 5 Card Guidebook (Starter)

A Persona 5–styled single page app built with React + Vite. Beyond being a card guidebook,
it is a teaching tool: the search screen runs a **binary search over parallel arrays** and
prints a step-by-step *system log* so every decision of the algorithm can be traced.

> **Branch status:** this is the **reference solution** branch — the exercise in
> [`src/utils/searcher.js`](src/utils/searcher.js) is already implemented (comments cleaned up).
> The starter branch for students is [`main`](https://github.com/teamsigmaschool/persona5-guidebook-app/tree/main).

## Features

| Route | Page | Description |
|-------|------|-------------|
| `/` | `Home` | Hero screen with the loading animation and a shortcut to the guidebook. |
| `/album` | `Album` (Search) | Enter a registry ID, run the binary search, print the algorithm log and render the matching card. |
| `/directory` | `Directory` (Card List) | Full table of all cards (ID, name, traits, level, power). |

* Navbar audio toggle (`AudioPlayer`) plays the Persona 5 OST (`public/theme.flac`) in a loop.
* All content is static data — there is **no backend / API calls** in the app.

## Tech stack

* [React 18](https://react.dev/) + [React Router v6](https://reactrouter.com/) — UI and routing
* [Vite 5](https://vitejs.dev/) (`@vitejs/plugin-react`) — dev server and build
* Plain CSS with a custom `@font-face` (`public/P5Hatty.ttf`) — `src/index.css`
* Static dataset — `src/data/personaData.js`

## Getting started

Requirements: **Node.js 18+** and **npm**.

```bash
npm install      # install dependencies
npm run dev      # start dev server (http://localhost:5173)
npm run build    # production build into dist/
npm run preview  # serve the production build locally
```

## Project structure

```
persona5-guidebook-app/
├── index.html                # Vite entry HTML (title: Persona 5 Card Guidebook)
├── vite.config.js            # Vite + React plugin config
├── package.json              # scripts & dependencies
├── database.sql              # optional PostgreSQL schema + seed data (not used by the UI)
├── public/                   # static assets: images, font, favicon, theme.flac, gif
└── src/
    ├── main.jsx              # React root, wraps <App /> in <BrowserRouter>
    ├── App.jsx               # navbar + routes
    ├── index.css             # global Persona 5 styling
    ├── components/
    │   └── AudioPlayer.jsx   # play/mute button for the OST
    ├── pages/
    │   ├── Home.jsx          # /
    │   ├── Album.jsx         # /album  (binary search screen)
    │   └── Directory.jsx     # /directory (card table)
    ├── data/
    │   └── personaData.js    # parallel arrays: personaIDs + personaDetails
    └── utils/
        └── searcher.js       # ★ solved binary search
```

## How the solution works

`src/data/personaData.js` exports two **parallel arrays**:

* `personaIDs` — sorted list of registry IDs (`[101, 102, … 112]`)
* `personaDetails` — card objects (`name`, `traits`, `level`, `stats`, `image`) at the *same index* as their ID

`Album.jsx` calls:

```js
import { find } from '../utils/searcher';

const result = find(targetId, personaIDs, personaDetails);
```

### Contract

```js
find(targetId: number, idArray: number[], detailArray: object[]) => SearchOutcome
```

It returns an object with four keys:

```js
{
  found: true | false,   // was the ID located?
  index:  number,        // index where it was found, or -1
  data:    object | null,// detailArray[index] of the match, or null
  log:     string[]      // human readable, step-by-step trace of the search
}
```

### Algorithm (binary search)

1. Keep two pointers, `left = 0` and `right = idArray.length - 1`.
2. While `left <= right`, compute `mid = Math.floor((left + right) / 2)` and log the check.
3. `idArray[mid] === targetId` → log the success, return `{ found: true, index: mid, data: detailArray[mid], log }`.
4. `idArray[mid] < targetId` → the target is in the right half: `left = mid + 1`.
5. Otherwise → the target is in the left half: `right = mid - 1`.
6. Loop exhausted → log the miss and return `{ found: false, index: -1, data: null, log }`.

Because the range is halved on every step, the search runs in **O(log n)** time instead of
**O(n)** for a linear scan.

Example trace for `find(112, personaIDs, personaDetails)`:

```
Checking index 5: ID is 106
106 is smaller than target 112. Searching right half...
Checking index 8: ID is 109
109 is smaller than target 112. Searching right half...
Checking index 10: ID is 111
111 is smaller than target 112. Searching right half...
Checking index 11: ID is 112
Success! Found ID 112 at index 11.
```

## Branches

| Branch | Purpose |
|--------|---------|
| `main` | Starter / course branch — UI and data are ready, the search implementation is the task. |
| `solution` | This branch — reference implementation of the binary search exercise. |

```bash
git checkout main        # back to the starter
git checkout solution    # reference implementation
```

## Database

`database.sql` contains a PostgreSQL schema (`persona5_users`, `persona5_cards`) plus a few
seed rows. It is provided as reference material for the SQL part of the course — the
frontend reads everything from `src/data/personaData.js` and does not connect to it.

## License / assets

Persona 5 and its characters belong to Atlus / Sega. All artwork, music and fonts in
`public/` are fan-made course assets used for educational purposes only.
