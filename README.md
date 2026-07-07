# My Instagram Feed (GitHub Pages)

A static site that shows your latest Instagram posts, kept up to date by a
scheduled GitHub Action — no server required.

## How it works

- `.github/workflows/fetch-instagram.yml` runs on a daily schedule (and can
  be triggered manually), calls the Instagram API, and saves the results to
  `data/posts.json`, committing that file back to the repo.
- `index.html` is a plain static page that reads `data/posts.json` and
  renders the posts in a grid. GitHub Pages serves this file directly.

## One-time setup

1. **Get a long-lived Instagram access token.** In your Meta app, go to
   Instagram → "API setup with Instagram login" → make sure your account is
   added as an Instagram Tester (Roles tab) and has accepted the invite on
   Instagram's side → click "Generate Token" in the "Generate access tokens"
   section. Tokens from this flow are already long-lived (60 days) — no
   extra exchange step needed. Copy it immediately; the dashboard only
   shows it once.

2. **Add the token as a GitHub secret**:
   - In your repo: Settings → Secrets and variables → Actions → New repository secret
   - Name: `IG_ACCESS_TOKEN`
   - Value: paste your long-lived token

3. **Enable GitHub Pages**:
   - Settings → Pages → Source → Deploy from branch → select `main` / root

4. **Run the workflow once manually** to populate `data/posts.json` right
   away instead of waiting for the daily schedule:
   - Actions tab → "Fetch Instagram Posts" → Run workflow

## Token expiry — the one thing you must not forget

Long-lived Instagram tokens expire after **60 days**. This setup does not
auto-refresh the token for you (that requires extra permissions). Every
~50 days:

1. Go back to your Meta app's "API setup with Instagram login" page.
2. Click "Generate Token" again next to your Instagram account — it
   generates a fresh 60-day token immediately.
3. Update the `IG_ACCESS_TOKEN` secret in GitHub with the new value.

If the token expires, the workflow will fail loudly (check the Actions tab)
rather than silently break your feed.

## Customizing

- Change how often it fetches: edit the `cron` line in
  `fetch-instagram.yml` (currently daily at 06:00 UTC).
- Change how many posts show: edit `limit=12` in the same file.
- Styling: edit the `<style>` block in `index.html`.
