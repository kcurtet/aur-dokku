# aur-dokku

Personal AUR overlay for [Dokku](https://dokku.com) on Arch/CachyOS, pinned
at **0.38.34-1**. The AUR package (`dokku`) fails to build at this version
because `cron_command.patch` no longer applies — upstream refactored
`plugins/cron/crontab.go` to use a `crontabUser` variable and the hardcoded
`-u dokku` strings the patch targeted are gone.

## Changes vs AUR

- Dropped `cron_command.patch` (obsolete; no longer referenced in `prepare()`)
- Removed its orphaned `sha256sums` entry
- `package()` installs the sudoers rules the scheduler needs
  (`/etc/sudoers.d/dokku`, from upstream `docker/etc/sudoers.d/dokku-docker`)
  and fixes the events plugin's Debian `syslog`-user assumption. Without
  these, every deploy dies in checks (exit 143) right after a clean install.

## Build

```bash
makepkg -si
# or
paru -U .
```

## Post-install

The package handles sudoers + events; still manual on Arch:

```bash
sudo dokku plugin:install --core
echo "$(<~/.ssh/id_kx.pub)" | sudo dokku ssh-keys:add admin
sudo dokku domains:set-global int.ntux.link
# hardened sshd must admit the deploy user:
sudo sed -i 's/^AllowUsers .*/& dokku/' /etc/ssh/sshd_config.d/50-hardening.conf
```

Bump: update `pkgver`, re-run `updpkgsums` (or fix hashes), `makepkg --printsrcinfo > .SRCINFO`.
