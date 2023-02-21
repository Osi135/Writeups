# [Forensics](https://play.picoctf.org/practice?category=4&page=1)

### Notes
For all the challenges, when I am using the terminal, I am using Windows subsystem for linux (wsl). A program that lets you use the linux operating system on your windows machine. So some of these solutions - especialy those involving the terminal - may only work on a linux os.

## [Wireshark doo dooo do doo...](https://play.picoctf.org/practice/challenge/115?category=4&page=1)
### Description
Can you find the flag? shark1.pcapng.
### Prerequizits
Have wireshark dowloaded. You can dowload it [here](https://www.wireshark.org/download.html)
### Solution
1. Open the "shark1.pcapng" file in Wirehark
2. Click tab 'File'->'Export Objeect'->'HTTP...'
3. Sort 'Content Type:' with 'text/html'
4. Dowload the file named '\'
5. Open and copy the encrypted flag
6. Go to [CyberChef](https://gchq.github.io/CyberChef/)
7. Add 'ROOT13' to the 'recipie'
8. Paste the flag
9. Click 'BAKE!'
10. The flag is in the output

### Explanation
We are given no spesific place to look in the shark1.pcapng file. So, we can look at a few things that are typical to look at in wireshark. There are multiple ways of finding this flag, but the way I did it was by exporting HTTP object. You can do that by clicking 'File' in the upper left corner, then click *Export Objects' on the droppdown menu, then click 'HTTP...' You will now get a page with list of HTTP object. Lets look for one that looks different than the others. In the upper right corner, it says 'Content Type:', followed by a dropdown menu. Selecting 'text/html' or 'text/plain', we get only one object. Lets first look under 'text/html'. There we se a object with the filename '\'. Click it and click the save button. Now we can dowload it. After dowloading it, open the file in a text editor. You now se somthing that looks like a flag, but not quite. It in encrypted. We need to decrypt it. A great tool for decryption is [CyberChef](https://gchq.github.io/CyberChef/). We now need to know what encryption algorithem was used. The brackets "{}" in the flag is still there, so it has to be a algorithem that doesn't affect beackets. Me knowing CTF's, I know that a variation of a [Caesar Cipher](https://en.wikipedia.org/wiki/Caesar_cipher) is typical encryption algorithem for CTF's. Adding the 'ROOT13'(a common Caesar Cipher with 13 as defult rotation.) to the 'recipe' area, and pasting the encrypted flag in the input. We get the decrypted flag.
