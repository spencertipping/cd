# `cd`, but better

A replacement for the default Bash `cd` command. It can mount FUSE filesystems
on-demand, jump down multiple levels of directories, etc. `cd` always prefers
real directories to special commands; so if you had a directory called `foo:`,
`cd foo:` would just cd into `foo:`, not try to mount `foo:` with sshfs.

    $ cd machine-name:[path]    # automounts machine-name:path with sshfs
    $ cd /dev/sdb1              # create a mountpoint, then sudo-mount it
    $ cd **foo                  # cd's to the first 'foo' descendant dir
    $ cd ..foo                  # cd's up to the nearest 'foo'
    $ cd x.tar                  # mounts x.tar with archivemount
    $ cd ^                      # history: go back one directory
    $ cd ^^                     # history: go back two directories
    $ cd ^10                    # history: go back ten directories
    $ cd ^foo                   # history: go back to last dir matching /foo/

Fuse-mounted directories are unmounted automatically when you `cd` out of them.

To enable it (this can also be done from `.bashrc`):

    $ . cd                      # enables extensible cd
    $ . cd-traverse             # enables descendant/ancestor traversal
    $ . cd-history              # ^, ^^, ^n, ^regexp
    $ . cd-ssh
    $ . cd-archive
    $ . cd-dev

Some of `cd`'s extensions store state and/or create temporary mountpoints in
`~/.cd`, which it automatically creates.
