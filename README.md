# Sync Brewfiles to Workbrew

Automate your Brewfiles using GitOps workflow. Use this GitHub Action to keep your Brewfiles in sync with your Workbrew workspace. Push changes to your repo, and your Brewfiles follow—no manual steps, no drift.

## How it works

This Action treats your repository as the source of truth. The action fetches all Brewfiles from Workbrew and compares them to the files in your repo. If a Brewfile is new or changed, it updates Workbrew. If a Brewfile is gone from your repo, it deletes it from Workbrew.

Device targeting is automatic: add a comment at the top of your Brewfile to target a device group or serial numbers. If you don’t, it defaults to no devices. Example Brewfiles:

1. Target all devices

```ruby
# device_serial_numbers: all
brew "curl"
cask "1password"
```

2. Target a device group

```ruby
# device_group_id: e6c10d0-0b13-554c-b976-a05d8a18f0cc
brew "curl"
cask "1password"
```

3. Target specific devices by serial numbers[^1]

```ruby
# device_serial_numbers: AB3456DG90,1234567890
brew "curl"
cask "1password"
```

4. Default behavior (no targeting)

```ruby
brew "curl"
cask "1password"
```

When no device targeting directive is specified, the action treats it as `# device_serial_numbers: "none"`.

## Repository structure

```
your-repo/
├─ brewfiles/
│  ├─ Brewfile-developers
│  ├─ Brewfile-marketing
├─ .github/
│  ├─ workflows/
│  │  ├─ sync-brewfiles.yml
```

## Triggers

You can run this Action on any GitHub Actions trigger. The best way is to run it on every push to your main branch, but only when Brewfiles change. Here’s the recommended setup:

```yaml
name: Sync Brewfiles

on:
  push:
    branches:
      - main
    paths:
      - 'brewfiles/**'

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: workbrew/sync-brewfiles-action@1.0.0
        with:
          api-token: ${{ secrets.WORKBREW_API_TOKEN }}
          workspace-name: ${{ secrets.WORKBREW_WORKSPACE_NAME }}
          brewfiles-dir: brewfiles
```

You can also trigger it manually or on a schedule. But for most teams, syncing on push is all you need.

Are you a Workbrew customer struggling to implement this action? Contact your account manager and we're happy to help.
Not a Workbrew customer yet? [Reach out to talk about becoming one](https://workbrew.com/contact).

[^1]: You can get device serial numbers from either [the API](https://console.workbrew.com/documentation/api#:~:text=Returns%20a%20list%20of%20Device%20Groups) (Pro & Enterprise) or by manually exporting on the Device Groups page (Enterprise only).
