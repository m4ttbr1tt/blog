+++
draft = false
date = 2025-07-04T20:05:51+02:00
title = "LFCS - Essential Commands - File management and permissions"
authors = ["Matt Britt"]
tags = ["LFCS", "Linux", "Certificates"]
+++
A summary of the commands learnt in the essentials section of the LFCS course preparation.
<!--more-->
### File management

```shell
cd - # goes back to previous dir
cd # goes to home dir
cp -r [source] [dest] # copy dir with all its contents

# Good idea to prepend directories with /

cp -r testdir/ anotherdir/ # copies recursively to anotherdir

#Hard links

stat someimage.jpg # file info, will show link count (can be multiple)

ln ./target_file ./link_file # hard link

#Soft links
#Hard links point to inodes, softlinks point to a path

ln -s ./target_file ./link_file # soft link
ls -l
readlink ./link_file 

#soft link to dirs or different file sytem
```

###  Owners, groups and permissions

```shell
ls -l
chgroup group_name fileordir # changes group

groups # check the groups of user
sudo chown jane file  # change owner
sudo chown aaron:family file # change user and group

#Permissions
chmod permissions file_or_dir # change mode

# + is adding permissions
# - is removing permissions
# = exact permissions  g=rw (declarative, will remove x if it exists)
# u+   - user
# g+   - group
# o+   - other

chmod u+w file # adding to user the write permission
chmod u+rwx file # adding to user the rwx permissions

chmod g+w file # adding to group the write permission
chmod g+rwx file # adding to group the rwx permissions

# split perm for user group and other
chmod u+r,g=r,o= file # o has no permissions

# or via decimal eg. 640

One decimal for each permission eg. 640
u   |  g  | o
6   |  4  |  0
110 | 100 | 000
rw- | r-- | ---

# SUID bit - Set User Identification bit (set on files)
# when executed will be executed with userid of owner of file not by the person executing

chmod 4664 suidfile # first 4 is suid permission

ls -l suidfile
-rwSrw-r-- # suid bit is S on the x permission (enabled but no executed permission) lower case s will be suid and x (applied with 4764)

# SGID bit - Set Group Identification bit (set on files)

chmod 2664 sgidfile # takes a 2 instead of 4 as first perm

find . -perm /4000   # searching suid files
find . -perm /2000   # searching sgid files

chmod 6664 both # 4 (suid) & 2 (sguid) is 6 (for both)

find . -perm /6000
./sgidfile
./both
./suidfile

#sticky bit for directories - for shared dirs - only people that can remove the file is owner and root

chmod +t stickydir
#or 
chmod 1777 stickydir # takes a 1 for sticky dir

ls -ld  
# t or T on end of permissions

chmod u+s,g+s,o+t file # sets sgid suid and sticky
```

