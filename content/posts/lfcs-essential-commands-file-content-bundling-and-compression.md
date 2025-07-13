+++
draft = false
date = 2025-07-07T17:35:51+02:00
title = "LFCS - Essential Commands - File content, bundling and compression"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
series = ["LFCS"]
+++

Continuing with the LFCS preparation series, this post covers various commands, including sed (stream editor), bundling and compression.

<!--more-->

#### Compare and Manipulate File Content

```shell
cat /home/users.txt
tac /home/users.txt # reversed

tail -n 20 /home/users.txt # last 20 lines
head -n 20 /home/users.txt # first 20 lines

sed 's/canda/canada/g' userinfo.txt #stream editor - s is for substitute

# file not edited yet
-i # for --in-place

cut -d ' ' -f 1 userinfo.txt # -d is delimiter to split columns by -f is fields (columns)

uniq filename # removes unique lines next to each other
sort filename | uniq # will get all

diff file1 file2
diff -c file1 file2 # context
diff -y file1 file2 # side by side
#or
sdiff file1 file2
```

#### Bundling / Packing

```shell
# tar (tape archive)
# tar is a packer and unpacker
# ust packs/bundles doesnt compress
# list contents of a tar file
tar --list --file archive.tar
tar -tf archive.tar
tar tf archive.tar
# create a new tar file with file1
tar --create --file archive.tar file1
tar cf archive.tar file1
# append a new tar file with file1
tar --append --file archive.tar file1
tar rf archive.tar file1
# append a new tar file with entire directory
tar --create --file archive.tar Pictures/ 
# extract an tar (current dir)
tar --extract --file archive.tar
tar xf archive.tar
# extract to another dir 
tar --extract --file archive.tar --directory /tmp/
tar xf archive.tar -C /tmp/
sudo tar xf archive.tar -C /tmp/ # makes sure permissions are restored
```

#### Compression

```shell
# these zips only work on a single file
# all these commands create a zip file and delete original
gzip file1
bzip2 file1
xz file1
# uncompress auto deletes zip and creates file 
gunzip file1.gz    gzip --decompress file1.gz
bunzip file1.gz    bzip2 --decompress file1.bz2
unxz file1.gz      xz --decompress file3.xz
gzip --help
# -k    will keep original file

# pack and compress entire dir
zip -r archive.zip Pictures/  # -r is recursive
unzip archive.zip

# tar can compress with the 

tar --create --autocompress --file archive.tar file1  # there a specific options also

```
