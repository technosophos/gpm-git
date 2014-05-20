# gpm-git: Local Git Package Management for Go

[gpm](https://github.com/pote/gpm) is a tool for managing Go packages.
`gpm-git` is a plugin that adds support for direct Git management of
directories inside of a `gpm`-managed `$GOPATH`.

## Use Cases

Using this plugin, you can use GPM to...

* Work with Git repos that are password protected (e.g. private
  Bitbucket repos).
* Use any git repo that `git clone` can use.
* Clone a full git repo into a local working copy, and modify it within
  the copy. (Not necessarily recommended, but possible).

## Installation

## Usage

`gpm-git` uses a special file called `Gopath-Git` to read the repos that
it needs. This file differs slightly from the regular GPM `Gopath` file.
It uses up to three fields separated by whitespace:

```
GIT_URL   GO_PKG_NAME   [VERSION]
```

* `GIT_URL`: The URL to the git repository.
* `GO_PKG_NAME`: The full name of the package in Go's package name
  format.
* `VERSION`: The git commit or reference.

Here's an example file:

```

# Get the tip of master of the foo/bar project
git@bitbucket.org:foo/bar bitbucket.org/foo/bar master
# This works, too
# git@bitbucket.org:foo/bar bitbucket.org/foo/bar

# Get a specific commit or tag off of foo/baz
git@bitbucket.org:foo/baz bitbucket.org/foo/baz 2feb1ef
git@bitbucket.org:foo/argh bitbucket.org/foo/argh 1.2.3
```

Once you have a `Gopath-Git` file, you can use `gpm-git` like this:

```bash
$ gpm git
```

The above will attempt to clone (or `fetch --all` if the clone exists)
the given URL, and then place it in the appropriate location in
`.gopath`.

Once you have a repo initialized, you can manage it directly by going
into `.gopath/src`, or you can simply update the package by calling `gpm
git` again.

## Under the Hood

If you're interested in knowing how `gpm-git` works, here's the
high-level overview:

1. It reads the `Gopath-Git` file line by line, breaking it into fields.
2. For each `GIT_URL` it creates a new package path in `.gopath/src` and
   then clones the git repository into it. It uses whichever version of
  `git` it finds on the $PATH.
3. If a VERSION is set, it checks out that particular version.

For performance reasons, re-running `gpm git` will *not* re-clone each
repository. Instead it will `git fetch --all`. I did this because large
git repos can take a *really* long time to download.

