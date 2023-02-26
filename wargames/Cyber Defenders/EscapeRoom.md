# [Escaperoom](https://cyberdefenders.org/blueteam-ctf-challenges/18)
## Info
### Category: Digital Forensics
### Tags:  Wireshark PCAP Linux 
### How to play:
You have to dowload the zip file. 
You need a profile to dowload the zip file.
Un-zip the file with the provided password: cyberdefenders.org
Have wireshark dowloaded. You can dowload it [here](https://www.wireshark.org/download.html)
Open the hp_challenge.pcap file with wireshark.
### WARNING!
The challenge has this disclaimer "Innocuous files may turn out to be malicious so take precautions when dealing with any files from this challenge." I myself, dont know the extent of damage the malicious files can do, so I am using a Viritual Machine for this challenge, and I sugest you do to. I sugest [ViritualBob](https://www.virtualbox.org/).

# Questions:
## Question 1
### Question
What service did the attacker use to gain access to the system?
### Answer
ssh
### Explanation
Look at the expert information by clicking the yellow sphere in the lower left corner. Right-click the warning and "Apply as filter", "Selected". Then rightclick the packet shown and click "Follow", "TCP Stream". At the top of the page, you see that the SSH protocol was used.

## Question 2
### Question
What attack type was used to gain access to the system?(one word) 
### Answer
bruteforce
### Explanation
We can now filter the traffic by ssh protocols. Loking at the info section of the packets, we se that the same pattern is repeated. The client and the server establish a connection using ssh, then they exchange keys, using the Diffie-Hellman key exchange protocol. The server and the client then exchange a few packets, before the process begins again. Scroling through all the packets - sorted by time -, we se that all the packets follow this pattern, exept the last session, that sendt way more encrypted packages, especialy from the server to the client.
Seeig this repeted pattern, followed by what seems to be successfull data transmition(on behalf of the hacker). We can see that the hacker used bruteforce.

## Question 3
### Question
What was the tool the attacker possibly used to perform this attack? 
### Answer
hydra
### Explanation
We now know the hacker bruteforced acces with ssh. All we need to do now is do a quick web search with the prompt "bruteforce ssh", and we get a tool called hydra as the first sugestion.

## Question 4
### Question
How many failed attempts were there?
### Answer
52
### Explanation
Sorting for ssh again, we can se that the last session was the successful one. By rightclicking on anny packet in that session and clicking "Follow", "TCP Stream", we can se that this successful stream is the 53 one. Meaning that the 52 stram was the last failed attempt.

## Question 5
### Question
What credentials (username:password) were used to gain access? Refer to shadow.log and sudoers.log. 
### Answer
manager:forgot
### Explanation
In the "shadow.log" file, there are 10 usernames with hashed passwords. I use kali linux, so I use hashcat to crack the password hashes. First I copy the hashed passwords to its own file and name the file "hashes.txt". Then I import a wordlist of hashes, which hashcat needs to crack the hashed passwords. I use "rockyou.txt" which is a commonly used wordlist that comes with kali linux. Then i type this in to the terminal `hashcat -m 1800 -a 0 hash.txt rockyou.txt` and the terminal gives these passwords manager:forgot and sean:spectre. By trying both, we se that "manager:forgot" is correct.

## Question 6
### Question
What other credentials (username:password) could have been used to gain access also have SUDO privileges? Refer to shadow.log and sudoers.log. 
### Answer
sean:spectre
### Explanation
The other credential is the other credential we got from cracing the hashes.

## Question 7
### Question
What is the tool used to download malicious files on the system? 
### Answer
wget
### Explanation
As we know, the fisrt 52 requests failed, and in the 53 request, the hacker succeded in cracking the password. They all used the SSH protocol. Looking at the 54 stream. We can se that it uses the HTTP protocol. We asume that the 54 stream was used to performe the attack with the password found in 53. In the 54th stream, we can se this `User-Agent: Wget/1.13.4 (linux-gnu)`. Wget i a GNO tool that dowloads files from the web. 

## Question 8
### Question
How many files the attacker download to perform malware installation? 
### Answer
3
### Explanation
Again looking at the streams we can se that stream 54 , 55 and 56 looks similar. They all start with `GET /d/<number> HTTP/1.1`, wher each stream has \<number> replaced by 1, 2 or 3. Stream 57 and onward look different, end has way more info flowing from the server. This sugest that the hacker sent 3 files to dowload the malware, and the following streams was used to recive info.

## Question 9
### WARNING!
We are going to dowload mallware in this question.
### Question
What is the main malware MD5 hash? 
### Answer
772b620736b760c1d736b1e6ba2f885b
### Explanation
If we click "File", "Export Object", "HTTP" and sort "Content Type" by "text/html", we now get three files. These are the uploades mallware files. The next step is dowloading these files. The files will automatically be named 1, 2 and 3.
Then we can run `md5sum 1 2 3` to get the hash. The first hash is the correct one.

## Question 10
### Question
What file has the script modified so the malware will start upon reboot? 
### Answer
/etc/rc.local
### Explanation
Again we will follow the TCP streams nr. 54, 55 and 56. Looking at all of them, we see that 54 and 55 contain a lot of encrypted information, while 56 contains some comandline comands. We can se that the commands Initialise BASH, then move "1" (the mallware file) to "var/mail/mail", then makes it executable and then extracts the content and puts it in to "/etc/rc.local"

## Question 11
### Question
Where did the malware keep local files? 
### Answer
/var/mail/
### Explanation
We follow the TCP stream nr. 56. On the line 1 of the BASH scriptt, we se this command `mv 1 /var/mail/mail`. It takes the file "1", renames it to "mail" and moves it to "/var/mail/". We can se that the file is stored in "/var/mail/".

## Question 12
### Question
What is missing from ps.log? 
### Answer
/var/mail/mail
### Explanation
We just saw that one of the mallicious files was located at "/var/mail/mail", but we don't see it show up in the ps.log.

## Question 13
### Question
What is the main file that used to remove this information from ps.log? 
### Answer
sysmod.ko
### Explanation
We follow the TCP stream nr. 56. We have seen that file 1 was the main mallicious file. But there was another file that was uploaded. We asume that the other file is some sort of helper file. And here we guess that it helps by clearing the log. We can se on line 5 of the BASH script mv 2 `/lib/modules/`\``uname -r`\``/sysmod.ko` that the file is moved in to the file is moved to "/lib/modules/\`uname -r\`/", and is renamed "sysmod.ko"

## Question 14
### Question
Inside the Main function, what is the function that causes requests to those servers? 
### Answer
requestFile
### Explanation
We now know that the file "1", is the main mallware file, so we look for the Main function inside that file (Which we have experted as an object) We can type `strings 1` we now get some lines of text, so we look through it to see if we find somthing interesting. We find the text "__libc_start_main@@GLIBC_2.2.5"
We are now in the main function. reathing further, we se the name of multiple functions. We are looking for a "function that causes requests to those servers". `requestFile` seams like the right function.

## Question 15
### Question
One of the IP's the malware contacted starts with 17. Provide the full IP. 
### Answer
174.129.57.253
### Explanation
We look at the same result we got from using `strings 1`. Scowering through the results, we find the only ip that starts with 17.

## Question 16
### Question
How many files the malware requested from external servers?
### Answer
9
### Explanation
Click "File", "Export Objects", "HTTP...". Here we can see alle the HTTP objects. We can se that the ip adresses are the same as we saw together with 174.129.57.253. We can se that there are 12 object, but 3 of them, where the mallware that was implanted. The other 9 was requested.

## Question 17
### Question
What are the commands that the malware was receiving from attacker servers? Format: comma-separated in alphabetical order
### Answer
NOP,RUN
### Explanation
To answer this question, we have to reverse enginer the mallware file "1". I use [IDA](https://hex-rays.com/ida-free/) to do that. In the code we find the function "processMessage()". Looking at that function we se that it has conditionals that compare the parameter to to hex values, "0x4e4f5000" and "0x52554a3a". By enterperating these values, f.eks using [CyberChef](https://gchq.github.io/CyberChef/#recipe=From_Hex('Auto')&input=NGU0ZjUwMDA1MjU1NGUzYQ). We get the values "NOP" and "RUN".