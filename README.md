# vijitsaxena.github.io

Personal blog built with [Hugo](https://gohugo.io/) and the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme. Published at https://vijit-saxena.github.io/.

## Requirements

- Hugo v0.157.0 (extended)

## Setup

1. Install Hugo (extended build):
   ```bash
   brew install hugo
   ```

2. Clone the repository:
   ```bash
   git clone https://github.com/VijitSaxena/vijitsaxena.github.io.git
   cd vijitsaxena.github.io
   ```

## Run locally

Start the development server:
```bash
hugo server
```

The site will be available at http://localhost:1313.

To preview draft posts as well:
```bash
hugo server -D
```

## Create a new post

```bash
hugo new content/posts/my-post.md
```

## Build

Generate the static site into the `public/` directory:
```bash
hugo
```
