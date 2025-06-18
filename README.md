<pre> <!--NEOFETCH_START--> updating... <!--NEOFETCH_END--> </pre>


---

### ⚙️ 3. Add GitHub Action to auto-update it with Neofetch

Inside the repo, go to:

`/.github/workflows/neofetch.yml`

And paste this:

```yaml
name: Update Neofetch Output

on:
  schedule:
    - cron: '0 6 * * *'  
  workflow_dispatch:  

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Install neofetch
        run: sudo apt-get update && sudo apt-get install -y neofetch

      - name: Run neofetch and update README
        run: |
          NEOFETCH_OUTPUT=$(neofetch --stdout)
          awk -v output="$NEOFETCH_OUTPUT" '
            /<!--NEOFETCH_START-->/ {print; print output; skip=1; next}
            /<!--NEOFETCH_END-->/ {skip=0}
            skip == 0
          ' README.md > temp.md
          mv temp.md README.md

      - name: Commit and push
        run: |
          git config --global user.name 'github-actions[bot]'
          git config --global user.email 'github-actions[bot]@users.noreply.github.com'
          git add README.md
          git commit -m "🖥️ update neofetch output" || echo "No changes"
          git push
