# Bandit Write-up

The following document is a personal writeup for the bandit ctf wargame at [OverTheWire](https://overthewire.org/wargames/bandit/). It is not open for sharing but only for my own monitoring of learning. Don't spoil yourself of the fun kiddo.

## Level 0
First level is easy, you just use the manual for ssh and find out how to connect to a specific port.
```bash
# The syntax is ssh username@host -p portnumber
ssh bandit0@bandit.labs.overthewire.org -p 2220
```
Type in the bandit0 password and we are in.

## Level 0 -> Level 1
The first flag (password) is stored in the readme file of the current directory.
We read the content of a file with the cat command:
```bash
cat readme
```
And that's it. As output we get our password for the next level.

## Level 1 -> Level 2
The next flag is stored in a file with a special character. Since cat can't read a special character as input without a path appended, we have to add the path to the file as input for cat.
```bash
cat ./-
```
There is our password.

## Level 2 -> Level 3
Next level, next Problem. The name of the file in which our flag is stored contains spaces and cat can't read spaces as input. We have to escape them with a ```\``` character.
```bash
cat spaces\ in\ this\ filename
```
Magic, I know.

## Level 3 -> Level 4
This level introduces changing directory and hidden files. We change directory with cd and list hidden files with ls -a
```bash
bandit3@bandit:~$ cd inhere
bandit3@bandit:~$ ls -a
.  ..  .hidden
bandit3@bandit:~$ cat .hidden
```
We got our password from the hidden file!

## Level 4 -> Level 5
In this one, you can use the file command, to get the type of content the file contains.
```bash
bandit4@bandit:~/inhere$ file ./-file*
./-file00: data
./-file01: data
./-file02: data
./-file03: data
./-file04: data
./-file05: data
./-file06: data
./-file07: ASCII text
./-file08: data
./-file09: data
bandit4@bandit:~/inhere$ cat "./-file07"
```
We got our password with the help of the checking the file!

## Level 5 -> Level 6
The find command gets introduced, and oh boy, it is one powerful tool.
In this level, we can use the -size option for find, to get the path to our file which contains the password.
```bash
bandit5@bandit:~$ cd inhere/
bandit5@bandit:~/inhere$ ls
maybehere00  maybehere02  maybehere04  maybehere06  maybehere08  maybehere10  maybehere12  maybehere14  maybehere16  maybehere18
maybehere01  maybehere03  maybehere05  maybehere07  maybehere09  maybehere11  maybehere13  maybehere15  maybehere17  maybehere19
# find -size takes different input sizes, an appended c denotes bytes. Check the man page,your best friend!
bandit5@bandit:~/inhere$ find -size 1033c
./maybehere07/.file2
bandit5@bandit:~/inhere$ cat maybehere07/.file2
```
Voila, we are done.

## Level 6 -> Level 7
We have to search on the whole server now, not only in the home directory!
```bash
bandit6@bandit:~$ cd ..
bandit6@bandit:/home$ cd ..
bandit6@bandit:/$ find -size 33c -user bandit7 -group bandit6 2>/dev/null
./var/lib/dpkg/info/bandit7.password
```
The interesting bit, is the ```2>/dev/null``` part. It redirects all standard errors like ```Permission denied``` to /dev/null, and null automatically discards the output written to it.
So we only get our one line, instead of a bulk of errors.
Nice!

## Level 7 -> Level 8







