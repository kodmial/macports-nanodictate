# macports-nanodictate

MacPorts port tree for [NanoDictate](https://github.com/kodmial/nanodictate) —
a macOS dictation tool (double-Alt, bilingual EN/RU, 4 STT providers).

This is a **binary port**: the prebuilt release tarball is installed as-is —
no Xcode / Swift toolchain needed. The install registers the background agent
(`com.nanodictate.agent`) as a global LaunchAgent and starts it, so dictation
works right after `port install`.

Layout (standard MacPorts ports-tree layout):

```
audio/nanodictate/
├── Portfile             # real generated Portfile for v0.0.3 (release script output)
└── config.example.toml  # canonical defaults, copied to ~/.config/nanodictate/config.toml on first launch
```

## Install on a clean Mac with MacPorts

NanoDictate is not in the official MacPorts tree, so register this GitHub repo
as a **git port source**. MacPorts has no `git://` scheme — the supported way
is a local `git clone` exposed through a `file://` source: `port selfupdate`
then auto-updates it with `git pull`. Update the tree with
`sudo port selfupdate` any time you want the latest Portfile.

1. Clone the port tree:

   ```sh
   sudo mkdir -p /Users/Shared/macports-nanodictate
   sudo chown "$USER":admin /Users/Shared/macports-nanodictate
   git clone https://github.com/kodmial/macports-nanodictate /Users/Shared/macports-nanodictate
   # Selfupdate runs git as root; hand the tree to root so git >=2.35.2
   # (dubious ownership) does not refuse the pull.
   sudo chown -R root:admin /Users/Shared/macports-nanodictate
   ```

2. Register the source in `/opt/local/etc/macports/sources.conf` — add this
   line **above** the default `rsync://` line:

   ```
   file:///Users/Shared/macports-nanodictate
   ```

3. Sync and install:

   ```sh
   sudo port selfupdate && sudo port install nanodictate
   ```

## Update

```sh
sudo port selfupdate && sudo port upgrade nanodictate
```

`selfupdate` pulls the latest Portfile from this repo (`git pull`), then the
upgrade reinstalls the newest NanoDictate release.

## Uninstall

```sh
sudo port uninstall nanodictate
```

`pre-deactivate` fully tears the service down: the running agent is unloaded
(`launchctl bootout`) and the global plist
`/Library/LaunchAgents/com.nanodictate.agent.plist` is deleted — a single
`sudo port uninstall` leaves no trace, no manual `sudo rm` step.

## Source note

Because MacPorts requires a signed tarball for `http(s)` sources and has no
live `git://` scheme, the `file://` + git clone form is the only supported way
to use a GitHub repo as a source. Do not put `https://github.com/...` or
`git://github.com/...` lines in `sources.conf` — they are rejected at
`port selfupdate`.