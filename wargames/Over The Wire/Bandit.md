# [Bandit](https://overthewire.org/wargames/bandit/)

### Notes
For all the levels, I am using Windows subsystem for linux (wsl). A program that lets you use the linux operating system on your windows machine. So some of these solutions - especialy those involving the terminal - may only work on a linux os. 

These solutions assumes that you have read the "Level Goal" from the challenge website, if you want a full  understandig. However, you sould be able to follow along, even if you havent. 

The exlanation section assumes you have read the explenation in the previos levels


## How to connect
You have to connect to the server in a terminal using ssh. The host is "bandit.labs.overthewire.org", the port is "2220", the username is "bandit0" and the password is "bandit0"
To connect you therefor type "ssh -p2220 bandit0@bandit.labs.overthewire.org" in the terminal, and then you type "bandit0", when you are asked for the password.

## Level 0
### Solution
1. Type `cat readme` to get the password.

### Explanation
cat - for "concatenate", prints out the content of a file.

## Level 1
### Solution
1. Type `cat ./-` to get the password.

### Explanation
To acces a "-" file, you can refer to it by its path.


## Level 2
### Solution
1. Type `cat "spaces in this filename"` to get the password.

### Explanation
To reffer to a file with spaces, you can encapsulate it in quotes.


## Level 3
### Solution
1. Type `cat inhere/.hidden`

### Explanation
The .hidden file, can be found if you type `ls -a`

## Level 4
### Solution
1. Type `cat inhere/-file07`

### Explanation
Just read all files until you find the correct one.

## Level 5
### Solution
1. Type `cat inhere/maybehere07/.file2`

### Explanation
Here there are to manny files to read all of them. Luckily, the "Level Goal"-text gives us a good hint. The correct file is 1033 bytes in size. By using this command `du -a -b inhere/`, we can se the size of all the files. "du" looks at file size, "-a" includes all the files, even inside directorys, "-b" gives us the filesize in bytes and "inhere/" just directs the command to execute in the inhere/ directory. We now get all list off all the files and directorys, with their respective size, and we can find the file with 1033 bytes in size.