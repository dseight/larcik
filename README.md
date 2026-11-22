# Larčik

Like a box, but small.

## What is this?

This is a small containerization solution, primarily aimed for running not so
trustworthy binaries inside of it.

When you open larcik, you will have only the following available inside:
- your work directory
- if the work directory happened to be a git worktree, .git/config and .git/hooks
  are going to be R/O
- some of your config files (R/O), such as `~/.vimrc`, `~/.gitconfig`, etc.

## Design

Larčik is inspired by [toolbx] and [devkit].

While toolbx makes containers extremely easy to use, just like a native system,
it lacks isolation (by design). devkit goes the opposite way: you only get your
worktree and nothing else (and also adds agents as the "first-class citizen").

While designing this, I wanted to have a tool that is somewhat middleground:
you get your worktree in the container, but not the surroundings. And apart
from the worktree you get some of your configuration files (like .vimrc), but
no sensitive data like ssh keys.

On top of that, one of the major things that is missing in the above solutions
is the NETWORK isolation. Larčik solves that by configuring nft tables in the
container's network namespace with OCI hooks, prior starting the container. For
example, you can get a container that only has access to web, but no access to
your home network. Or the opposite: local network, without web access.

Some basic principles of the project:
- no external dependencies (meaning no extra python packages)
- no sudo/root access
- network access is disabled by default

## Usage

Run the following command in a directory you want to start working with:

    larcik open

when container is no longer needed, you can close it with:

    larcik close

When starting larčik, you can limit network access. For example, you can say
that it's only allowed to access a couple of your machines in local net:

    larcik open --net ip:192.168.0.2,192.168.0.10

or that it can only access web, but not local network:

    larcik open --net web

## Configuration

### Global configuration

You can put a config file into `~/.config/larcik/larcik.cfg` to override the
defaults (including mounting of extra paths).

E.g., you can override what image is used by default:

```ini
image = ubuntu:26.10
```

Or what extra paths you want to mount:

```ini
[mount rw]
    # Mount /tmp from host system
    /tmp

[mount ro]
    /mnt
```

In mount entries it is allowed to use environment variables, enclosed in `{}`.
For example, this is how you can mount extra configs from home directory:

```ini
[mount ro]
    {HOME}/.config/fish
    {HOME}/.config/nvim
```

### Repo configuration

Configuration can further be changed for a given git repo.

For example, if you want to start a different image by default, when larcik is
opened in the current repo:

    git config --local larcik.image fedora:latest

Or you can add a mount point that will automatically be addded, when larcik is
opened in the current repo:

    git config --local --add larcik.mount-rw "{HOME}/.buildroot-dl"

You can always edit what you've added manually with:

    git config edit

## License

Licensed under [MIT License](LICENSE).


[toolbx]: https://github.com/containers/toolbox
[devkit]: https://github.com/devkit-dev/devkit
