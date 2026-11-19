# Session 02 — State, selection, and voting walkthrough

[Concepts and tasks](README.md) · [Project rules](../../PROJECT.md#business-rules) · [Course overview](../../README.md)

Start from session 1, including its independent genre task. Finish with a list where you can choose a movie and cast votes. Use only the existing movie fixtures. All file paths and commands refer to your own application repository root.

No packages are added. Continue using your session 1 lockfile. Forms and movie management will be introduced in session 4.

## 1. Check the starting point

```bash
nvm use
npm run lint
npm run build
npm run dev
```

Open `/movies`. Each movie should have an ID, title, year, and genre, and the shared navigation should still work. Keep the development server in one terminal and use another for checks and Git.

**Checkpoint:** the session 1 list works before making changes. Complete the genre task if necessary. Keep any extra movie you added; the examples below show the original three.

## 2. Add a vote count to each movie

In `lib/movies.ts`, add `votes: number` to the type and `votes: 0` to every fixture. The complete file for the original three movies is:

```ts
export type Movie = {
  id: number;
  title: string;
  year: number;
  genre: string;
  votes: number;
};

export const movies: Movie[] = [
  { id: 1, title: "Interstellar", year: 2014, genre: "Sci-fi", votes: 0 },
  { id: 2, title: "Parasite", year: 2019, genre: "Thriller", votes: 0 },
  { id: 3, title: "Dune", year: 2021, genre: "Sci-fi", votes: 0 },
];
```

The initial vote counts are zero. This is temporary display data; session 3 will introduce related vote records for persistent storage.

**Checkpoint:** the existing page still compiles. If TypeScript reports a missing field, check all fixtures, including your fourth movie. We will display the counts in the next steps.

## 3. Move the list behind a client boundary

Create `components/MovieList.tsx` with this small starting version:

```tsx
"use client";

import { useState } from "react";
import MovieCard from "@/components/MovieCard";
import type { Movie } from "@/lib/movies";

type MovieListProps = {
  initialMovies: Movie[];
};

export default function MovieList({ initialMovies }: MovieListProps) {
  const [movies] = useState<Movie[]>(initialMovies);

  return (
    <section aria-label="Movie suggestions">
      <p>Movies: {movies.length}</p>
      {movies.map((movie) => (
        <MovieCard key={movie.id} movie={movie} />
      ))}
    </section>
  );
}
```

At this point we only read state. We will add its setter when connecting voting.

Replace `app/movies/page.tsx` with:

```tsx
import MovieList from "@/components/MovieList";
import { movies } from "@/lib/movies";

export default function MoviesPage() {
  return (
    <main>
      <h1>Movies</h1>
      <MovieList initialMovies={movies} />
    </main>
  );
}
```

The page remains a Server Component and passes serializable data to `MovieList`. The `"use client"` directive marks the entry to a component tree that can use hooks and browser event handlers. Next.js can still render initial HTML for Client Components; the directive does not mean “never rendered on the server”.

`useState(initialMovies)` takes the supplied fixtures as the initial value for this mounted list. Future edits will update this state, rather than changing the imported fixture array.

`MovieCard` is imported beneath the client boundary, so it does not need a separate directive. Callback functions will be created and passed inside this client tree; the server page passes only data.

**Checkpoint:** `/movies` still displays the same cards and a movie count. Navigation and the shared layout remain unchanged.

## 4. Understand the two pieces of state

We need two independent values:

| Value | Initial value | What changes it? |
|---|---|---|
| `movies` | The fixture array | A Vote button updates one count |
| `selectedMovieId` | `null` | A Choose button stores one movie ID |

`null` means there is no current selection. Store an ID and derive the selected movie from the current array with `find`. Storing a second movie object in state would give us two copies to keep synchronized after every vote.

Choosing previews a movie. It does not cast a vote, enforce one vote per person, or automatically choose the highest-voted movie. Voting and ranking are separate concepts; ranking comes later.

## 5. Give the card action callbacks

Replace `components/MovieCard.tsx` with:

```tsx
import type { Movie } from "@/lib/movies";

type MovieCardProps = {
  movie: Movie;
  onSelect: (id: number) => void;
  onVote: (id: number) => void;
};

export default function MovieCard({
  movie,
  onSelect,
  onVote,
}: MovieCardProps) {
  return (
    <article>
      <h2>{movie.title}</h2>
      <p>Year: {movie.year}</p>
      <p>Genre: {movie.genre}</p>
      <p>Votes: {movie.votes}</p>
      <button type="button" onClick={() => onSelect(movie.id)}>
        Choose {movie.title}
      </button>
      <button type="button" onClick={() => onVote(movie.id)}>
        Vote for {movie.title}
      </button>
    </article>
  );
}
```

The card displays the current data and calls the appropriate callback with its ID. It does not own a separate vote counter or selection state.

`onClick={() => onVote(movie.id)}` passes a function that React calls on a click. Writing `onClick={onVote(movie.id)}` would call the function during rendering. Explicit `type="button"` states that these are ordinary action buttons.

**Transition:** the old list now lacks the new required callback props. Complete step 6 before checking the browser or build again.

## 6. Connect the state updates

Replace `components/MovieList.tsx` with this complete version:

```tsx
"use client";

import { useState } from "react";
import MovieCard from "@/components/MovieCard";
import type { Movie } from "@/lib/movies";

type MovieListProps = {
  initialMovies: Movie[];
};

export default function MovieList({ initialMovies }: MovieListProps) {
  const [movies, setMovies] = useState<Movie[]>(initialMovies);
  const [selectedMovieId, setSelectedMovieId] = useState<number | null>(null);

  const selectedMovie = movies.find((movie) => movie.id === selectedMovieId);

  function selectMovie(id: number) {
    setSelectedMovieId(id);
  }

  function voteForMovie(id: number) {
    setMovies((currentMovies) =>
      currentMovies.map((movie) =>
        movie.id === id ? { ...movie, votes: movie.votes + 1 } : movie,
      ),
    );
  }

  return (
    <section aria-label="Movie suggestions">
      <p>Movies: {movies.length}</p>
      <div aria-live="polite">
        {selectedMovie ? (
          <p>Selected: {selectedMovie.title} — Votes: {selectedMovie.votes}</p>
        ) : (
          <p>No movie selected. Choose one from the list.</p>
        )}
      </div>
      {movies.map((movie) => (
        <MovieCard
          key={movie.id}
          movie={movie}
          onSelect={selectMovie}
          onVote={voteForMovie}
        />
      ))}
    </section>
  );
}
```

Trace a selection: Choose button → card callback → `selectMovie` → selected ID state → `find` → selection message.

Trace a vote: Vote button → card callback → `voteForMovie` → new movie array → updated card and selection message.

The important parts are:

- `useState<number | null>(null)` allows either a selected ID or no selection.
- `setMovies((currentMovies) => ...)` calculates the next value from the latest state React provides.
- `map` returns a new array, and `{ ...movie, votes: movie.votes + 1 }` copies the matching movie with its new count.
- Movies with other IDs are returned unchanged. Do not write `movie.votes++` or mutate the fixture array.
- `selectedMovie` is recalculated from the current array on each render, so its count stays up to date without another state setter or an effect.
- `key={movie.id}` gives each card a stable identity. A movie’s position is not its identity.

Updater functions should be pure: return the next state without side effects. React may call them more than once during development checks.

**Checkpoint:** choose a movie, then vote for it. Both its card and the selection message update. Vote for a different movie and verify that the selection stays on the first movie.

## 7. Verify the behaviour

| Action | Expected result |
|---|---|
| Open `/movies` | All fixtures have zero votes and no movie is selected |
| Choose Interstellar | Its title and zero votes appear in the selection message; counts do not change |
| Vote for Interstellar three times | Its count reaches three in both the card and selection message |
| Vote for Parasite | Only Parasite gains a vote; Interstellar remains selected |
| Choose Parasite | Selection switches and shows its existing count |
| Vote repeatedly | Each click adds one; no count decreases |
| Refresh the browser | Fixtures return to zero votes and no selection |

Use a full browser refresh for the final check. Hot reload may preserve state, and navigation can have different remount behaviour. Neither is evidence of persistence. These changes are not written to a file, local storage, or database.

```bash
npm run lint
npm run build
```

## 8. Independent work

Try the [required tasks](README.md#independent-work) before opening the hints.

### Display total votes

Calculate the total from the current movie array and display it above the cards. Do not store the total in another state variable.

<details>
<summary>Hint: derive a total</summary>

After the `selectedMovie` calculation, add:

```tsx
const totalVotes = movies.reduce((total, movie) => total + movie.votes, 0);
```

`reduce` accumulates a value. The initial total is zero, and each iteration adds one movie’s votes. It also works for an empty array.

Add this paragraph inside the returned section:

```tsx
<p>Total votes: {totalVotes}</p>
```

Vote for different movies and compare the total with the individual counts.

</details>

### Clear the selection

Add a button that clears the selected ID without changing any vote counts.

<details>
<summary>Hint: return to null</summary>

After the selection message, add:

```tsx
<button type="button" onClick={() => setSelectedMovieId(null)}>
  Clear selection
</button>
```

The existing conditional display handles `null` by showing the initial message. Choose a movie, cast votes, clear the selection, and confirm that the votes remain.

Optional: add `disabled={selectedMovieId === null}` to the button.

</details>

### Explain the state flow

Create `docs/state-and-voting.md` in English. Identify the state owner, describe how callbacks reach it, explain why selection is stored as an ID, and record what happens on a full refresh. Include the manual checks you performed and their results.

## 9. Review changed files and save

```text
lib/movies.ts                 Movie type and fixtures with vote counts
app/movies/page.tsx           Server page supplying initial data
components/MovieList.tsx      Client boundary, selection, and vote state
components/MovieCard.tsx      Display and choose/vote callbacks
docs/state-and-voting.md      Independent-work explanation and observations
```

No installation or configuration changes are needed.

```bash
npm run lint
npm run build
git status
git add lib/movies.ts app/movies/page.tsx components/MovieList.tsx components/MovieCard.tsx docs/state-and-voting.md
git diff --cached --stat
git commit -m "Add movie selection and voting with React state"
git push
```

Review the staged files before committing. Stop here; the next session introduces persistence.

## Troubleshooting

| Symptom | Check |
|---|---|
| Hooks or event handlers are rejected | Put `"use client"` before imports in `MovieList.tsx`. |
| Callback functions are rejected at the server boundary | The page passes movie data only. Define callbacks inside `MovieList`. |
| Card props are missing | Finish replacing the list after updating the card. |
| Clicking causes repeated renders | Pass an arrow function to `onClick`; do not call the callback while rendering. |
| Every movie gains a vote | Check the ID comparison inside `map`. |
| Selection shows an old vote count | Derive the selected movie from the current array rather than storing a separate object. |
| Changes disappear after refresh | Expected: this session uses browser memory. |

## Verification and references

The complete examples passed ESLint, TypeScript checking, a production build, and browser checks for selection, repeated votes, vote isolation, and refresh reset using Node.js 24.22.3, Next.js 16.4.6, and React 19.3.1. No dependency upgrade is required for this lesson.

- [Next.js: Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [React: updating arrays in state](https://react.dev/learn/updating-arrays-in-state)
- [React: choosing a state structure](https://react.dev/learn/choosing-the-state-structure)
