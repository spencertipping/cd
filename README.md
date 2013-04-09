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
    $ cd hdfs://namenode:9000   # mounts namenode with hadoop-fuse-dfs
    $ cd ^                      # history: go back one directory
    $ cd ^^                     # history: go back two directories
    $ cd ^10                    # history: go back ten directories
    $ cd ^foo                   # history: go back to last dir matching /foo/

Fuse-mounted directories are unmounted automatically when you `cd` out of them.

## Setup

To enable it (this can also be done from `.bashrc`):

    $ . cd                      # enables extensible cd
    $ . cd-traverse             # enables descendant/ancestor traversal
    $ . cd-history              # ^, ^^, ^n, ^regexp
    $ . cd-ssh                  # does nothing unless you have sshfs
    $ . cd-archive              # does nothing unless you have archivemount
    $ . cd-hdfs                 # does nothing unless you have hadoop-fuse-dfs
    $ . cd-dev

**Be sure to initialize this script after RVM.** RVM redefines `cd`, clobbering
any existing redefinitions. This script, on the other hand, preserves RVM's
`cd` redefinition while adding behavior of its own.

Some of `cd`'s extensions store state and/or create temporary mountpoints in
`~/.cd`, which it automatically creates.

## Experimental extensions

Unfinished, but potentially useful anyway.

### Git (via gitfuse)

    $ . cd-git                  # does nothing unless you have gitfuse

If you have [gitfuse](https://github.com/davesque/gitfuse) somewhere in your
`$PATH`, you can `cd` into `/some/path.git` to get access to all files in all
revisions.

The simplest way I've found to install Gitfuse on Ubuntu 12.10 is:

    # apt-add-repository ppa:xav0989/libgit2
    # apt-add-repository ppa:dennis/python
    # apt-get install libgit2 libgit2-dev python-pygit2 python-setuptools python-dev
    $ git clone git://github.com/davesque/gitfuse && cd gitfuse
    $ sed -i '/pygit2/ d' setup.py
    # python setup.py install

As of this revision, Gitfuse does not produce useful results.
