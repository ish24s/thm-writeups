##  [VulnNet: Internal](https://tryhackme.com/room/vulnnetinternal)

**Topics:** Enumeration, Scanning, Recon, Reverse-shell, PrivEsc

**Difficulty:** Easy

---

## 📝 Room Description
This is a room that highly focuses on internal networks/systems rather than more common areas like  web-applications. Most of the work will be done in the terminal instead of on a browser.

> Note: Research is your best friend.
> Especially for beginners, many of the tools and systems/applications in this room may be new to you so if something looks unfamiliar please take the time to do research before consulting writeups.


---

## 🔎 Enumeration
### Nmap
- First we run a basic nmap scan to see which ports are running. i used `sudo nmap -sC -sV -p- -Pn -T4 -O $ip --min-rate 1000 -oN vulnetnmap.txt`
> Tip - instead of having to type in your ip every time type `export ip=machineip` where machineip is the actual ip. this makes a variable called ip which you can call using $ip. therefore instead of typing ip again and again you can just use $ip as the ip.

![Nmap Scan](../images/vulnnet_internal.png)

- As seen there are many ports since this is a network based ctf.
- Some well-known ones we can see are smb and ssh.
### SMB
-dwa

