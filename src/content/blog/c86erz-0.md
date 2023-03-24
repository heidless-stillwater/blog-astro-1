---
author: Rob Craig
pubDatetime: 2023-01-30T15:57:52.737Z
title: c86erx - grass roots community support
postSlug: c86er-intro
featured: true
ogImage: https://user-images.githubusercontent.com/53733092/215771435-25408246-2309-4f8b-a781-1f3d93bdf0ec.png
tags:
  - release
description: c86erz - grass woots communtiy support
---

# c86erz - grass roots community support

<img src="https://storage.cloud.google.com/frontend-bucket-0/sdsh/c86erz-roundel.png?authuser=0" width=500 alt="c86erz roundel" />

Born in Haringey, London the C86ERZ regularly asseble a crew to ride-out and deliver practical support to the homeless of the Borough.

Since inception, the network has swiftly grown though inspiring similar groups in EVERY London Borough.

Growth continues. Impact Continues. Need Grows. More is Needed.

I have the honour of managing the website. The site is aimed to promote and enable the initiative and the teams.

Visit the Website: https://community.mimeworks.com/

Founder, Paul-Verral Walcott was featured in a compelly profile on The Russell Howard Hour.

![c86erz - Russell Howard Hour](https://storage.cloud.google.com/frontend-bucket-0/sdsh/russellhoward.jpg?authuser=0)

View Profile: https://community.mimeworks.com/c86erz-russel-howard-hour/


<hr />
<hr />
<hr />
<hr />
<hr />
<hr />
<hr />
<hr />
<hr />
<hr />

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
