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

## Experimental extensions

Unfinished, but potentially useful anyway.

### HTTP

You can enable this with `. cd-http`.

    $ cd spencertipping.com     # creates a virtual HTTP client directory
    $ get /                     # GETs /
    $ cd spencertipping.com/posts/
    $ get 2012.0207.occams-razor.html
    ...
    $ ls                        # HTTP verbs
    delete                      # TODO
    get
    head                        # TODO
    post                        # TODO
    put                         # TODO
    options                     # TODO
    $

Virtual HTTP directories are managed just like fuse-mounted ones: they should,
but don't yet, get cleaned up automatically when you `cd` out of them.
