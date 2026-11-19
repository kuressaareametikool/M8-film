# Session 02 — State, forms, and validation walkthrough

[Concepts and tasks](README.md) · [Project rules](../../PROJECT.md#business-rules) · [Course overview](../../README.md)

Start from session 1, including its independent genre task. Finish with a movie list that supports adding, deleting, and voting in browser memory. All file paths and terminal commands refer to your own application repository root.

No packages are added in this session. Use the versions in your existing lockfile. The examples use Next.js App Router and React hooks; the verification versions are recorded at the end.

## 1. Check the starting point

```bash
nvm use
npm run lint
npm run build
npm run dev
```

Open `/movies`. Check that each movie has an ID, title, year, and genre, and that the shared navigation still works. Keep the development server in one terminal and use another for Git and checks.

**Checkpoint:** the session 1 list works before making changes. If the genre task is unfinished, complete it now. Keep any extra movie you added; the examples below show the original three.

## 2. Extend the data type

In `lib/movies.ts`, add `votes: number` to `Movie` and `votes: 0` to every fixture. Add `MovieInput` for the fields a person may enter. The complete file for the original three movies is:

```ts
export type Movie = {
  id: number;
  title: string;
  year: number;
  genre: string;
  votes: number;
};

export type MovieInput = Pick<Movie, "title" | "year" | "genre">;

export const movies: Movie[] = [
  { id: 1, title: "Interstellar", year: 2014, genre: "Sci-fi", votes: 0 },
  { id: 2, title: "Parasite", year: 2019, genre: "Thriller", votes: 0 },
  { id: 3, title: "Dune", year: 2021, genre: "Sci-fi", votes: 0 },
];
```

`Pick` creates a type containing selected fields from another type. The form supplies title, year, and genre; the list supplies the ID and initial vote count. A TypeScript type describes expected values but does not validate text entered at runtime.

This counter is temporary prototype data. Later we will represent persistent votes as related database rows.

**Checkpoint:** the existing list still compiles. Extra properties do not force the current card to display them yet. If TypeScript reports a missing `votes` field, check every fixture.

## 3. Move the interactive list behind a client boundary

First create `components/MovieList.tsx` with this small version:

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

At this stage we only read state. We will add its setter when connecting the actions.

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

The page remains a Server Component. It passes serializable movie data to `MovieList`. The `"use client"` directive marks the entry into the client component tree, where hooks and event handlers are allowed. Next.js can still render an initial HTML preview for Client Components; the directive does not mean “never rendered on the server”.

`MovieCard`, and later `MovieForm`, are imported below this boundary, so they do not need their own directives here. Callback props will be passed within the client tree, not from the server page.

`useState(initialMovies)` uses the prop as the initial value for this mounted list. From this point, the state array is the source for edits; do not mutate the imported fixtures.

**Checkpoint:** `/movies` looks the same and shows a count. The shared layout and navigation remain unchanged.

## 4. Write validation independently of React

Create `lib/movie-validation.ts`:

```ts
import type { MovieInput } from "@/lib/movies";

export type MovieFieldErrors = Partial<Record<keyof MovieInput, string>>;

type RawMovieInput = {
  title: string;
  year: string;
  genre: string;
};

type ValidationResult =
  | { success: true; data: MovieInput }
  | { success: false; errors: MovieFieldErrors };

export function validateMovieInput(
  input: RawMovieInput,
  currentYear: number,
): ValidationResult {
  const title = input.title.trim();
  const genre = input.genre.trim();
  const year = Number(input.year);
  const errors: MovieFieldErrors = {};

  if (title.length === 0 || title.length > 120) {
    errors.title = "Title must contain 1–120 characters.";
  }

  if (
    input.year.trim() === "" ||
    !Number.isInteger(year) ||
    year < 1888 ||
    year > currentYear + 1
  ) {
    errors.year = `Year must be an integer from 1888 to ${currentYear + 1}.`;
  }

  if (genre.length === 0 || genre.length > 40) {
    errors.genre = "Genre must contain 1–40 characters.";
  }

  if (Object.keys(errors).length > 0) {
    return { success: false, errors };
  }

  return { success: true, data: { title, year, genre } };
}
```

The function trims text, converts the year, and applies the rules from `PROJECT.md`. `Number.isInteger` rejects fractional and non-numeric results. An explicit empty-string check avoids treating missing input as a meaningful number.

`MovieFieldErrors` maps field names to optional messages. The result has two alternatives: success with parsed data, or failure with errors. Checking `result.success` lets TypeScript determine which properties are available.

The caller supplies `currentYear`. The validation function reads no clock, changes no external state, and depends only on its arguments. That makes it reusable on the server and easy to test later.

**Checkpoint:** read the function and predict its result for a blank title, a fractional year, and a genre containing only spaces. Confirm that it has no React imports or database access.

## 5. Create the form

Create `components/MovieForm.tsx`:

```tsx
import { useState, type FormEvent } from "react";
import type { MovieInput } from "@/lib/movies";
import { validateMovieInput } from "@/lib/movie-validation";

type MovieFormProps = {
  onAdd: (input: MovieInput) => void;
};

export default function MovieForm({ onAdd }: MovieFormProps) {
  const [error, setError] = useState("");

  function handleSubmit(event: FormEvent<HTMLFormElement>) {
    event.preventDefault();

    const form = event.currentTarget;
    const formData = new FormData(form);
    const result = validateMovieInput(
      {
        title: String(formData.get("title") ?? ""),
        year: String(formData.get("year") ?? ""),
        genre: String(formData.get("genre") ?? ""),
      },
      new Date().getFullYear(),
    );

    if (!result.success) {
      setError(Object.values(result.errors).join(" "));
      return;
    }

    onAdd(result.data);
    setError("");
    form.reset();
  }

  return (
    <form onSubmit={handleSubmit} noValidate>
      <h2>Add a movie</h2>
      {error && <p role="alert">{error}</p>}
      <p>
        <label htmlFor="title">Title</label>
        <input id="title" name="title" type="text" required />
      </p>
      <p>
        <label htmlFor="year">Release year</label>
        <input id="year" name="year" type="number" step="1" required />
      </p>
      <p>
        <label htmlFor="genre">Genre</label>
        <input id="genre" name="genre" type="text" required />
      </p>
      <button type="submit">Add movie</button>
    </form>
  );
}
```

The inputs are **uncontrolled**: the browser stores the text while the person types. `FormData` reads it when submitted, using each input’s `name`. The form owns only feedback state, not a second movie list.

`event.preventDefault()` prevents the normal page submission. The captured `form` element is used both to read values and to reset the fields after a successful addition. Invalid input returns early, leaving the values available to correct.

`noValidate` deliberately disables the browser’s built-in submission checks for this exercise, so we can observe our validation for blank and fractional values. It does not make TypeScript or the `required` attributes enforce our rules. The helper is responsible for this prototype’s checks; server-side validation will also be required once writes reach a database.

The form combines validation messages in one alert for now. Independent work will place each message next to its field.

## 6. Give the card action callbacks

Replace `components/MovieCard.tsx` with:

```tsx
import type { Movie } from "@/lib/movies";

type MovieCardProps = {
  movie: Movie;
  onVote: (id: number) => void;
  onDelete: (id: number) => void;
};

export default function MovieCard({
  movie,
  onVote,
  onDelete,
}: MovieCardProps) {
  return (
    <article>
      <h2>{movie.title}</h2>
      <p>Year: {movie.year}</p>
      <p>Genre: {movie.genre}</p>
      <p>Votes: {movie.votes}</p>
      <button type="button" onClick={() => onVote(movie.id)}>
        Vote for {movie.title}
      </button>
      <button type="button" onClick={() => onDelete(movie.id)}>
        Delete {movie.title}
      </button>
    </article>
  );
}
```

The card displays one movie and reports which ID was selected. It does not own the collection or update vote counts itself.

`onClick={() => onVote(movie.id)}` passes a function to React to call on a click. Writing `onClick={onVote(movie.id)}` would call it during rendering. Explicit `type="button"` avoids accidental form submission if the card is later composed inside a form.

The movie title in the button label also helps distinguish actions for different movies.

**Transition:** the old list now lacks the new required callback props. Complete step 7 before checking the browser or build again.

## 7. Connect add, delete, and vote operations

Replace `components/MovieList.tsx` with this complete version:

```tsx
"use client";

import { useRef, useState } from "react";
import MovieCard from "@/components/MovieCard";
import MovieForm from "@/components/MovieForm";
import type { Movie, MovieInput } from "@/lib/movies";

type MovieListProps = {
  initialMovies: Movie[];
};

export default function MovieList({ initialMovies }: MovieListProps) {
  const [movies, setMovies] = useState<Movie[]>(initialMovies);
  const nextId = useRef(Math.max(0, ...initialMovies.map((movie) => movie.id)) + 1);

  function addMovie(input: MovieInput) {
    const newMovie: Movie = { ...input, id: nextId.current, votes: 0 };
    nextId.current += 1;
    setMovies((currentMovies) => [...currentMovies, newMovie]);
  }

  function deleteMovie(id: number) {
    setMovies((currentMovies) =>
      currentMovies.filter((movie) => movie.id !== id),
    );
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
      <MovieForm onAdd={addMovie} />
      <p aria-live="polite">Movies: {movies.length}</p>
      {movies.map((movie) => (
        <MovieCard
          key={movie.id}
          movie={movie}
          onVote={voteForMovie}
          onDelete={deleteMovie}
        />
      ))}
    </section>
  );
}
```

Follow one addition: form submission → validation → `onAdd` → `addMovie` → state update → rendered card.

The array operations serve different purposes:

| Operation | New state |
|---|---|
| Add | `[...currentMovies, newMovie]` creates an array with one extra item |
| Delete | `filter` keeps every movie except the selected ID |
| Vote | `map` replaces only the selected movie with a copied object and an incremented count |

A functional setter receives the latest state React provides. Do not use `push`, `splice`, or `movie.votes++` on state objects. React may call updater functions more than once during development checks, so keep them free of side effects.

`nextId` is a ref: it retains a value across renders without triggering a render. It starts above the highest fixture ID, including when there are no fixtures. We advance it in the add event handler, outside the state updater. Deleting a movie does not move it backwards. Using `movies.length + 1` would risk duplicate IDs after a deletion.

This ID counter lasts only for the mounted prototype. A database will generate persistent IDs later. There is no second list state in the card or form.

**Checkpoint:** add a movie, vote on it, and delete a different movie. The screen updates without a page reload. Votes begin at zero, and existing cards retain their values.

## 8. Verify the behaviour

Run these checks manually before committing:

| Action | Expected result |
|---|---|
| Add `  Arrival  `, `2016`, `  Sci-fi  ` | A new movie with trimmed text and zero votes; inputs reset |
| Submit a blank title or a spaces-only genre | Error feedback; no movie added; entered values remain |
| Submit year `2014.5`, `1887`, or current year + 2 | Rejected; no movie added |
| Submit year `1888` or current year + 1 | Accepted when the other fields are valid |
| Submit a title longer than 120 characters or genre longer than 40 | Rejected |
| Click Vote three times on one movie | Only that movie gains three votes |
| Delete a movie, then add two more | Correct cards remain; no duplicate-key warnings |
| Delete every movie | Count reaches zero; the add form still works |
| Reload the browser | Original fixtures and vote counts return |

Use a full browser refresh for the last check. Development hot reload may preserve component state, and navigation can have different remount behaviour; neither is evidence of persistence. No local storage, file, server write, or database stores these changes.

From a second terminal:

```bash
npm run lint
npm run build
```

## 9. Independent work

Try the [required tasks](README.md#independent-work) before opening the hints below.

### Field feedback

Display each validation error beside its corresponding input. Connect the input to the message with `aria-describedby`, and set `aria-invalid` when that field has an error. Clear old errors after a successful submission.

<details>
<summary>Implementation hints: field errors</summary>

In `MovieForm.tsx`, import the error type alongside the helper:

```tsx
import {
  validateMovieInput,
  type MovieFieldErrors,
} from "@/lib/movie-validation";
```

Replace the single error state with:

```tsx
const [errors, setErrors] = useState<MovieFieldErrors>({});
```

In the invalid branch use `setErrors(result.errors)`. After a successful addition use `setErrors({})`. Remove the old `error` alert and every call to `setError`.

For each field, use this pattern (shown for title):

```tsx
<p>
  <label htmlFor="title">Title</label>
  <input
    id="title"
    name="title"
    type="text"
    required
    aria-invalid={Boolean(errors.title)}
    aria-describedby={errors.title ? "title-error" : undefined}
  />
  {errors.title && (
    <span id="title-error" role="alert">{errors.title}</span>
  )}
</p>
```

Repeat for year and genre with matching field names and unique error IDs. Retain `type="number"` and `step="1"` on the year input. Submit invalid values in multiple fields, then correct them and verify that errors clear.

</details>

### Empty list

Show a helpful message when all movies have been removed, while keeping the form available.

<details>
<summary>Implementation hint: empty list</summary>

Inside `MovieList`, after the count and before the card mapping, add:

```tsx
{movies.length === 0 && <p>No movies yet. Add the first suggestion.</p>}
```

Delete all movies, confirm the message appears, then add a movie and confirm it disappears.

</details>

### Record your findings

Create `docs/state-and-validation.md` in English. Include accepted/rejected inputs and observed results, the state owner, the role of the form and card, and what a refresh resets. Explain why this application does not yet save data.

Optional: add a reset button with `type="reset"`. Use the form’s `onReset` event to clear feedback too; do not add a second collection state. A reset should clear the form, not remove movies.

## 10. Review the changed files and save your work

```text
app/movies/page.tsx           Server page: supplies initial data
lib/movies.ts                 Movie type, form input type, fixtures
lib/movie-validation.ts       Pure validation and parsed input
components/MovieList.tsx      Client boundary and movie state owner
components/MovieForm.tsx      Form submission and feedback
components/MovieCard.tsx      Display and callback buttons
```

After independent work, `docs/state-and-validation.md` records your verification. No package installation or configuration change is needed.

```bash
npm run lint
npm run build
git status
git add app/movies/page.tsx lib/movies.ts lib/movie-validation.ts components/MovieList.tsx components/MovieForm.tsx components/MovieCard.tsx docs/state-and-validation.md
git diff --cached --stat
git commit -m "Add movie form, voting, and browser state"
git push
```

Review the staged files and ensure no generated output is included. Stop here: persistent storage starts in session 3.

## Troubleshooting

| Symptom | Check |
|---|---|
| Hooks or event handlers are rejected | Put `"use client"` at the top of `MovieList.tsx`, before imports. Keep the page server-rendered. |
| “Functions cannot be passed…” across the server boundary | The server page passes movie data only. Define and pass callbacks inside `MovieList`. |
| New card props are missing | Finish replacing `MovieList` after updating the card. |
| Form reloads the page | Check `event.preventDefault()` and the `onSubmit` handler. |
| `FormData` has missing values | Check each input’s `name`, not only its `id`. |
| Cards show unexpected values after deletion | Use stable IDs as keys; do not use array indexes or length-based IDs. |
| A vote changes the wrong movie | Compare IDs in `map`, copy the matching object, and return every other item unchanged. |
| Changes disappear on refresh | Expected: this session uses browser memory. |

## Verification and references

The in-class examples passed ESLint, TypeScript checking, a production build, validation boundary checks, and browser checks for add/delete/vote and refresh behaviour with Node.js 24.22.3, Next.js 16.4.6, and React 19.3.1. Students continue using their session 1 lockfile; no dependency upgrade is required for this lesson.

- [Next.js: Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [React: updating arrays in state](https://react.dev/learn/updating-arrays-in-state)
- [React: input fields and reading form values](https://react.dev/reference/react-dom/components/input)
- [React: useRef](https://react.dev/reference/react/useRef)
