# Lab 6 · Mission: test what you ship, ship a release

## 1 · Smoke test

- **My step names:** `Build for the smoke test (amd64, kept on the runner)` and `Smoke test the image`.
- **What my smoke test asks:** It starts the image in a Docker container (`docker run -d -p 5000:5000`) and runs `curl http://localhost:5000/` to verify the API is responding.
- **What makes it fail:** If the API doesn't respond with a 200 OK, it prints the container's log (`docker logs smoke`) and exits with code 1.

## 2 · What broke and how I found it

- **The check and step that went red:** The `image` job failed on the `Smoke test the image` step.
- **The log line that showed the cause:** 
  `Error: Cannot find module './banner'`
- **The cause in my own words:** The teammate added a new `banner.js` file and required it in `server.js`, but they accidentally added `banner.js` to `api/.dockerignore`. This meant the file was excluded from the Docker build context, so the image was missing the file and crashed on startup.
- **Why the other three checks stayed green:** `lint`, `unit`, and `integration` run the source code directly on the GitHub runner, where `banner.js` exists. They do not use the Docker image, so they passed even though the image was broken.
- **My fix:** I removed the `banner.js` lines from `api/.dockerignore` and pushed the fix (`fix(api): keep banner.js in the image`).

## 3 · Release v0.2.0

- Release URL: https://github.com/ranbirsingh0091/turiba-devops-labs/releases/tag/v0.2.0
- Image: `ghcr.io/ranbirsingh0091/turiba-devops-labs/api:0.2.0`
- Platforms: `linux/amd64` and `linux/arm64`
