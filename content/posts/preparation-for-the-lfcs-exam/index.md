+++ 
draft = false
date = 2025-07-01T18:05:51+02:00
title = "Preparation for the LFCS exam"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
+++

The Linux Foundation Certified System Administrator (LFCS) exam is a 2 hour performance based exam in which you solve multiple issues from the command line.

<!--more-->

Whist I have been using Linux for almost a decade, I would like to formalise my knowledge and make sure all areas are covered.  Learning Linux is an essential part of DevOps and SRE roles, so understanding its inner workings and how to manage a Linux system is essential.

<https://training.linuxfoundation.org/certification/linux-foundation-certified-sysadmin-lfcs/>

There are other Linux certifications, namely the Linux Professional Institute LPIC-1 (101 & 102) certifications. These are multiple choice exams, and whilst this is still valuable, I feel the performance based tests will be a better reflection of knowledge and skill. I will likely still study the free LPIC content in future.

<https://www.lpi.org/our-certifications/lpic-1-overview/>

There is also a really valuable YouTube series that will assist in preparation [LPIC 1 - Video Series](https://www.youtube.com/watch?v=AKkNUvEHXhk&list=PLFOYXCPEqdNUU55Xvgst8wGTWnz_sd-cj)

The exam is broken up into the following sections:

- Essential Commands (20%)
- Operations Deployment (25%)
- Users and Groups (10%)
- Networking (25%)
- Storage(20%)

#### Getting help directly on Linux

Whilst information can be gleaned from the web, its an important skill to master being able to source answers locally (ie. on the system you are running on). Here are some of the most useful and common commands.

#### Man Pages

Man pages provide all the information you need. You can search specific sections depending on the documentation you require.

```shell
# will list the sections
man man

# examples of getting information
ls --help
man ls
man 1 printf
man 3 printf
```

#### Apropos command

The apropos command searches all man pages related to a keyword or phrase. So if you are not sure where you should be searching this is a great tool.

```shell
# searches man pages if you dont know what you need
sudo mandb # create apropos db (man database)
apropos tar # search for occurences of tar in manpages
apropos -s 1,3 tar # sections
```

Finding all executables (man section 1) with the term archiv

```shell
homelab on  master took 3s ⬢ [Docker] ❯  apropos -s 1 archiv
apt-ftparchive (1)   - Utility to generate index files
ar (1)               - create, modify, and extract from archives
dpkg-deb (1)         - Debian package archive (.deb) manipulation tool
dpkg-split (1)       - Debian package archive split/join tool
funzip (1)           - filter for extracting from a ZIP archive in a pipe
git-archive (1)      - Create an archive of files from a named tree
git-bundle (1)       - Move objects and refs by archive
git-diagnose (1)     - Generate a zip archive of diagnostic information
git-get-tar-commit-id (1) - Extract commit ID from an archive created using git-archive
git-index-pack (1)   - Build pack index file for an existing packed archive
git-pack-objects (1) - Create a packed archive of objects
git-show-index (1)   - Show packed archive index
git-unpack-objects (1) - Unpack objects from a packed archive
git-upload-archive (1) - Send archive back to git-archive
git-verify-pack (1)  - Validate packed Git archive files
gp-archive (1)       - archive gprofng experiment data
gpg-zip (1)          - encrypt or sign files into an archive
gpgtar (1)           - Encrypt or sign files into an archive
ptardiff (1)         - program that diffs an extracted archive against an unextracted one
ptargrep (1)         - Apply pattern matching to the contents of files in a tar archive
ranlib (1)           - generate an index to an archive
tar (1)              - an archiving utility
tarcat (1)           - concatenates the pieces of a GNU tar multi-volume archive
unzip (1)            - list, test and extract compressed files in a ZIP archive
unzipsfx (1)         - self-extracting stub for prepending to ZIP archives
x86_64-linux-gnu-ar (1) - create, modify, and extract from archives
x86_64-linux-gnu-gp-archive (1) - archive gprofng experiment data
x86_64-linux-gnu-ranlib (1) - generate an index to an archive
zip (1)              - package and compress (archive) files
zipgrep (1)          - search files in a ZIP archive for lines matching a pattern
zipinfo (1)          - list detailed information about a ZIP archive

```

![apropos](/posts/preparation-for-the-lfcs-exam/apropos-archive.png)

#### Tldr-pages

Man pages can sometimes be hard to digest, a simpler tool is tldr-pages, which is a collection of community-maintained help pages - which gives examples of how the tool is used.

```shell
sudo apt install tldr
tldr tar
```

```shell
homelab on  master took 3s ⬢ [Docker] ❯  tldr tar
Downloading tldr pages to /home/vscode/.local/share/tldr
tar

Archiving utility.
Often combined with a compression method, such as gzip or bzip2.
More information: https://www.gnu.org/software/tar.

 - [c]reate an archive and write it to a [f]ile:
   tar cf path/to/target.tar path/to/file1 path/to/file2 ...

 - [c]reate a g[z]ipped archive and write it to a [f]ile:
   tar czf path/to/target.tar.gz path/to/file1 path/to/file2 ...

 - [c]reate a g[z]ipped (compressed) archive from a directory using relative paths:
   tar czf path/to/target.tar.gz --directory=path/to/directory .

 - E[x]tract a (compressed) archive [f]ile into the current directory [v]erbosely:
   tar xvf path/to/source.tar[.gz|.bz2|.xz]

 - E[x]tract a (compressed) archive [f]ile into the target directory:
   tar xf path/to/source.tar[.gz|.bz2|.xz] --directory=path/to/directory

 - [c]reate a compressed archive and write it to a [f]ile, using the file extension to [a]utomatically determine the compression program:
   tar caf path/to/target.tar.xz path/to/file1 path/to/file2 ...

 - Lis[t] the contents of a tar [f]ile [v]erbosely:
   tar tvf path/to/source.tar
```

<https://github.com/tldr-pages/tldr>

![tldr](/posts/preparation-for-the-lfcs-exam/tar-tldr-pages.png)
