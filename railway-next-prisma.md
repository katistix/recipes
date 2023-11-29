## Railway.app - Next.js + Prisma (Complete CI/CD Pipeline)

Deploying an app on Railway.app is a breeze, here's a quick guide to get you started and what files you need to add to your project.

### Prerequisites

-   Create a new project on Railway.app
-   Add the Github repo to your Railway.app project
-   Add a new database service/plugin to your Railway.app project

### 0. Add the following secrets to your Github repo

-   `RAILWAY_TOKEN` - Your Railway.app API token
-   `RAILWAY_SERVICE` - The name of your Railway.app service (this is a string, not a UUID)

### 1. Enable on Check Suites for your Railway.app service

> If enabled, deployments are triggered after all GitHub Actions have run successfully. Make sure you have accepted our updated GitHub permissions required for this feature.

### 2. Add the railway workflow to your github repo, inside `.github/workflows/prod-migrations.yml`

This workflow will run the Prisma migrations before deploying your app to Railway.app

```yaml
# /.github/workflows/prod-migrations.yml
name: Prisma Migrate on Main Branch

# Only run this workflow on pushes (or PRs) to the main branch
on:
    push:
        branches:
            - main

jobs:
    migrate:
        runs-on: ubuntu-latest
        steps:
            - name: Check out the repository
              uses: actions/checkout@v2

            - name: Set up Node.js
              uses: actions/setup-node@v2
              with:
                  node-version: "20" # Specify the Node.js version

            - name: Install dependencies
              run: npm install

            - name: Install Railway
              run: npm i -g @railway/cli

            - name: Run migrations on Railway
              run: railway run -s $RAILWAY_SERVICE npx prisma migrate deploy
              env:
                  RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
                  RAILWAY_SERVICE: ${{ secrets.RAILWAY_SERVICE }}
```
