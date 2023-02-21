# [General Skills](https://play.picoctf.org/practice?category=5&page=1)

### Notes
For all the challenges, when I am using the terminal, I am using Windows subsystem for linux (wsl). A program that lets you use the linux operating system on your windows machine. So some of these solutions - especialy those involving the terminal - may only work on a linux os.


## [Obedient Cat](https://play.picoctf.org/practice/challenge/147?category=5&page=1)
### Description
This file has a flag in plain sight (aka "in-the-clear"). Download flag.
### Solution
In the challenge you are give a file to dowload. You simply have to open that file in a text editor.


## [Python Wrangling](https://play.picoctf.org/practice/challenge/166?category=5&page=1)
### Description
Python scripts are invoked kind of like programs in the Terminal... Can you run this Python script using this password to get the flag?
### Solution
1. Prerequisite: Have python dowloaded.
2. Dowload the files to one folder.
3. In a terminal in that folder, run:
`python3 ende.py -d flag.txt.en 68f88f9368f88f9368f88f9368f88f93`

### Explanation
Opening the ende.py file, it contains about 60 lines of code. It contains Some setup and some automatic responses, but the important part is this: 
<code>

    elif sys.argv[1] == "-d":
    if len(sys.argv) < 4:
        sim_sala_bim = input("Please enter the password:")
    else:
        sim_sala_bim = sys.argv[3]

    ssb_b64 = base64.b64encode(sim_sala_bim.encode())
    c = Fernet(ssb_b64)

    with open(sys.argv[2], "r") as f:
        data = f.read()
        data_c = c.decrypt(data.encode())
        sys.stdout.buffer.write(data_c)
</code>

We can se that by using the `-d` flag to the command, we use decoding mode. And by either entering the password in the pw.txt file when asked to enter the password, or entering it behind the name of the encoded file, the code will return the decoded flag.


## [Wave a flag](https://play.picoctf.org/practice/challenge/170?category=5&page=1)
### Description
Can you invoke help flags for a tool or binary? This program has extraordinarily helpful information...
### Solution
1. Dowload the file.
3. In the terminal. run:
`chmod +x warm`
4. In the terminal. run:
`./warm -h`

### Explanation
If you try to read the file it is un-readable. If you try to run it by typing `warm` in the terminal, the terminal does not understand it since it is not executable. You can work around that by typing `./warm` in the terminal. Now the terminal returns: `-bash: ./warm: Permission denied`. You can fix this by making the file an executable. You can do this by typing `chmod +x warm` in the terminal. Running the previous command again, the terminal now tels us to passs the -h flag. Doig this and writig `./warm -h` returns the flag.