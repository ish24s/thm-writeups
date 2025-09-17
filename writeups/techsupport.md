## [Tech_Supp0rt: 1](https://tryhackme.com/room/techsupp0rt1)

**Topics:** Enumeration, Scanning, PrivEsc

**Difficulty:** Easy

---

## 📝 Room Description
This is a room with no specific focus. It covers a little bit of everything. There is also not a set way to do certain things. e.g enumeration is flexible; more than one tool can be used and there are multiple approaches. 


---

## 🔎 Enumeration
### Nmap
- Running a nmap scan using `nmap -p- -Pn -sV -sC -T4 -oN TSnmap.txt $ip`we can see that there is a web server, ssh port and smb running.
> Note: I may have extra input compared to your scan due to -sC which runs default nmap scripts. Would recommend using these as gives more information and potential vulnerabilities of certain services.

![nmap](../images/Tech%20Support/nmap.png)

### SMB
- We can now connect to targets SMB and list available shares using `smbclient -L //$ip -U`

![smb1](../images/Tech%20Support/smbclient.png)

- This shows 3 shares. $IPC is a default share which isnt accessible. $print isnt important. As this machine has a web server and there is a share called websvr this is probably what were looking for.
- We can connect to this share using `smbclient //$ip/websvr/ -U`
> Note: From my nmap script we can see that they allow guest login therefore it doesnt matter what user we type in we can log in as guest.

![smb2](../images/Tech%20Support/smbclien2.png)

- By reading the text file we see a few interesting things
1. There is a page called /subrion which doesnt work and involves a panel?
2. We also get a admin credential for subrion which is possibly encoded?

![enter](../images/Tech%20Support/entertxt.png)

### Gobuster/ffuf
- By running gobuster/ffuf using `gobuster dir -u http://$ip/ -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt` we can see that there are 2 directories.
- /test doesnt show anything interesting but /wordpress shows us a website.
 
![web](../images/Tech%20Support/subrion.png)
 
- As it is a wordpress website it likely contains lots of subdirectories (could maybe find some accidentally made public config files)  which we can find using ffuf or gobuster. However this doesnt result in anything useful. There is a wp-admin but not relevant right now.
- Looking back at the txt file we can try looking up the /subrion directory however it hangs and doesnt give us a page.
- Using `curl -v $ip` we can see that it gives a 302 redirect. We can enumerate this further aswell by using gobuster/ffuf. Gobuster may give an error so by using ffuf we can find a subdirectory called /panel which relates to txt file.
> Tip: by using -fc 302 for ffuf we can exclude all 302 redirects.

![curl](../images/Tech%20Support/curl302.png)

![ffuf](../images/Tech%20Support/ffuf.png)

- Alternatively we could check /subrion/robots.txt to check any hidden subdirectories or domains.

![robots](../images/Tech%20Support/robotstxt.png)

- Now we can access the panel page

![panel](../images/Tech%20Support/panel.png)


### Subrion
- To log in we can try admin and the password however it doesnt work which means we need to encode it.
- Using cyberchef and the magic option (magic option tries various decoding techniques automatically - faster alternative then trying trying to decode manually)

![cc](../images/Tech%20Support/cc.png)

- It was encoded in base58 and now we have the password.
- Logging in with Scam2021 and admin we arrive at the dashboard.
> Note: Subrion is a CMS platform which manages webpages.

### Exploitation
- Subrion is running v4.2.1 which is vulnerable to RCE and has an exploit on exploitdb.

![db](../images/Tech%20Support/exploitdb.png)

- By downloading it and running it with suitable parameters we get a shell.

![shell](../images/Tech%20Support/shell1.png)

### PrivEsc
- By looking around we can try priv esc to another user. By reading etc/passwd we can see another user called scamsite.
- Furthermore by looking around the /wordpress directory we find wp-config.php which is a very important file and contains the wordpress pages configurations.

![wordpress](../images/Tech%20Support/wordpress.png)

-By reading the file we see a password which might possibly be for ssh and the scamsite user.

![ssh](../images/Tech%20Support/sshpass.png)

- Now we can login to using ssh with `sudo ssh scamsite@$ip`

![sshlogin](../images/Tech%20Support/sshlogin.png)

- Now we can try various methods to privilege escalate to root. However one way is to use `sudo -l` which lists any commands we can run as root without needing a password. Conveniently it works for us.

![sudol](../images/Tech%20Support/sudol.png)

- Now we have a binary we can use to privilege escalate. To exploit this we need to go go [GTFOBins](https://gtfobins.github.io/) and search iconv to find methods to exploit the binary.

![gtfo](../images/Tech%20Support/bins.png)

- From here we can try read the root file as we know its called root.txt. First we do `LFILE=/root/root.txt`. This gives us a parameter we can input into the next command. We put /root due to us not having access to the root directory so we have to give the full path. Now we use `iconv -f 8859_1 -t 8859_1 "$LFILE"` to read the root file which will give us the flag.
> Note: I have not displayed the flag to prevent cheating. The command below results in the flag.

![root](../images/Tech%20Support/root.png)

---

## Afterthoughts
- Overall not incredibly complex but still some hard parts including the privilege escalation. For more info on Linux PrivEsc I highly recommend ![this](https://tryhackme.com/room/linprivesc) room on THM. Shows a lot of techniques including many the one in this challenge.
- This also encourages using various tools for different areas such as gobuster and ffuf due to gobuster throwing errors.

---

### Questions or Issues
- Discord (ish24s)
- Email (ismaeelj888@gmail.com)
