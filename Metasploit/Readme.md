![ZTW Logo](../Assets/Hacking_Labs_graphics_ztw_logo_med_1.png)

# TL25MHLDev

In this lab we perform two hacks against two intentionally vulnerable VM
targets. The first target is a Metasploitable VM which is a great target
to practice on as it has many vulnerabilities. The second target is a
dated Windows 7 VM that has not been patched for the critical
eternalblue exploit.

We begin each hack the same way, by doing recon with NMAP. For the first
attack, we already know the IP address.

<img src="Assets/04842d39996d808320216f19b734b36b7f410b92.png"
style="width:6.5in;height:6.45834in" />If we wanted to go even further,
we could use the –p## parameter to specify a port, but it is not
required to do so at this time.

From the results above, we can see that port 21 is open and that this
port is commonly used for ftp. We can try to connect to that port using
netcat to demonstrate the first hack.

<img src="Assets//cbc33e829d63d80168c2a7891a7084c1160a5696.png"
style="width:3.52796in;height:2.45846in" />

Once we connect to the machine with netcat, we see it is running the
vulnerable version of vsFTPd, so we type in a username that ends with
“:)" and any password. Nothing happens of course, because that username
and password combination do not exist, so we exit with ctrl+c.

Using nmap again, we can peek at the port 6200 and now see that it is
open. Not that unless we use the –sS parameter, the port will close and
we will need to connect with netcat again. In my testing, it sometimes
closes the port even if –sS is used.

<img src="Assets/30f88cb08a33fefb67f5c062bb1d2100fd87ac0c.png"
style="width:6.5in;height:2.46875in" />Once we are sure the port is
open, we can connect to it with netcat and verify we are connected using
command like ”id”, ”ls”, and ”ip a”.

<img src="Assets/e21c65449e3088cf34dea541c541e4626bbba4a0.png"
style="width:5.94792in;height:6.5in" />

Now that we have demonstrated a hack without metasploit, let’s see how
much easier it is when using metasploit. Metasploit can be launched with
the msfconsole command or by selecting it from the start menu.

<img src="Assets/208f96739c9e119c2deb0bdeffef1ec8e7100a95.png"
style="width:6.5in;height:4.52083in" />

The prompt will change from kali to msf6 once we are in. A random ascii
image will be present as well, it might be different from the one your
neighbor gets. We can search for the module we wish to use, in this case
the vsFTPd exploit. We are going to use the one ranked excellent.

<img src="Assets/33c953ad05edcf3070d92a353ba9950d8d185a72.png"
style="width:6.5in;height:2.10417in" />

Once loaded, we can show the options to see that the only required one
without a default setting is rhosts, so we set that one to the IP of our
metasploitable VM. Then we simply run it and wait a moment. Sometimes it
needs to be run twice.

<img src="Assets/b71fbf5a78dfa560b1a8e04842ef7c521b533980.png"
style="width:6.5in;height:3.6875in" />

We can verify we are in using the same command as earlier, but we can
also do more, like shutting down the server.

<img src="Assets/a049210540ce7c44f3bdf33702adcf2d835acb03.png"
style="width:6.5in;height:5.13542in" />

We can now close and revert this VM.

For our next hack, we will launch the Blue VM. It is a windows 7
machine. Silly me forgot the passwords to the VM, so we can’t even login
to get the IP addresses, which I’ve also conveniently forgotten as well.
Luckily, nmap can save us here.

<img src="Assets/c51750971659659a8a4dddfa922713f031df8f27.png"
style="width:6.5in;height:5.32292in" />

A basic scan of the whole subnet reveals our VM’s IP. Now we can search
for eternalblue.

<img src="Assets/50216777a267e21cfe2db1f5f39aaf2f239b2d35.png"
style="width:6.5in;height:2.79167in" />

We’re going to use the one under
exploit/windows/smb/ms17_010_eternalblue, then show options.

<img src="Assets/55bc1d3b0133a12f74577f194bb97cdfe11efb70.png"
style="width:6.5in;height:5.23958in" />

We can see we need to set rhosts again, but also that we should change
lhost to our ip. To get our IP we use the “ip a” command.

<img src="Assets/1f5f8562a10cb7e51a9c78e0d58c73a5f3da37c6.png"
style="width:6.5in;height:3.53125in" />Once set, we can run the exploit
and wait while it performs a series of complicated tasks that hopefully
ends with a win message and a meterpreter prompt.

<img src="Assets/9a87861af27eb7caeb88b1cc26b99d1b26ee0bfe.png"
style="width:6.5in;height:3.97917in" />

That was easy, but what can we do now? Shutting down the machine doesn’t
help us login to it. We can run the command “hashdump” to get the user
password hashes.

<img src="Assets/2d1c939018a1f26b42579162a1960655d781cea2.png"
style="width:6.5in;height:0.95833in" />

Let’s open a new terminal window and type “mousepad passwerds.txt.” and
paste those hashes into it. We can then save and close the text file but
keep the terminal window open.

<img src="Assets/4f560d9b3afc11fb49e9ebe052421f2bc869e7be.png"
style="width:6.5in;height:3.79167in" />

We can then launch the wordlists from the start menu by going to
start-\>05 Password Attacks-\>Password Profiling &
Wordlists-\>wordlists. Select Y on the window that pops up.

<img src="Assets/c308a143315c2b053a4145c7e8b360430f1c34ee.png"
style="width:6.5in;height:5.05208in" />

<img src="Assets/8eb7d5dc4fb76aa0ca6d8bc962b7ac1cfe3f5ae2.png"
style="width:4.84375in;height:6.5in" />

After typing in Y, you may be asked for the sudo password, it’s kali.

Now that we have both the passwerds.txt file, and our rockyou.txt
wordlist, we can use john the ripper to crack the hashes we obtained
from the Blue VM. In the same terminal window we used to type the
mouspad command, or a new one. Launch john using the following command:
john --wordlist=/usr/share/wordlists/rockyou.txt passwerds.txt
--format=NT

<img src="Assets/1498e44c6a16977f861d145449135582e73ed4c7.png"
style="width:6.5in;height:3.30208in" />

This is the reason you have been told not to use a single word or
variation thereof as your password. If any of the passwords listed in
rockyou.txt are yours, change them. I recommend using a short sentence.

<img src="Assets/107fa58836617e62b234bb9c562ded0dd7dbc7f9.png"
style="width:6.5in;height:5.28125in" />

If you see something like the output below instead of the output above,
it means that someone has already cracked those passwords on this
machine and has not cleaned up after themselves.

<img src="Assets/37e5fa19498cdb9dd5cd3a0871542828f4552d0c.png"
style="width:6.5in;height:0.89583in" />

To force John to forget that the passwords have been cracked, type the
following, using tab when necessary to autocomplete.

<img src="Assets/4f52d98ba20e6921889001750f7b178fe67727df.png"
style="width:2.11458in;height:0.54167in" />

Now you can run the John command from above by pressing the up-arrow key
and it should give you the cracked passwords.

Alternatively, since they’d already been cracked, you can just open the
john.pot file to see them using the following command.

<img src="Assets/2dc987891a2c8773197e0067ec57512829298d5a.png"
style="width:2.64583in;height:0.53125in" />
