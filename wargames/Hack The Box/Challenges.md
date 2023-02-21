# [Challenges](https://app.hackthebox.com/challenges)


## [Illumination](https://app.hackthebox.com/challenges/illumination)
### INFO
Category: forensics
### Challenge Description
A Junior Developer just switched to a new source control platform. Can you find the secret token?
### Prerequizits
Have git dowloaded.
### Solution
1. Dowload file
2. Un-zip folder with the key 'hackthebox'
3. Navigate to the 'Illumination.JS' folder.
4. With git BASH, run `git show 47241a47f62ada864ec74bd6dedc4d33f4374699`
5. The flag is inn the old "token"

### Explanation
We are given a git repository, with a secret token. Looking at the 'config.json' file we can se that the token is "Replace me with token when in use! Security Risk!". This is not the correct token. Typing `git log` in git BASH, we get a list of commits. One of the commits messages says "Thanks to contributors, I removed the unique token as it was a security risk. Thanks for reporting responsibly!" It is likely that the token uses to be there, but the token was changed in this commit. Lets check it by typing `git show ` + the code for the commit with that message. Now we can see the new token and the old. The token is not in the propper format for a flag. The token is encrypted. We need to decrypt it. A great tool for decryption is [CyberChef](https://gchq.github.io/CyberChef/) We paste the token in to the input field, and then try different posible decryption alorithms. I found that the 'From Base64' was the correct algorithm. Decryt it, and you now have the flag.