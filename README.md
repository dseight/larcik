# Larčik

Like a box, but small.

## Design

Larčik is inspired by [toolbx] and [devkit].

While toolbx makes containers extremely easy to use, just like a native system,
it lacks isolation (by design). devkit goes the opposite way: you only get your
worktree and nothing else (and also adds agents as the "first-class citizen").

While designing this, I wanted to have a tool that is somewhat middleground:
you only get your worktree in the container, but not the surroundings. Apart
from the worktree you get some of your configuration files (like .vimrc), but
no sensitive data like ssh keys.

On top of that, one of the major things that is missing in the above solutions
is the NETWORK isolation. Larčik solves that by configuring nft tables in the
container's network namespace with OCI hooks, prior starting the container. For
example, you can get a container that only has access to web, but no access to
your home network. Or the opposite: local network, without web access.

Some basic principles of the project:
- no external dependencies
- no sudo/root access
- network access is disabled by default

TODO: write a meaningful section describing features:
- container sees the workdir where you started it, and some of your config
  files (e.g., .vimrc)
- if workdir is a git repo, some actions are restricted (such as modifying git
  hooks, modifying git config, etc)

## Usage

Run the following command in a directory you want to start working with:

    larcik open

when container is no longer needed, you can close it with:

    larcik close

## License

Licensed under [MIT License](LICENSE).


[toolbx]: https://github.com/containers/toolbox
[devkit]: https://github.com/devkit-dev/devkit
