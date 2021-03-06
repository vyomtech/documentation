.. _mfsmount.1:

***********
mfsmount(1)
***********

NAME
====

mfsmount - mount a LizardFS File System

SYNOPSIS
========

::

 mfsmount 'mountpoint' [-d] [-f] [-s] [-m] [-n] [-p]
         [-H 'HOST'] [-P PORT] [-S PATH] [-o opt[,opt]...]
 mfsmount -h | --help
 mfsmount -V | --version


DESCRIPTION
===========

Mount a LizardFS Filesystem

DATA CACHE MODES
----------------

There are three cache modes: *NO*, *YES* and *AUTO*. The default option is
*AUTO* and you shouldn't change it unless you really know what you are doing.
In *AUTO* mode data cache is managed automatically by mfsmaster.

NO,NONE or NEVER
  never allow files data to be kept in cache (safest but can reduce efficiency)

YES or ALWAYS
  always allow files data to be kept in cache (dangerous)

AUTO
  file cache is managed by mfsmaster automatically (should be very safe and
  efficient)

SUGID CLEAR MODE
----------------

SMODE can be set to:
^^^^^^^^^^^^^^^^^^^^

NEVER
  MFS will not change suid and sgid bit on chown

ALWAYS
  clear suid and sgid on every chown - safest operation

OSX
  standard behavior on OS X and Solaris (chown made by unprivileged
  user clear suid and sgid)

BSD
  standard behavior on BSD systems (like on OSX, but only when
  something is really changed)

EXT
  standard behavior in most file systems on Linux (directories not
  changed, others: suid cleared always, sgid only when group exec bit
  is set)

XFS
  standard behavior in XFS on Linux (like EXT but directories are
  changed by unprivileged users)

SMODE extra info:
^^^^^^^^^^^^^^^^^

btrfs,ext2,ext3,ext4,hfs[+],jfs,ntfs and reiserfs on Linux work as 'EXT'.

Only xfs on Linux works a little different. Beware that there is a strange
operation - chown(-1,-1) which is usually converted by a kernel into something
like 'chmod ug-s', and therefore can't be controlled by MFS as 'chown'


OPTIONS
=======

General options
---------------

-h, --help
  display help and exit

-V
  display version information and exit

FUSE options
------------

-d, -o debug
  enable debug mode (implies -f)
-f
  foreground operation
-s
  disable multi-threaded operation


LizardFS options
----------------

::

  -m, --meta, -o mfsmeta
     mount MFSMETA companion filesystem instead of primary LizardFS

  -c CFGFILE, -o "mfscfgfile=CFGFILE"
     loads file with additional mount options

  -n
     omit default mount options (-o allow_other,default_permissions)

  -p, -o askpassword
     prompt for password (interactive version of -o mfspassword='PASS')

  -H HOST, -o mfsmaster\=HOST
     connect with LizardFS master on <HOST> (default is mfsmaster)

  -P PORT, -o mfsport=PORT
     connect with LizardFS master on <PORT> (default is 9421)

  -B <HOST>, -o mfsbind=<HOST>
     local address to use for connecting with master instead of default one

  -S <PATH>, -o mfssubfolder\=<PATH>
     mount specified LizardFS directory (default is /, i.e. whole filesystem)

  -o enablefilelocks=[0,1]
     enables/disables global file locking (disabled by default)

  -o mfspassword=<PASSWORD>
     authenticate to LizardFS master with <PASSWORD>

  -o mfsmd5pass=<MD5>
     authenticate to LizardFS master using directly given <MD5> (only if
     mfspassword option is not specified)

  -o mfsdelayedinit
     connection with master is done in background - with this option mount can
     be run without network (good for running from fstab / init scripts etc.)

  -o mfsacl
     enable ACL support (disabled by default)

  -o mfsaclcacheto=<SEC>
     set ACL cache timeout in seconds (default: 1.0)

  -o mfsaclcachesize=<N>
     define ACL cache size in number of entries (0: no cache; default: 1000)

  -o mfsrwlock=<0|1>
     when set to 1, parallel reads from the same descriptor are performed
     (default: 1)

  -o mfsmkdircopysgid=<N>
     sgid bit should be copied during mkdir operation (on linux default: 1,
     otherwise: 0)

  -o mfssugidclearmode=<SMODE>
     set sugid clear mode (see below)

  -o mfsdebug
     print some LizardFS-specific debugging information

  -o mfscachemode=<CACHEMODE>
     set cache mode (see DATA CACHE MODES; default is AUTO)

  -o mfscachefiles
     (deprecated) preserve file data in cache (equivalent to -o
     mfscachemode='YES')

  -o mfsattrcacheto=<SEC>
     set attributes cache timeout in seconds (default: 1.0)

  -o mfsentrycacheto=<SEC>
     set file entry cache timeout in seconds (default: 0.0, i.e. no cache)

  -o mfsdirentrycacheto=<SEC>
     set directory entry cache timeout in seconds (default: 1.0)

  -o mfswritecachesize=<N>
     specify write cache size in MiB (in range: 16..2048 - default: 128)

  -o mfscacheperinodepercentage=<N>
     specify what part of the write cache non occupied by other inodes can a
     single inode occupy (measured in %).
     E.g. When N=75 and the inode X uses 10 MiB, and all other inodes use 20
     MiB out of 100 MiB cache, X can use 50 MiB more (since 75% of 80 MiB is
     60 MiB).
     Default: 25.

  -o mfschunkserverreadto=<MSEC>
     set timeout for whole communication with a chunkserver during read
     operation in milliseconds (default: 2000)

  -o mfschunkserverwriteto=<MSEC>
     set chunkserver response timeout during write operation in milliseconds
     (default: 5000)

  -o mfschunkserverrtt=<MSEC>
     set timeout after which SYN packet is considered lost during the first
     retry of connecting a chunkserver (default: 200)

  -o mfschunkserverconnectreadto='MSEC'
     set timeout for connecting with chunkservers during read operation in
     milliseconds (default: 2000)

  -o mfschunkserverwavereadto='MSEC'
     set timeout for executing each wave of a read operation in milliseconds
     (default: 500)

  -o mfschunkservertotalreadto='MSEC'
     set timeout for the whole communication with chunkservers during a read o
     operation in milliseconds (default: 2000)

  -o mfsrlimitnofile='N'
     try to change limit of simultaneously opened file descriptors on startup
     (default: 100000)

  -o mfsnice='LEVEL'
     try to change nice level to specified value on startup (default: -19)

  -o mfswriteworkers='N'
     define number of write workers (default: 10)

  -o mfswritewindowsize='N'
     define write window size (in blocks) for each chunk (default: 15)

  -o cacheexpirationtime=MSEC
     set timeout for read cache entries to be considered valid in milliseconds
     (0 disables cache) (default: 0)

  -o readaheadmaxwindowsize=KB
     set max value of readahead window per single descriptor in kibibytes
     (default: 0)

  -o mfsmemlock
     try to lock memory (must be enabled at build time)

  -o mfsdonotrememberpassword
     do not remember password in memory - more secure, but when session is lost
     then new session is created without password

  -o mfsioretries='N'
     specify number of retries before I/O error is returned (default: 30)

  -o mfsreportreservedperiod='N'
     specify interval of reporting reserved inodes in seconds (default: 60)

  -o mfsiolimits='PATH'
     specify local I/O limiting configuration file (default: no I/O limiting)

  -o symlinkcachetimeout=<N>
     Set timeout value for symlink cache timeout in seconds. Default value is
     3600.

  -o bandwidthoveruse=<N>
     Define ratio of allowed bandwidth overuse when fetching data. Default
     value is 1.25. This option is effective only with N+M goals (xors and
     erasure codes).

General mount options (see mount(8) manual):
----------------------------------------------

  -o rw, -o ro
     Mount file-system in read-write (default) or read-only mode respectively.

  -o suid, -o nosuid
     Enable or disable suid/sgid attributes to work.
  -o dev, -o nodev
     Enable or disable character or block special device files interpretation.

  -o exec, -o noexec
     Allow or disallow execution of binaries.


REPORTING BUGS
==============

Report bugs to <contact@lizardfs.org>.

COPYRIGHT
=========

Copyright 2008-2009 Gemius SA, 2013-2016 Skytechnology Sp. z o.o.

LizardFS is free software: you can redistribute it and/or modify it under the
terms of the GNU General Public License as published by the Free Software
Foundation, version 3.

LizardFS is distributed in the hope that it will be useful, but WITHOUT ANY
WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR
A PARTICULAR PURPOSE. See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with
LizardFS. If not, see <http://www.gnu.org/licenses/>.

SEE ALSO
========

mfsmaster(8), lizardfs(1), lizardfs(7), mount(8)
