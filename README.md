# Easy Box

This project is a simple vulnerable machine.

## Description

The goal of this project is use a combination of enumeration and expliots to first obtain a foothold on the target machine and acquire the first user flag.  From there, we escalate privileges to acquire root access and find the final flag.  

## Languages and Utilities Used

- NMap
- Metasploit
- PHP
- Linux

## Program Walkthrough

### The first step for us is enumeration, so we run a simple NMap on the provided IP:

![Simple NMap](https://i.imgur.com/hcsTI64l.png)

### We find two open ports, 22 and 80.  Let's ensure that there aren't any other ports with a deeper scan:

![sV NMap](https://i.imgur.com/0TqHEjNl.png)

### Finding no other ports, we know there's a webserver run on Apache. Let's check the webpage:  

![GetSimplePage](https://i.imgur.com/NuGKOd2l.png)

### Checking the pages source code reveals nothing, so let's run an http-enum script on the webserver through Nmap:

![HTTP-Enum](https://i.imgur.com/UVJZs9Pl.png)

### We attempt to acces the /admin/ domain, but the page won't load.  We'll table that for now and check the other pages for useful information:

![/data/](https://i.imgur.com/Jx27vSFl.png)
![/data/users](https://i.imgur.com/4Z32BZ7l.png)
![/data/users/admin.xml](https://i.imgur.com/39tfnoIl.png)
![/data/cache](https://i.imgur.com/T66gv1El.png)
![/data/cache/<SNIP>.xml](https://i.imgur.com/Qy1AWqWl.png)

### Digging through the pages we found through the http-enum, we find two intesting things.  The first is an admin.xml file that contains login credentials for the admin account.  Unfortunately, the /admin/ domain still won't load, so let's see if we can find another way.  If the admin page had a login portal, we could use the found credentials and try an injection attack. We could attempt to cURL into the page, but as it's not responding or responding very slowly, we can try another method. If that doesn't work we can come back to this. In the cache folder we find a message saying the version is 3.3.15 and should be updated to 3.3.16.  Let's check for exploits on GetSimple 3.3.15. 

![Searchsploit](https://i.imgur.com/IZSUIlbl.png)

### Searchsploit is fruitless, so let's check Google for exploits:

![Exploit-DB](https://i.imgur.com/ECxDHwKl.png)

### On Exploit-DB, we find a Remote Code Execution exploit for GetSimple 3.3.15 we can run through Metasploit.  Let's see if we can use it get a foothold. Searching Metasploit, we find the RCE that we're looking for.  Let's load it up.

![Metasploit Search](https://i.imgur.com/Z18hBWkl.png)

### We select it with use 1, and then check the options.  All it needs is an RHOST, so we add one:

![Exploit Options](https://i.imgur.com/STXdohRl.png)
![RHOSTS set](https://i.imgur.com/724hxmrl.png)

### We're also going to change the payload to give us a reverse shell instead of a reverse tcp:

![Reverse Shell](https://i.imgur.com/iKSOny7l.png)

### Running the exploit gives us a shell:

![Metasploit Shell](https://i.imgur.com/qrslWT5l.png)

### Let's see if we can use Python to upgrade our shell:

![Shell Upgraded](https://i.imgur.com/uQeA1Enl.png)

### Now that we're in, we navigate to the home folder and check for users.  There's one user.  Navigating into that folder shows us the first user.txt file.  We try to read the file, and find the first flag:

![Flag One](https://i.imgur.com/fc9lzOTl.png)

### Our next goal is to become root and find the last flag.  Let's check who we are, and if we have any privileges:

![Check Privileges](https://i.imgur.com/OgGCAxVl.png)

### It turns out that we have the ability to run PHP as sudo without needing a password, which opens up a lot of options, including trying to open a bash shell. We check GTFObins to see what we can do with PHP:

![Sudo Code](https://i.imgur.com/cE32gYkl.png)

### Using the information we found on GTFObins, we write the command to execute in the shell we currently have, navigate to the /usr/bin folder and execute it:

![Success](https://i.imgur.com/nzaY0Gal.png)

### We receive the root shell in return.  Now we can navigate to the root directory, and find the flag:

![Final flag](https://i.imgur.com/WjmJoGcl.png)

## Conclusion

This was just one way into this particular box.  If the admin log-in page loaded, we could have tried the credentials that we found.  Even if they didn't work, we could have attempted to brute force the password using admin as the username if able using hydra after obtaining the PHP form through Burp Suite. 

It also may have been neccesary to use LinEnum or LinPEAS to search for vulnerabilities if our ability to run PHP code as sudo hadn't been such a glaring weakness. For me, this was the first box that I did on my own from start to finish using the tools and skills I've recently acquired.  It was a fantastic learning experience, and I'm looking forward to solving more boxes and continuing to grow my skillset.
