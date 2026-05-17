# ZoPhysicist — LaTeX → live site, automatically

You write a `.tex` file, commit it, and ~1–2 minutes later it is rendered
and live on your InfinityFree domain. No Markdown, no manifest, no manual
uploads. This guide has a one-time setup (Part 1–3) and then a 30-second
routine for every new lesson (Part 4).

---

## How it works (the short version)

```
you add  content/your-lesson.tex   ──commit──▶  GitHub Actions
                                                   │
                            Pandoc renders LaTeX→HTML, build.py
                            auto-creates the manifest from your headers
                                                   │
                                          FTP upload ▶ InfinityFree (htdocs)
                                                   │
                                        live on your domain
```

Topics are created automatically: whatever you type in
`% !ZP topic = Waves` becomes a "Waves" section in the site navigation.

---

## Part 1 — Put this project on GitHub (once, ~10 min)

1. Create a free account at github.com if you don't have one.
2. Click **New repository** → name it e.g. `zophysicist` → **Private** is
   fine → **Create**.
3. Easiest upload path: on the new repo page click
   **"uploading an existing file"**, then drag in **everything inside this
   folder** (the `content/`, `shell/`, `.github/` folders and
   `build.py`, `.gitignore`, `README.md`). Commit.
   - Keep the folder structure exactly. The `.github` folder is essential —
     if GitHub hides it, that's normal, it still uploads.

## Part 2 — Give GitHub your InfinityFree FTP details (once)

1. In the **InfinityFree control panel**, open **Account Details** /
   **FTP Accounts**. Note three things:
   - **FTP Server / Hostname** (looks like `ftpupload.net`)
   - **FTP Username** (looks like `epiz_12345678`)
   - **FTP Password** (the one you set; you can reset it there)
2. In your **GitHub repo** go to **Settings → Secrets and variables →
   Actions → New repository secret** and add exactly these three:

   | Secret name   | Value                          |
   |---------------|--------------------------------|
   | `FTP_SERVER`  | your FTP hostname              |
   | `FTP_USERNAME`| your FTP username              |
   | `FTP_PASSWORD`| your FTP password              |

   Secrets are encrypted; the workflow reads them, nobody can see them.

## Part 3 — First deploy (once)

1. In the InfinityFree **File Manager**, open `htdocs` and **delete the
   default placeholder file** (the default `index.html` / `index2.html`).
2. In GitHub, open the **Actions** tab → click the latest run → it builds
   and deploys. Green check = done. Visit your domain.
   - SSL (the padlock) can take a few hours to activate the first time —
     normal, not an error.

That's the whole setup. You never repeat Parts 1–3.

---

## Part 4 — Add a lesson (the everyday routine, ~30 sec)

1. In your repo, open the `content/` folder → open **`_TEMPLATE.tex`** →
   click the **"Copy raw file"** / create-new-file button, or just click
   **Add file → Create new file** and name it e.g. `waves-intro.tex`.
2. Paste the template, then edit the header lines and write your physics
   in LaTeX below the header:

   ```latex
   % !ZP title   = Introduction to Waves
   % !ZP type    = lecture
   % !ZP topic   = Waves
   % !ZP summary = Wavelength, frequency and the wave equation.
   % !ZP order   = 1

   \section*{Introduction to Waves}
   A wave transfers energy without transferring matter. The wave
   equation is
   \begin{equation}
     v = f\lambda
   \end{equation}
   ```
3. **Commit** (the green button). Done. The Actions tab will show it
   building; ~1–2 minutes later it is live, already in the navigation,
   already searchable.

You can do all of this from a phone browser too.

### The header fields

| Field        | Required | Notes |
|--------------|----------|-------|
| `title`      | yes | Page heading + search |
| `type`       | yes | `lecture`, `definition`, `problem`, or `solution` |
| `topic`      | yes | **Any text.** New text = new section auto-created |
| `summary`    | no  | One line on the browse card |
| `tags`       | no  | Comma-separated, used by search |
| `order`      | no  | Sort within the topic (low first) |
| `topicorder` | no  | Where the topic sits in the sidebar |
| `pair`       | no  | Same value on a problem + a solution links them |

**Linking a problem to its solution:** put `% !ZP pair = wav-01` on the
problem file *and* the same `% !ZP pair = wav-01` on the solution file.
The "View solution" / "Back to problem" buttons appear automatically.

**Mizo version (optional):** put a line that is exactly `%%% MIZO %%%` in
the body. Text above it = English, below it = Mizo. The EN/MIZO switch
shows the right one. Leave it out for English-only.

---

## Troubleshooting

**The Actions run is red (failed).** Click it → open the failed step.
`build.py` prints a plain-English reason, e.g.
`[waves-intro.tex] missing required header % !ZP title = ...` or a Pandoc
error pointing at the LaTeX it couldn't parse. Fix that file, commit again.
A failed build does **not** touch your live site — it stays as it was.

**Build is green but FTP step failed.** InfinityFree occasionally throttles
automated FTP. Two safety nets:
- Every run also attaches the finished site as a downloadable
  **`site` artifact** (top of the run page). You can download it and drag
  it into the InfinityFree File Manager manually as a one-off.
- Re-running the job usually succeeds (throttling is temporary).

**I updated a file but the site looks old.** Browser cache. Press
**Ctrl + F5** (hard refresh). Other visitors get it within ~1 hour.

**Preview locally before committing (optional).**
```bash
sudo apt-get install -y pandoc      # once
python3 build.py
cd site && python3 -m http.server 8080   # open http://localhost:8080
```

---

## Optional: free mirror as extra insurance

If you ever want a backup copy that doesn't depend on InfinityFree at all,
GitHub can host the exact same `site/` for free (GitHub Pages). Ask and a
few extra lines in the workflow enable it — your InfinityFree domain stays
the primary.

---

Built so that the only thing you ever think about is writing physics.
