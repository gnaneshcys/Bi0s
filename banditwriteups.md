Level0:
Ssh command is used to connect to the another server. Syntax for ssh command is given as ‘ssh username@hostname -p portnumber’ . Therefore it can be written as ‘Ssh bandit0@bandit.labs.overthewire.org -p 2220’.
Level 0 -> Level 1:
ls command is used to list all the files . after to display the contents of the file readme cat command is used .
Level 1 -> Level 2
 The password is in a file named '-' located in the home directory.To read a file named '-', use the command: cat ./- Alternatively, you can redirect it using: cat < -Copy the displayed password to log in as bandit2.
Level 2 -> Level 3
The password is in a file named 'spaces in this filename'. Use the 'ls' command to confirm the exact file name.To read it, enclose the filename in quotes: cat "spaces in this filename"Alternatively, use a backslash to escape spaces: cat spaces\ in\ this\ filename
Level 3 -> Level 4
The password is in a hidden file within the 'inhere' directory. Change directory using: cd inhereList all files, including hidden ones, using: ls -la Read the hidden file using: cat .hidden
Level 4 -> Level 5
 The password is in the only human-readable file in the 'inhere' directory.Change directory using: cd inhere Determine file types of all files using: file ./*Look for the file identified as 'ASCII text' and read it using: cat ./<filename>
Level 5 -> Level 6
The password is in a file within the 'inhere' directory with specific properties: human-readable, 1033 bytes, and not executable.Change directory using: cd inhere Use the find command to locate it: find . -type f -size 1033c ! -executableRead the found file using: cat <path-to-file>
Level 6 -> Level 7
The password is in a file somewhere on the server owned by user 'bandit7', group 'bandit6', and is 33 bytes in size.Search the entire file system: find / -user bandit7 -group bandit6 -size 33c 2>/dev/nullThe '2>/dev/null' part hides permission denied errors to make the output readable. Read the found file using: cat <path-to-file>
Level 7 -> Level 8
 The password is stored in 'data.txt' next to the word 'millionth'. You can search through the file using the grep command. Run the command: grep "millionth" data.txt The password will be displayed on the same line as the matched word.
Level 8 -> Level 9
The password is the only line of text that occurs exactly once in 'data.txt'. First, sort the file so identical lines are grouped together: sort data.txtThen pipe the output to uniq to filter unique lines: sort data.txt | uniq -u The command will output the only unique line, which is the password.
Level 9 -> Level 10
The password is in 'data.txt' and is one of the few human-readable strings, preceded by several '=' characters.Extract readable strings from the binary file using: strings data.tx. Pipe the output to grep to search for the equals signs: strings data.txt | grep "===". The password will be shown at the end of the matching line.
Level 10 -> Level 11
The password in 'data.txt' contains base64 encoded data. You need to decode it to reveal the plaintext password. Run the base64 decode command: base64 -d data.txtThe decoded output is your password for the next level.
Level 11 -> Level 12
The password in 'data.txt' has been scrambled where all lowercase and uppercase letters are rotated by 13 positions (ROT13).Read the file using cat: cat data.txt. Pipe it to the 'tr' command to translate the characters: cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'This reverses the ROT13 cipher and displays the password.
Level 12 -> Level 13
.The file 'data.txt' is a hexdump of a repeatedly compressed file.Create a working directory in /tmp, e.g., mkdir /tmp/mywork, then copy the file there. Reverse the hexdump using: xxd -r data.txt > data. Repeatedly check the file type using 'file data' and decompress using the appropriate tool (tar, gzip, bzip2) by renaming the extension as needed until you get plaintext.
Level 13 -> Level 14
 You are given an SSH private key instead of a password.. Use this key to log into bandit14 locally on the same server. Run the SSH command with the identity file flag: ssh -i sshkey.private bandit14@localhost -p 2220 Once logged in as bandit14, read the password from /etc/bandit_pass/bandit14.
Level 14 -> Level 15
The password can be obtained by submitting the current level's password to port 30000 on localhost. Retrieve the current password using: cat /etc/bandit_pass/bandit14Connect to the port using netcat: nc localhost 30000Paste the current password and hit Enter to receive the next password.
Level 15 -> Level 16
 The password is obtained by submitting the current password to port 30001 using SSL encryption. Retrieve the current password: cat /etc/bandit_pass/bandit15Connect using OpenSSL: openssl s_client -connect localhost:30001Paste the current password and hit Enter to get the next one.
Level 16 -> Level 17
 Find the open port between 31000 and 32000 that responds with SSL. Scan the ports using nmap: nmap -p 31000-32000 localhostConnect to the open ports using openssl s_client until one asks for input: openssl s_client -connect localhost:<port>. Submit the current password. It will return an RSA private key. Save this key locally to SSH into level 17.
Level 17 -> Level 18
 There are two files in the home directory: passwords.old and passwords.new.. The password for the next level is the only line that has been changed between the two files.Use the diff command to compare them: diff passwords.old passwords.new. Look for the line indicated with a '>' sign, which signifies the new password.
Level 18 -> Level 19
 The .bashrc file for bandit18 has been modified to log you out immediately upon connecting.You need to pass a command directly over SSH to prevent the shell from fully loading.Run SSH with the command at the end: ssh bandit18@bandit.labs.overthewire.org -p 2220 "cat readme"This executes 'cat readme' directly, bypassing the immediate logout, and returns the password.
Level 19 -> Level 20
 There is a setuid binary in the home directory that executes commands as another user (bandit20).Run the binary without arguments to see how it works: ./bandit20-doUse the binary to read the password file belonging to bandit20. Command: ./bandit20-do cat /etc/bandit_pass/bandit20
Level 20 -> Level 21
There is a setuid binary 'suconnect' that connects to a specified port and sends the bandit20 password.Open a second terminal window (using tmux or a new SSH session). In terminal 1, set up a netcat listener on a port (e.g., 4444): nc -l -p 4444In terminal 2, run the binary pointing to that port: ./suconnect 4444The binary will connect, read the password you send (paste the bandit20 password in term 1), and then return the bandit21 password.
