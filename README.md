name: Profile Visual System

on:
  schedule:
    - cron: "0 7 * * *"

  workflow_dispatch:

permissions:
  contents: write

jobs:

  # ==========================================================
  # 3D CONTRIBUTION MATRIX
  # ==========================================================

  generate-3d:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v5

      - name: Generate 3D Contribution Graph
        uses: yoshi389111/github-profile-3d-contrib@latest

        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          USERNAME: ${{ github.repository_owner }}

      - name: Commit 3D Graph
        run: |

          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"

          git add profile-3d-contrib

          if git diff --cached --quiet; then
            echo "No changes detected."
          else
            git commit -m "chore: update 3D contribution graph"
            git push
          fi


  # ==========================================================
  # CONTRIBUTION SNAKE
  # ==========================================================

  generate-snake:

    runs-on: ubuntu-latest

    steps:

      - name: Generate Contribution Snake
        uses: Platane/snk/svg-only@v3

        with:

          github_user_name: ${{ github.repository_owner }}

          outputs: |

            dist/github-contribution-grid-snake.svg

            dist/github-contribution-grid-snake-dark.svg?palette=github-dark


      - name: Push Snake To Output Branch
        uses: crazy-max/ghaction-github-pages@v5

        with:

          target_branch: output
          build_dir: dist

        env:

          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
