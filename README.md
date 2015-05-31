# PTES_Final-Exam-2015
MS14960628 - D.N.Fernando

##Question 1##
**A.** Date: June 17, 2014. 

**B.** 

**1)** Source: China and Unknown

Destination: Hong Kong aimed at pro-democracy site

**2)** Period: June 13,2014 and June 18,2014 (6 days)

**3)** The targets—one, the site of civil society group “Occupy Central with Love and Peace”, the other newspaper Apple Daily—both seek to advocate for universal suffrage in Hong Kong.

Occupy Central’s three web hosting services suffered violent DDoS attacks; due to the fallout of the incident, only one of these services, Cloudflare, still supports the voting system. As a proposed workaround to the voting system’s susceptibility to attacks, Hong Kong University’s Public Opinion Program is now considering using telephone lines instead, a weak alternative to the online platform.

The attacks were separately orchestrated. That said, Next Media chairman Jimmy Lai suspects that mainland Chinese hackers, eager to suppress Hong Kong’s emerging democratic impulses, wanted to silence two of the city’s most audibly pro-democracy voices through these attacks.

Occupy Central and Apple Daily’s founders both feel that Beijing loyalists are undoubtedly behind the attacks, given the subversive, pro-democratic nature of both sites in the wake of increasing tensions between Hong Kong’s pro-democracy supporters and the mainland Chinese government. That said, no particular hacking forces have come forward and taken responsibility for the attacks

![captureq1_1](https://cloud.githubusercontent.com/assets/12239510/7900493/2aa42d2e-0779-11e5-9ac3-427f68d43034.jpg)

![captureq1_2](https://cloud.githubusercontent.com/assets/12239510/7900494/2ad85144-0779-11e5-9f40-d2e703ed0db3.jpg)

Reference: [June 17, 2014](http://blogs.law.harvard.edu/internetmonitor/2014/06/20/ddos-attacks-in-hong-kong-attack-silence-pro-democracy-websites/)

**C.** China seems to be the most botnet attack generating country. United States seems to be the most targeted country. 

![captureq1_3](https://cloud.githubusercontent.com/assets/12239510/7900508/22eb2a32-077a-11e5-8b93-e2cc9dbcf150.JPG)

![captureq1_4](https://cloud.githubusercontent.com/assets/12239510/7900520/24f31a78-077b-11e5-8858-a2ecef20e338.jpg)

----------

##Question 2##
###Wargames: Leviathan
####Leviathan – Level 0
Leviathan’s levels are called leviathan0, leviathan1, etc. and can be accessed on **leviathan.labs.overthewire.org** through SSH. Data for the levels can be found in the home directories.
To login to the first level use:
	
	Username: leviathan0
	Password: leviathan0

![captureq2_1](https://cloud.githubusercontent.com/assets/12239510/7900533/69d8c196-077c-11e5-9d8c-74c0fefaa8d2.JPG)

![captureq2_2](https://cloud.githubusercontent.com/assets/12239510/7900534/69da7496-077c-11e5-8a2c-8c3dfe252eb7.JPG)

![captureq2_3](https://cloud.githubusercontent.com/assets/12239510/7900538/deaae026-077c-11e5-9f7e-14c680b6245b.JPG)

	Username: leviathan1
	Password: rioGegei8m


####Leviathan – Level 1
Leviathan 1 presents us with a binary in our home directory.  If we run it we see that is a utility that ask and checks for a password. Knowing this, the binary is either reading the correct password from somewhere else or has it stored within the binary. In this case, the password is stored within the binary. We need to trace the binary. Many of you might catch the small hint to the movie Hackers within the binary. When Plague states the 4 most common passwords are love, sex, secret, and god.

![captureq2_4](https://cloud.githubusercontent.com/assets/12239510/7900557/8d373d24-077d-11e5-8840-f5d90a83a29c.JPG)

To see what functions it is calling let’s use “ltrace”. We can see it is using strcmp() to check if an entered password is the same as it's stored string, "sex". Running the binary again we can now try entering "sex" as the password to test what will happen.

	Username: leviathan2
	Password: ougahZi8Ta

![captureq2_5](https://cloud.githubusercontent.com/assets/12239510/7900564/d3373158-077d-11e5-83c8-967656edd699.JPG)


####Leviathan – Level 2
Leviathan2 presents us with a small binary that belongs to the user leviathan3 and group leviathan2. The program contains a small security hole that can be exploited using a symbolic link.  To understand how the program functions at its core and what is happening behind the scenes when the program executes, we will use a few Linux commands and techniques to enlighten us with this information.

When you run "printfile" we can see that it wants a file path argument. We are going to go ahead and create a directory and a file with a space in the name.

![captureq2_6](https://cloud.githubusercontent.com/assets/12239510/7900700/5f28b7da-0784-11e5-82f3-3e5e8525e2ed.JPG)

So, what is happening here is a small security hole in how this program functions. We can see that the function access() and /bin/cat are being called on the file. What access() does is check permissions based on the process’ real user ID rather than the effective user ID. While access does use the full file path, the cat on the file is not using the full file path. We can see this near the end of the output where /bin/cat/ is being called to tmp.txt as if it were a separate file, it’s really the second half of our filename. This can be exploited if we create a symbolic link from the password file to the file we created in /tmp.
Let’s create the symbolic link, but with only the first part of the filename we created before the space. Issue the command ls –al; we see file tmp.txt was not converted to a symlink, but a separate symlink called "file" was created.

![captureq2_7](https://cloud.githubusercontent.com/assets/12239510/7900703/5f2c92d8-0784-11e5-91a4-c57e0f918c3b.JPG)

Calling ~/printfile on the file that actually links to the password will fail. The file did actually access the symbolic link correctly up until file, but then tried to incorrectly cat the part of the file name after the space as if it were another file.
This all works because /printfile is owned by leviathan3. Access will call the symlink with that privilege. But we also needed to utilize a syntax hack to make it work, hence the filename with a space in it.

> **ltrace** - A library call tracer
ltrace is a program that simply runs the specified command until it exits. It intercepts and records the dynamic library calls which are called by the executed process and the signals which are received by that process. It can also intercept and print the system calls executed by the program.

A symbolic link (also symlink or soft link) is a special type of file that contains a reference to another file or directory in the form of an absolute or relative path and that affects pathname resolution. A symbolic link contains a text string that is automatically interpreted and followed by the operating system as a path to another file or directory. This other file or directory is called the "target". The symbolic link is a second file that exists independently of its target. If a symbolic link is deleted, its target remains unaffected. If a symbolic link points to a target, and sometime later that target is moved, renamed or deleted, the symbolic link is not automatically updated or deleted, but continues to exist and still points to the old target, now a non-existing location or file. 
In POSIX-compliant operating systems, symbolic links are created with the symlink system call. The ln shell command normally uses the link system call, which creates a hard link. When the ln -s flag is specified, the symlink() system call is used instead, creating a symbolic link.

The following command creates a symbolic link at the command-line interface (shell):

	ln -s target_path link_path

target_path is the relative or absolute path to which the symbolic link should point. Usually the target will exist, although symbolic links may be created to non-existent targets. link_path is the path of the symbolic link.

**access()** In some situations it is desirable to allow programs to access files or devices even if this is not possible with the permissions granted to the user. One possible solution is to set the setuid-bit of the program file. If such a program is started the effective user ID of the process is changed to that of the owner of the program file. So to allow write access to files like /etc/passwd, which normally can be written only by the super-user, the modifying program will have to be owned by root and the setuid-bit must be set. But beside the files the program is intended to change the user should not be allowed to access any file to which s/he would not have access anyway. The program therefore must explicitly check whether the user would have the necessary access to a file, before it reads or writes the file. To do this, use the function access, which checks for access permission based on the process’s real user ID rather than the effective user ID. (The setuid feature does not alter the real user ID, so it reflects the user who actually ran the program.)
	
	Function: int access (const char *filename, int how)

![captureq2_8](https://cloud.githubusercontent.com/assets/12239510/7900701/5f2c12d6-0784-11e5-9361-4714b24e398c.JPG)

	Username: leviathan3
	Password: Ahdiemoo1j


####Leviathan – Level 3
For Leviathan 3, the process is fairly simple.  We only need a few commands that we are already familiar with to get access to Leviathan4’s shell. When we finally do get his shell, we can cat his password in /etc/leviathan_pass/leviathan4 and correctly log in as him to begin level 4->5.

![captureq2_9](https://cloud.githubusercontent.com/assets/12239510/7900704/5f2e14f0-0784-11e5-84c8-ffb1c014fc8f.JPG)

![captureq2_10](https://cloud.githubusercontent.com/assets/12239510/7900702/5f2c9f44-0784-11e5-8ec9-74f499edb9b0.JPG)

The point of interest for us will be the snlprintf word. Right after that you see the words, "You've got shell!” Let's use that as the password.

![captureq2_11](https://cloud.githubusercontent.com/assets/12239510/7900705/5f2f2796-0784-11e5-9af2-e95ded249f6d.JPG)

All are familiar with snprintf() in C, but not snlprintf(). So we can assume it is a made up function or actually is the password. Clearly it is a visible string in the binary. If you follow the logic in the strings output, you can crudely tell this is what pops the shell in main.

	Username: leviathan4
	Password: vuH0coox6m

![captureq2_12](https://cloud.githubusercontent.com/assets/12239510/7900706/5f5d0ab2-0784-11e5-8845-891d91766268.JPG)


####Leviathan – Level 4

![captureq2_13](https://cloud.githubusercontent.com/assets/12239510/7900709/5f6425fe-0784-11e5-8c06-236f2dd5d087.JPG)

Covert binary values to ASCII and obtain the password.

![captureq2_14](https://cloud.githubusercontent.com/assets/12239510/7900708/5f63ea3a-0784-11e5-8da2-88da4d6d03b3.JPG)

	Username: leviathan5
	Password: Tith4cokei

![captureq2_15](https://cloud.githubusercontent.com/assets/12239510/7900710/5f657db4-0784-11e5-928c-d2b02b7dbd41.JPG)


####Leviathan – Level 5
In Leviathan 6, we will exploit a binary by using symbolic linking to a file we have permission of. When we run the binary in Leviathan5’s home directory, it appears to be attempting to read from a file in /tmp. The binary is owned by Leviathan6 but belongs to the Leviathan5‘s group. Therefore, it can pull Leviathan6’s password. We successfully exploited bad permissions placement on files via symlinking and now have Leviathan6’s pass as a result.

![captureq2_16](https://cloud.githubusercontent.com/assets/12239510/7900707/5f614f78-0784-11e5-8634-51a28af79286.JPG)

	Username: leviathan6
	Password: UgaoFee4li

![captureq2_17](https://cloud.githubusercontent.com/assets/12239510/7900711/5f9096ca-0784-11e5-9b37-504a81f74251.JPG)


####Leviathan – Level 6
The final stage of Leviathan presents us with a problem that can be solved via some quick bash scripting. The binary in our home directory accepts a 4 digit combination as the password. Iterating through all the possible combination manually would take too much time. So we create a bash script to do this in a matter of seconds.

![captureq2_18](https://cloud.githubusercontent.com/assets/12239510/7900712/5f967284-0784-11e5-8087-efd2fc99bd36.JPG)


Run the binary and simply wait for it to iterate through all possible number combinations. Note that we could echo out the password when we find it. Here the script will simply input the pass to the binary and pop us into leviathan7’s shell when it finds a match.

![captureq2_19](https://cloud.githubusercontent.com/assets/12239510/7900714/5f9a0bce-0784-11e5-9dff-3ca0546522e1.JPG)

	Username: leviathan7
	Password: ahy7MaeBo9

![captureq2_20](https://cloud.githubusercontent.com/assets/12239510/7900713/5f995242-0784-11e5-9f22-a5f39635151d.JPG)

####Leviathan – Level 7

![captureq2_21](https://cloud.githubusercontent.com/assets/12239510/7900715/5fc46680-0784-11e5-8fb5-664d9d6465ec.JPG)

----------

##Question 3##

**A.**

**a)** the /etc/passwd file? used to change the user password in UNIX like environments.

**b)** the /etc/shadow file? stores the encrypted password and other details such as account & password expiration times.

**c)** the setuid bit? set user id upon execution. these are access rights flags it contains the owner , group , user execution permissions.

**d)** chroot? a command that changes apparent root directory

**B.** *lsattr* command lists the file attributes on a second extended file system and *ls -l* shows the long listing information about the file or directory

**C.** Android system implements the principle of least privilege. This means that each app, by default, has access to only the components that it requires to do its work and no more. The granularity of control that the OS has over privileges of an individual process is limited. In practice it is very difficult to  control a process's access to memory, processing time, I/O device addresses or modes with the precision needed to facilitate only the precise set of privileges a process will require.

**D.** Access control lists provides an additional, more flexible permission mechanism for file systems. ACL allows you to give permissions for any user or group to any disc resource. It is designed to assist with unix file permissions.
In standard unix permission models, the ownership of the files is an important component. Access to the files  and read, write, execute permission depends on the permissions given by the owner or group. 

**E.** *ruid* is used to identify who the user is actually. once it is setup by the system, it cannot be changed till your session terminates. Users cannot change the ruid. only the root can change it.
*euid* is used to determine what level of access the current process has. when the euid is zero then the process has unlimited access

**F.** In a unix system, Syslog file provides information about the activities of the machine, such as the user activity and history.IF the attacker clears this file, the illegal activities cannot be discovered.

Altering accounting files in unix is another method of covering tracks. utmp, wtmp, and lastlog files are the main accounting files in unix

----------

##Question 4##
**A.** mov DWORD PTR [ebp-0x4], 0x8
moves the memory address 0x8 into the location pointed to by EBP, such as pushing a value onto the stack so we can work with it.

**B.** mov eax, DWORD PTR [ebp+0x8]
moves value at epb+0x8 into EAX 

**C.** lea eax, [ecx + eax*1]
the quantity ecx+eax*1 is placed in EAX

**D.** call _htons
call _htons function - call puts the address of the next instruction on the stack  so the program can return to it

**E.** cmp [ebp+0x8], 0
compare the values of the two operands ebp+0x8 and 0

----------

##Question 5##
Make both Kali Linux and Vulnix Host only.

![captureq5_1](https://cloud.githubusercontent.com/assets/12239510/7900939/3c8a935a-078f-11e5-8c76-ea2eb04bbf0f.jpg)

Then do a netdiscover to find it IP addresses.

![captureq5_2](https://cloud.githubusercontent.com/assets/12239510/7900940/3c993202-078f-11e5-978a-e9b6d1db934e.jpg)

![captureq5_3](https://cloud.githubusercontent.com/assets/12239510/7900941/3c9d094a-078f-11e5-9c80-0a2376505659.jpg)

![captureq5_4](https://cloud.githubusercontent.com/assets/12239510/7900942/3caaca26-078f-11e5-9cea-85fa4e235ce3.jpg)
