# R&R Tracker

A single-file web app for tracking responses to a revise and resubmit. It runs on GitHub Pages. Your data lives in a separate private repository as one JSON file, and the app only opens with a GitHub token that can write to that repository.

## How the lock works

- The app page (`index.html`) holds no data. It can sit in a public repo.
- Your projects are saved to `rnr-tracker.json` in a **private data repo**.
- On first visit, you enter a fine-grained personal access token. The app checks it against GitHub. Without a valid token, nothing loads.
- "Remember token on this device" stores the token in the browser's localStorage. Uncheck it on shared computers; the token then lasts only for that browser tab session.
- **Lock** (top right) clears the token and the local cache from the browser.
- Every save is a commit in the data repo, so the full history of your edits is recoverable from GitHub.

## Setup (about 10 minutes)

### 1. Create the private data repo
1. On GitHub, create a new repository, e.g. `rnr-data`.
2. Set it to **Private**.
3. Check **Add a README file** (the repo must not be empty).

### 2. Create a fine-grained token
1. GitHub > Settings > Developer settings > Personal access tokens > **Fine-grained tokens** > Generate new token.
2. Repository access: **Only select repositories** > choose `rnr-data`.
3. Permissions > Repository permissions > **Contents: Read and write**. Nothing else is needed.
4. Set an expiration (e.g. 1 year). Copy the token; GitHub shows it once.

### 3. Host the app
1. Create a repo for the app, e.g. `rnr-tracker` (public is fine, since it holds no data).
2. Upload `index.html` to the root.
3. Settings > Pages > Deploy from a branch > `main` / root > Save.
4. After a minute the app is at `https://<username>.github.io/rnr-tracker/`.

### 4. Unlock
- Access token: the token from step 2.
- Data repository: `<username>/rnr-data`.
- File path: leave `rnr-tracker.json`. Branch: leave blank to use the default.

The app creates the data file on first unlock.

## CSV import

Header names are matched loosely, so `Edit #`, `Reviewer`, `Section`, `Weight`, `Comment`, `Action`, `Response`, `Complete` all work, as do variants like "Reviewer comment" or "Difficulty". If the first row is not a recognizable header, columns are read in this order:

`Edit #, Reviewer, Section, Weight, Reviewer comment, Action taken, Response, Complete`

- Reviewer: `1`, `R1`, or `Reviewer 1` all become reviewer 1. Text like `Editor` is kept as is and sorts first in the letter.
- Weight: 1 to 3. Words like low/minor = 1 and high/major = 3. Missing = 2.
- Complete: yes, y, true, 1, x, or done count as complete.
- Missing edit numbers are filled in sequentially.
- Tab-separated and semicolon-separated files also work.

`sample-edits.csv` is a test file. The "CSV template" button in the app downloads a blank template.

## Progress modes

- **By count:** completed edits / total edits.
- **By weight:** completed weight points / total weight points. A weight-3 edit counts three times as much as a weight-1 edit.
- **Blend:** a slider mixes the two. 60% weight means 0.6 x weight progress + 0.4 x count progress.

## Response letter

The letter groups edits by reviewer (Editor first, then Reviewer 1, 2, ...) and orders them by edit number. Options: quote each reviewer comment, note the section, and choose numbering (edit numbers, sequential, or restarting per reviewer). Export produces an `.rtf` that opens in Word, Pages, and Google Docs. "Copy formatted text" puts a formatted version on the clipboard.

## Notes

- Saves happen automatically about 2 seconds after you stop typing. The status pill shows Saved, Unsaved changes, or an error.
- If you edit on two devices at once, the second save reports a conflict. Click the pill to choose which version to keep.
- Backup downloads the whole data file as JSON. Restore replaces everything from a backup.
