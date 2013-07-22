# `cd`, but better

A replacement for the default Bash `cd` command. It can mount FUSE filesystems
on-demand, jump down multiple levels of directories, etc. `cd` always prefers
real directories to special commands; so if you had a directory called `foo:`,
`cd foo:` would just cd into `foo:`, not try to mount `foo:` with sshfs.

    $ cd machine-name:[path]    # automounts machine-name:path with sshfs
    $ cd /dev/sdb1              # create a mountpoint, then sudo-mount it
    $ cd **x                    # cd's to the first descendant dir matching /x/
    $ cd ..5                    # cd's up five directories
    $ cd ..foo                  # cd's up to the nearest dir matching /foo/
    $ cd x.tar                  # mounts x.tar with archivemount
    $ cd hdfs://namenode:9000   # mounts namenode with hadoop-fuse-dfs
    $ cd ^                      # history: go back one directory
    $ cd ^^                     # history: go back two directories
    $ cd ^10                    # history: go back ten directories
    $ cd ^foo                   # history: go back to last dir matching /foo/
    $ cd git:/path/to/repo      # mounts git commits as directories
    $ cd enc:/path/to/encrypted # mounts an encfs directory

Fuse-mounted directories are unmounted automatically when you `cd` out of them.

`cd` also supports symlinks to virtual directories. For example:

    $ ln -s machine-name:/foo/bar my-symlink
    $ cd my-symlink             # same as cd machine-name:/foo/bar

If you want `cd` to virtualize the destination of a symlink, the symlink must
not point to a real directory.

## Interactive options

    $ cd --history              # prints $PWD history, most recent first
    $ cd --mounts               # lists probably-active FUSE mount points
    $ cd --patterns             # all patterns that 'cd' is looking for
    $ cd --clean                # attempts to unmount and remove all mounts

The `--clean` option is good for cases where FUSE mountpoints are left in an
inconsistent state; for instance, when you suspend a machine with an open SSHFS
connection. It will unmount and remove all mountpoints that aren't in use.

## Setup

To enable it (this can also be done from `.bashrc`):

    $ . cd                      # enables extensible cd
    $ . cd-traverse             # enables descendant/ancestor traversal
    $ . cd-history              # ^, ^^, ^n, ^regexp
    $ . cd-ssh                  # does nothing unless you have sshfs
    $ . cd-archive              # does nothing unless you have archivemount
    $ . cd-hdfs                 # does nothing unless you have hadoop-fuse-dfs
    $ . cd-git                  # does nothing unless you have yagfs
    $ . cd-encfs                # does nothing unless you have encfs
    $ . cd-dev                  # enables automounting for /dev/ entries

You can see the patterns that are handled by running `cd --patterns`. (This can
be useful if you want to find out which ones are ignored due to unmet
dependencies.) Anything that isn't a pattern is treated like a regular
directory change.

**Be sure to initialize this script after RVM.** RVM redefines `cd`, clobbering
any existing redefinitions. This script, on the other hand, preserves RVM's
`cd` redefinition while adding behavior of its own.

Some of `cd`'s extensions store state and/or create temporary mountpoints in
`~/.cd`, which it automatically creates.

## Experimental extensions

### Create missing directories

    $ . cd-missing-mkdir

Sourcing this script causes `cd` to create directories that don't exist. Any
leaf directory created by `cd` will be removed upon leaving if it is empty and
no process is using it. So, for example:

    $ ls
    foo  bar
    $ cd BOOYAH
    $ cd ..
    $ ls
    foo  bar
    $

At this point `cd` will create but not delete parent directories:

    $ ls
    foo  bar
    $ cd bif/baz/bok
    $ cd ../../..
    $ ls
    foo  bar  bif
    $ ls bif
    baz
    $ ls bif/baz
    $

## YaGFS

YaGFS doesn't have an Ubuntu package, but it should be easy to install from its
[source repository](https://github.com/spencertipping/yagfs). You'll need to
symlink the `yagfs` script into your `$PATH` for `cd` to know to use it.

## Authors

- Spencer Tipping [spencertipping](https://github.com/spencertipping)
- Vivien Didelot [vivien](https://github.com/vivien)
