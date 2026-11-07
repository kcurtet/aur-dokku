# aur-dokku

Personal AUR overlay for [Dokku](https://dokku.com) on Arch/CachyOS, pinned
at **0.38.34-1**. The AUR package (`dokku`) fails to build at this version
because `cron_command.patch` no longer applies — upstream refactored
`plugins/cron/crontab.go` to use a `crontabUser` variable and the hardcoded
`-u dokku` strings the patch targeted are gone.

## Changes vs AUR

- Dropped `cron_command.patch` (obsolete; no longer referenced in `prepare()`)
- Removed its orphaned `sha256sums` entry

## Build

```bash
makepkg -si
# or
paru -U .
```

## Post-install

```bash
echo "$(<~/.ssh/id_kx.pub)" | sudo dokku ssh-keys:add admin
sudo dokku domains:set-global int.ntux.link
```

Bump: update `pkgver`, re-run `updpkgsums` (or fix hashes), `makepkg --printsrcinfo > .SRCINFO`.
