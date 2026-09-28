# sclip

Copy a remote file to your local clipboard with one command, run on your
**local computer**:

```sh
sclip user@server:/path/to/remote/file
```

It runs `cat` on the server through SSH and sends the contents to local `xclip`.
The contents stay in memory; no temporary file is saved. Use it from Tilix on
your local X11 desktop. It works independently of any tmux sessions on the server.

## Install

Requires Python 3, OpenSSH's `ssh`, and `xclip` on your local computer. The remote
server only needs its usual shell and `cat`.

From this directory on your local computer:

```sh
mkdir -p "$HOME/.local/bin"
install -m 755 sclip "$HOME/.local/bin/sclip"
```

The server is required on every call; no default host or configuration is saved.
Use the destination you normally pass to `ssh`. SSH config aliases work, including
their configured username, port, identity file, and jump host.

If `~/.local/bin` is not already in your `PATH`, add this to your local shell's
startup file (`~/.bashrc` for Bash or `~/.zshrc` for Zsh), then reload it:

```sh
export PATH="$HOME/.local/bin:$PATH"
```

## Usage

```sh
sclip user@server:/etc/hostname
sclip -p 2222 user@server:/etc/hostname
sclip --port 2222 user@server:/etc/hostname
sclip "myserver:/home/me/path with spaces/notes.txt"
sclip 'myserver:~/notes.txt'
sclip 'user@[2001:db8::1]:/etc/hostname'
sclip --help
```

Use `-p` or `--port` to specify the SSH port (1–65535). If omitted, SSH uses
the port from your SSH config, or port 22 when none is configured.

The source uses `[user@]host:path` syntax, including SSH aliases and bracketed
IPv6 addresses. Quote the whole source when the path contains spaces or shell
metacharacters. Colons in the path are preserved.

Relative paths start from the remote SSH login directory, usually your remote
home. A leading `~/` explicitly refers to your remote home directory. Other path
characters are treated literally; wildcards and `~otheruser` are not expanded.

Successful copying is silent. SSH or remote read failures leave your clipboard
unchanged. Bytes and trailing newlines are preserved. The entire file is buffered
in memory before being passed to `xclip`.

References: [OpenSSH remote commands](https://man.openbsd.org/ssh),
[xclip documentation](https://github.com/astrand/xclip).
