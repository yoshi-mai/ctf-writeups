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
In this level, we can use the ```-size``` option for ```find```, to get the path to our file which contains the password.
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
The interesting bit, is the ```2>/dev/null``` part. It redirects all standard errors like ```Permission denied``` to ```/dev/null```, and null automatically discards the output written to it.
So we only get our one line, instead of a bulk of errors.
Nice!

## Level 7 -> Level 8
For our next challenge, we have to use ```grep```. We pipe the output of ```cat``` into ```grep```, which returns only the line containing the searched pattern.
```bash
bandit7@bandit:~$ ls
data.txt
bandit7@bandit:~$ cat data.txt | grep millionth
millionth       cvX2JJa4CFALtqS87jk27qwqGhBM9plV
```
Easy breezy.

## Level 8 -> Level 9
The next data.txt contains a ton of duplicate lines, and we have to find teh single unique one.
We will use ```sort``` and pipe the sorted output into ```uniq -u``` with which you can filter adjacent matching lines. With the ```-u``` argument, it outputs only unique lines.
```bash
bandit8@bandit:~$ sort data.txt | uniq -u
UsvVyFSfZZWbi6wgC7dAFyFuR6jQQUhR
```

## Level 9 -> Level 10
This file contains a lot of human-unreadable characters. We have to filter the human-readable strings and then grab our password from there.
To filter the human-readable strings we use ```strings``` which returns only human-readable strings. We pipe that output into ```grep =``` because our password is appended at multiple characters of ```=```.
```bash
bandit9@bandit:~$ strings data.txt | grep =
========== the*2i4
=:G e
========== password
<I=zsGi
Z)========== is
A=|t&E
Zdb=
c^ LAh=3G
*SF=s
&========== truKLdjsbJ5g7yyJ2X2R0o3a5HQJFuLk
S=A.H&^
```
And the lines with multiple ```=``` form our answer -> The password is X
Great!

## Level 10 -> Level 11
We get a data.txt file with a string in it, that is encoded with base64.

We pipe the string of the data.txt file into the ```base64``` decoder and get the password! Easy.
```
bandit10@bandit:~$ echo | cat data.txt | base64 --decode
The password is IFukwKGsFW8MOq3IRFqrxE1hxTNEbUPR
```
## Level 11 -> Level 12
Tricky. We have another data.txt file which contains absolute gibberish. The tips give us a hint, that all lowercase letters and uppercase letters [a-zA-Z] are rotated by 13 positions.
We can use the ```tr``` command, to translate the letters 13 positions.
```cat data.txt | tr '[a-zA-Z]' '[n-za-mN-ZA-M]'``` will do the magic. The characters in SET1 will get replaced by the characters in the same position in SET2. ```[a-zA-Z]``` includes exactly 52 characters (26 lower, 26 upper). Same goes for ```[n-za-mN-ZA-M]```.
At position 0 we got 'a' in SET1 and 'n' in SET2.
At position 1 we got 'b' in SET1 and 'o' in SET2.
At position 2 we got 'c' in SET1 and 'p' in SET2.
And so on....they are exactly 13 positions rotated. That is all.

```
bandit11@bandit:~$ cat data.txt | tr '[a-zA-Z]' '[n-za-mN-ZA-M]'
The password is 5Te8Y4drgCRfCx8ugdwuEX8KFC6k2EUu
```

## Level 12 -> Level 13


