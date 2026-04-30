# Principled Protection Against Leaky Web Caches

## System Requirements

1. Ensure these basic requirements are met
- requires `Go` to build Caddy
- `curl` for http requests
- PHP
- Docker

1. Download the sources

```sh
# clone WebCacheDeputy/ with submodules
git clone --recurse-submodules git@github.com:webcachedeputy/WebCacheDeputy.git
```

Or run `git submodule sync` after cloning the main repository.

- You might need a GitHub Account
- You should create a personal access token on GitHub via Settings -> Developer Settings -> Personal access tokens -> Fine-grained personal access tokens. Click "Create new token", give it a name and create the token.
- When running the clone command, you might be prompted to enter username and password for GitHub. Use your GitHub username and the personal access token as the password


The resulting directory structure should look like this.
The important part is that the webcachedeputy/webcachedeputy-souin and caddy-webcachedeputy-cache-handler repositories that come from the submodules, are placed under `src/`.

```
.
├── artifact-start.sh
├── artifact-stop.sh
├── artifact-test.sh
├── README.md
└── src
    ├── php
    ├── caddy
    ├── caddy-patched
    ├── build-caddy.sh
    ├── start-patched-caddy.sh
    ├── start-sample-server.sh
    ├── README.md
    ├── caddy-webcachedeputy-cache-handler/
    └── webcachedeputy-souin/
```

## Fast Walkthrough
Run the fast walkthrough using three scripts.

| Script                | Purpose                    | Duration |
| --------------------- | -------------------------- | -------- |
| `./artifact-start.sh` | Build everything and start | minutes  |
| `./artifact-test.sh`  | Run tests                  | seconds  |
| `./artifact-test.sh`  | Stop                       | seconds  |

- Execute `./artifact-start.sh` which starts our sample backend, builds the patched Caddy and starts it. This can take a few minutes on the first build.
- Run HTTP requests to test the setup via `./artifact-test.sh`. You should see:
  - All tests should print `[PASS]` 
  - These tests check correct behavior for public, public static, private static, private dynamic resources
- Stop everything, by calling `./artifact-stop.sh`

![Expected Output](./screenshot.png)

### Overview of Changes

- We patched `caddy-cache-handler` (→ `caddy-webcachedeputy-cache-handler`), specifically configuration.go to enable custom configuration commands in Caddyfile to control WebCacheDeputy, e.g., `wcdp_session_info` and `wcdp_upstream_host`.
- We patched `souin`(→ `webcachedeputy-souin`) to add support for the custom WCDPSettings from the Caddyfile and `pkg/middleware/` to incorporate `wcdp_settings`,  and implemented the WebCacheDeputy logic in `middleware.go`
- We patched caddy/ to replace `souin` with `webcachedeputy-souin`, and `cache-handler` with `caddy-webcachedeputy-cache-handler`

#### Our Environment
This artifact was produced and tested on an Intel MacBook Pro running MacOS Tahoe Version 26.2.
The system uses a 2,4 GHz 8-Core Intel Core i9 CPU and 16 GB of RAM.

