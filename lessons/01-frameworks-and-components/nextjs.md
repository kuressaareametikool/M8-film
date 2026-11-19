# Session 01 — Next.js walkthrough

[Concepts and tasks](README.md) · [Course overview](../../README.md)

Start with your own new GitHub repository and no application. Finish with two routes, a shared layout, typed movie fixtures, and a reusable card. This reorganises the original starter guide; do not implement later sessions yet.

All application paths below are relative to your own repository root. Keep your repository open in the editor and read this course guide separately on GitHub.

## 1. Create your own repository

On GitHub, create a new repository for your Movie Club project. Choose a lowercase repository name such as `filmiklubi`. Leave it empty: do not add a README, `.gitignore`, or licence yet. The generator will create the project files directly in this repository. Copy its HTTPS URL, then replace the example values below:

```bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
cd YOUR-REPOSITORY
git status
git remote -v
code .
```

An empty-repository warning during cloning is expected. `git clone` creates a local working copy with its Git connection already configured. `origin` should refer to the repository you just created. If `code .` is unavailable, use **File → Open Folder** in the editor.

**Checkpoint:** the editor has your empty repository folder open; `git remote -v` points to your GitHub repository. The course materials stay in the teacher’s repository.

## 2. Verify tools and generate the application

```bash
node --version
npm --version
git --version
```

Use a maintained Node.js LTS release compatible with the current generator and selected packages. Check the [official installation requirements](https://nextjs.org/docs/app/getting-started/installation) rather than relying on an old minimum version. Record the tested version in your application setup instructions. If the course uses nvm, record it in `.nvmrc` as well.

From the repository root:

```bash
npx create-next-app@latest . --disable-git
```

`npx` runs a package command. `@latest` selects the current stable generator. `.` means the current folder, so the application is created directly in the cloned repository. `--disable-git` preserves the existing Git setup.

When asked **“Would you like to use the recommended Next.js defaults?”**, select **“No, customize settings”**. Use the arrow keys to move between choices and press Enter to confirm.

Go through the selections one at a time:

| Question | Select |
|---|---|
| Use the recommended defaults? | **No, customize settings** |
| Use TypeScript? | **Yes** |
| Which linter? | **ESLint** |
| Use React Compiler? | **No** |
| Use Tailwind CSS? | **No** |
| Put code inside a `src/` directory? | **No** |
| Use App Router? | **Yes** |
| Use Cache Components? | **No** |
| Customize the import alias? | **No** — keep `@/*` |
| Include `AGENTS.md`? | **No** |
| Prepare anonymized agent feedback? | **No** |

The wording and order can vary by version. Choosing custom settings is essential: accepting the recommended defaults enables Tailwind. If offered a bundler choice, keep the default.

Commit `package-lock.json` so everyone can reproduce the selected dependency versions with `npm ci`.

```bash
npm run dev
```

Open http://localhost:3000. Keep the server running in this terminal; use another terminal for other commands. Stop it with **Ctrl+C**.

**Checkpoint:** the starter page loads without errors. Run all subsequent npm commands from this same repository root.

## 3. Understand the generated files

| Path | Responsibility |
|---|---|
| `app/page.tsx` | Home route `/` |
| `app/layout.tsx` | Shared root HTML structure and page wrapper |
| `public/` | Public static files |
| `package.json` | Dependencies and scripts |
| `package-lock.json` | Exact dependency resolution |
| `tsconfig.json` | TypeScript settings and import alias |
| `next.config.ts` | Next.js configuration |
| `eslint.config.mjs` | Code checks |
| `.gitignore` | Generated and local files excluded from Git |

`node_modules/` contains installed packages. `.next/` contains generated output. Do not edit these folders or commit them.

Read the generated `scripts` in `package.json`: `dev` runs the development server, `build` prepares production output, and `start` runs that output after a successful build. Keep the generator’s configuration intact.

## 4. Write a first page

Replace `app/page.tsx` with:

```tsx
export default function HomePage() {
  return (
    <main>
      <h1>Movie Club</h1>
      <p>Choose our next movie together!</p>
    </main>
  );
}
```

A React component is a function that returns JSX. `.tsx` permits JSX in a TypeScript file. `export default` exposes the page component to Next.js.

**Checkpoint:** changing the heading updates the browser.

## 5. Add the shared layout

Use semantic HTML and the browser’s default appearance. Styling is covered in another course.

The generated starter may include CSS files and font setup. The replacement layout below removes those imports and font settings. Delete the unused `app/globals.css` and `app/page.module.css` files if they exist, after replacing the starter page and layout with these examples.

Replace `app/layout.tsx` with:

```tsx
import type { Metadata } from "next";
import Link from "next/link";

export const metadata: Metadata = {
  title: "Movie Club",
  description: "Movie club example application",
};

export default function RootLayout({
  children,
}: Readonly<{
  children: React.ReactNode;
}>) {
  return (
    <html lang="en">
      <body>
        <header>
          <nav>
            <ul>
              <li><Link href="/">Movie Club</Link></li>
              <li><Link href="/movies">Movies</Link></li>
            </ul>
          </nav>
        </header>

        {children}

        <footer>
          Movie club example project
        </footer>
      </body>
    </html>
  );
}
```

`children` is the active page. The header and footer surround it on both routes. `Link` handles application navigation. `metadata` supplies the browser title and description. The Movies link will work after the next step.

Add a navigation link to `app/page.tsx`:

```tsx
import Link from "next/link";

export default function HomePage() {
  return (
    <main>
      <h1>Movie Club</h1>
      <p>
        Choose our next movie together!
      </p>
      <Link href="/movies">Browse movies</Link>
    </main>
  );
}
```

## 6. Add the movies route

Create `app/movies/page.tsx`:

```tsx
export default function MoviesPage() {
  return (
    <main>
      <h1>Movies</h1>
      <p>Our movie list will appear here.</p>
    </main>
  );
}
```

The folder name creates the URL segment: `app/page.tsx` maps to `/`, and `app/movies/page.tsx` maps to `/movies`.

**Checkpoint:** http://localhost:3000/movies loads and both navigation links work.

Replace the file with a list rendered from an array:

```tsx
const movies = [
  { id: 1, title: "Interstellar", year: 2014 },
  { id: 2, title: "Parasite", year: 2019 },
  { id: 3, title: "Dune", year: 2021 },
];

export default function MoviesPage() {
  return (
    <main>
      <h1>Movies</h1>

      <div>
        {movies.map((movie) => (
          <article key={movie.id}>
            <h2>{movie.title}</h2>
            <p>Year: {movie.year}</p>
          </article>
        ))}
      </div>
    </main>
  );
}
```

`map()` produces a card for each movie. `{movie.title}` embeds a JavaScript value in JSX. A stable `key` helps React identify list items; use the movie ID rather than the array position.

## 7. Separate data and presentation

Create `lib/movies.ts`:

```ts
export type Movie = {
  id: number;
  title: string;
  year: number;
};

export const movies: Movie[] = [
  { id: 1, title: "Interstellar", year: 2014 },
  { id: 2, title: "Parasite", year: 2019 },
  { id: 3, title: "Dune", year: 2021 },
];
```

`Movie` describes one item; `Movie[]` is an array of items. `export` makes values and types available to other files.

Create `components/MovieCard.tsx`:

```tsx
import type { Movie } from "@/lib/movies";

type MovieCardProps = {
  movie: Movie;
};

export default function MovieCard({ movie }: MovieCardProps) {
  return (
    <article>
      <h2>{movie.title}</h2>
      <p>Year: {movie.year}</p>
    </article>
  );
}
```

The `movie` prop supplies data to the component. `@/` refers to the application root under the settings chosen above.

Replace `app/movies/page.tsx` with:

```tsx
import MovieCard from "@/components/MovieCard";
import { movies } from "@/lib/movies";

export default function MoviesPage() {
  return (
    <main>
      <h1>Movies</h1>

      <div>
        {movies.map((movie) => (
          <MovieCard key={movie.id} movie={movie} />
        ))}
      </div>
    </main>
  );
}
```

Responsibilities now have separate homes: `lib/movies.ts` holds typed fixtures, `MovieCard` renders one item, and the route renders the list. No database or browser state is involved yet.

## 8. Complete the independent tasks

Follow the [session tasks](README.md#independent-work): add a fourth movie, add `genre: string` to the type and every fixture, display the genre in the card, document code conventions, and trace a request. Use English labels such as `Genre:` in your application.

## 9. Verify and save

From your repository root:

```bash
npm run lint
npm run build
```

Use the generated lint script; if it is missing, check the selected ESLint setup before proceeding. In the browser, check `/`, `/movies`, navigation, shared header/footer, and all movie fields.

From the same repository root:

```bash
git status
git add .
git diff --cached --stat
git commit -m "Create Movie Club Next.js app"
git push
```

Review staged files before committing: no `node_modules/`, `.next/`, or secrets. Push to your own repository, verified in step 1. If Git requests author details, configure your own name and email; do not copy someone else’s identity.

**Final checkpoint:** the application builds, both routes work, and your GitHub repository contains the application source and lockfile. Stop here before session 2.

## Troubleshooting

| Symptom | Check |
|---|---|
| `node` or `npm` not found | Install Node.js and reopen the terminal. |
| `package.json` not found | Run npm commands from the repository root containing `package.json`. |
| `/movies` returns 404 | Check the exact path `app/movies/page.tsx`. |
| Import cannot be resolved | Check case, file names, and the `@/*` alias. |
| TypeScript says `genre` is missing | Add it to every movie fixture. |
| Port 3000 is occupied | Read the URL printed by the development server, or stop the other server. |

## References

- [Next.js installation](https://nextjs.org/docs/app/getting-started/installation)
- [Next.js project structure](https://nextjs.org/docs/app/getting-started/project-structure)
- [Next.js layouts and pages](https://nextjs.org/docs/app/getting-started/layouts-and-pages)
- [React: describing the UI](https://react.dev/learn/describing-the-ui)
