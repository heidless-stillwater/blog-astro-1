---
author: heidless
pubDatetime: 2023-01-30T15:57:52.737Z
title: Deploy 2 Google nextJS/dJango/POSTGRES
postSlug: pfolio-deploy-google-cloud 
featured: true
ogImage: https://user-images.githubusercontent.com/53733092/215771435-25408246-2309-4f8b-a781-1f3d93bdf0ec.png
tags:
  - release
description: Walkthrough deploying to Google Cloud. NextJS frontend with dJango backend & Postgres instance
---


# Deploy a React Web Application to Google Cloud
https://www.youtube.com/watch?v=IjUnQ9kMnVo

## Access app
### https://pfolio-frontend-service-7dd5pbcoiq-nw.a.run.app/

## setup

### package.json

```
# ensure 'start' references $PORT
# allows cloud run to manage random PORT
 "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start -p $PORT",
    "lint": "next lint"
  },
  
# test by specifying port
npm run build
npm start 5000
```


## docker
### Dockerfile
```
vi Dockerfile
# base image
FROM node:16.15.1-alpine

# Create and change to the app directory.
WORKDIR /usr/app

COPY . .

# Install production dependencies.

# use 'clean install' (ci)
RUN npm ci --only=production

RUN npm run build

CMD ["npm", "start"]
```

### build | run
```
docker build . -t cloudrun-app-alpine:latest
docker run -p 3000:3000 -e=PORT=3000 cloudrun-app-alpine
```

## storage bucket
```
# project
pfolio-frontend-deploy-0

# storage bucket
'Cloud Storage'
-
frontend-bucket-0
-
```


## Cloud Run
### configure
```
https://console.cloud.google.com/run/create?enableapi=true&authuser=0&hl=en_GB&project=pfolio-backend-deploy-0-380215

'Cloud Run'->Create Service
-
pfolio-frontend-service
'Setup Cloud Build'
< pick repository >
'save'
-

# register container
'Container Registry'-> enable if not already done so

# service account
'IAM & ADMIN'->Service Accounts
-
NAME: nextjs-cloudrun-app-bot
DESC: service account for github actions
ROLES:
Editor
Cloud Run Admin
Storage Admin
Service Account User
'DONE'

# newly created instance
' 3 dots'->Manage Keys->Add Key->JSON

# keyfile auto downloads
# copy to project root dir
i.e. pfolio-backend-deploy-0-380215-8f9cc9423783.json

# enable Registry API - IF NEEDED
'Container Registery'-'enable'

# ename Cloud Run API - if NECESSARY
'Cloud Run API'->'enable'

```

## configure github workflows
```
# in project root dir
vi .github/workflows/cloud-run.yml
-
europ-west2
-

# refresh gitub
git add .
git commit -m "added .github structure"

# github secrets
# encode service account key
i.e.base64 -i pfolio-backend-deploy-0-380215-8f9cc9423783.json

<github repository>->Settings->Secrets & Variables->Actions
'New Repository Secret'
-
CLOUD_RUN_PROJECT_NAME=pfolio-frontend-deploy-0
CLOUD_RUN_SERVICE_ACCOUNT=`base64 -i pfolio-backend-deploy-0-380215-8f9cc9423783.json`
-

# now trigger a build to confirm success/failure
<github repository>->Actions-><workflow of interest>
```

## refresh install of SDK
https://cloud.google.com/sdk/docs/install#deb
```
sudo apt-get install apt-transport-https ca-certificates gnupg

echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key --keyring /usr/share/keyrings/cloud.google.gpg add -

sudo apt-get update && sudo apt-get install google-cloud-cli

```

## Cloud Run: enable permissions
```
'Cloud Run'-><app service>->Triggers->Allow Unauthenticated invocations

gcloud run services add-iam-policy-binding nextjs-test \
    --member="allUsers" \
    --role="roles/run.invoker"
```

## continuous deployment
```
'Cloud Run'->Service Details->Setup Continuous Deployment
-
'Enable Cloud Build API'
<set repository>
-
```

## access frontend app
```
PROJECT: pfolio-frontend-deploy-0->Cloud Run->pfolio-frontend-service
```
https://pfolio-frontend-service-7dd5pbcoiq-nw.a.run.app


## backups
### frontend
```
STORAGE BUCKET: pfolio-frontend-deploy-0->'Cloud Storage'
-
frontend-bucket-0
-
```
























Astro 2.0 has been released with some cool features, breaking changes, DX improvements, better error overlay and so on. AstroPaper takes advantage of those cool features, especially Content Collections API.

<!-- ![Introducing AstroPaper 2.0](https://user-images.githubusercontent.com/53733092/215683840-dc2502f5-8c5a-44f0-a26c-4e7180455056.png) -->

![Introducing AstroPaper 2.0](https://user-images.githubusercontent.com/53733092/215771435-25408246-2309-4f8b-a781-1f3d93bdf0ec.png)

## Table of contents

## Features & Changes

### Type-safe Frontmatters and Redefined Blog Schema

Frontmatter of AstroPaper 2.0 markdown contents are now type-safe thanks to Astro’s Content Collections. Blog schema is defined inside the `src/content/_schemas.ts` file.

### New Home for Blog contents

All the blog posts were moved from `src/contents` to `src/content/blog` directory.

### New Fetch API

Contents are now fetched with `getCollection` function. No relative path to the content needs to be specified anymore.

```ts
// old content fetching method
- const postImportResult = import.meta.glob<MarkdownInstance<Frontmatter>>(
  "../contents/**/**/*.md",);

// new content fetching method
+ const postImportResult = await getCollection("blog");
```

### Modified Search Logic for better Search Result

In the older version of AstroPaper, when someone search some article, the search criteria keys that will be searched are `title`, `description` and `headings` (heading means all the headings h1 ~ h6 of the blog post). In AstroPaper v2, only `title` and `description` will be searched as the user types.

### Renamed Frontmatter Properties

The following frontmatter properties are renamed.

| Old Names | New Names   |
| --------- | ----------- |
| datetime  | pubDatetime |
| slug      | postSlug    |

### Default Tag for blog post

If a blog post doesn't have any tag (in other words, frontmatter property `tags` is not specified), the default tag `others` will be used for that blog post. But you can set the default tag in the `/src/content/_schemas.ts` file.

```ts
// src/contents/_schemas.ts
export const blogSchema = z.object({
  // ---
  // replace "others" with whatever you want
  tags: z.array(z.string()).default(["others"]),
  ogImage: z.string().optional(),
  description: z.string(),
});
```

### New Predefined Dark Color Scheme

AstroPaper v2 has a new dark color scheme (high contrast & low contrast) which is based on Astro's dark logo. Check out [this link](https://astro-paper.pages.dev/posts/predefined-color-schemes#astro-dark) for more info.

![New Predefined Dark Color Scheme](https://user-images.githubusercontent.com/53733092/215680520-59427bb0-f4cb-48c0-bccc-f182a428d72d.svg)

### Automatic Class Sorting

AstroPaper 2.0 includes automatic class sorting with [TailwindCSS Prettier plugin](https://tailwindcss.com/blog/automatic-class-sorting-with-prettier)

### Updated Docs & README

All the [#docs](https://astro-paper.pages.dev/tags/docs/) blog posts and [README](https://github.com/satnaing/astro-paper#readme) are updated for this AstroPaper v2.

## Bug Fixes

- fix broken tags in the Blog Post page
- in a tag page, the last part of the breadcrumb is now updated to lower-case for consistency
- exclude draft posts in a tag page
- fix 'onChange value not updating issue' after a page reload
