# Remote Node Management: SSH over Tor

## Prelude and Objective ##
Running a lightning node requires maintenance of the underlying hard- and software because it should stay in the game for the long run. Most nodes are run by the usual guy and gal next door which definitely means "we got a life out there". Believe it or not ;)

A node should have its channels available for lightning payments as much as it can. Longer downtimes are bad considering lightning a globally active payment network. You don't want to queue up a line of pissed people behind yourself, telling them you currently can't pay because your lightning node has Tor issues, right? So maintenance is crucial and it's good practice if you're able to do it wherever you are. 

## Table of Content ##

- [Preconditions](#preconditions)
- [Configuring Tor](#configuring-tor)
- [Remote Access](#remote-access)
- [Establishing the Connection](#establishing-the-connection)
- [Securing SSH](#securing-ssh)

## Preconditions: ##

Setting up SSH over Tor requires some technical knowledge about the infrastructure of your node. This guide is based on a bare metal setup assuming this can also be done on Umbrel-OS, RaspiBolt, RaspiBlitz and MyNode setups as well. But don't take it for granted! Please, DYOR.

Some more assumptions:
- Node runs behind Tor
- SSH service is installed (if not `sudo apt-get install openssh-server`)
- Tor client available on mobile phone ([Orbot](https://github.com/guardianproject/orbot))

## Configuring Tor: ##

1) First we going to add an additional hidden Tor service to our node's setup. To do so SSH into your node and find the needed `torrc` file. Example of paths to this file:
````
/etc/tor/torrc (Bare Metal / RaspiBolt / RaspiBlitz)

~/umbrel/tor/torrc (Umbrel-OS)
````

2) Open `torrc` with `sudo nano /etc/tor/torrc` (path from above), look for the section which contains the hidden services and add the following entry:
````
# SSH over Tor
HiddenServiceDir /var/lib/tor/ssh
HiddenServicePort 22 127.0.0.1:22
````
`HiddenServiceDir` may be adjusted to another path, look for other hidden services and their corresponding dirs to match them.

3) Reload Tor config:

on bare metal:
````
$ sudo systemctl restart tor
````
on Umbrel-OS:
````
$ cd ~/umbrel && docker-compose reload tor
````

4) Get the onion address:
Get the hostname / onion address with the following command. Because it's a long address, it's good to save it to file or write it down manually because we need to type in the address on the remote device. 
````
$ sudo cat /var/lib/tor/ssh/hostname
3uz5g2i5g43gtihit7h32t2itgxxxxxxxxxxxxxxxxx.onion
````


## Remote Access: ##

The node setup is done. Now we're going to setup the remote device. In this guide I will use an android mobile phone and the apps:
- [Orbot (Tor client)](https://github.com/guardianproject/orbot)
- [JuiceSSH](https://juicessh.com) (for iOS: there's Termius or whatever you like)

Setup the app the following way:
1) Open Orbot and add JuiceSSH to `tor-activated apps` list
2) Start Orbot (connect to Tor network)
3) Open JuiceSSH within Orbit
4) Create a new identity with your node's main user (Umbrel's user is named `umbrel`)
5) Create a new connection and set up the following parameters:
- Name: < name your node nicely >
- Address: 3uz5g2i5g43gtihit7h32t2itgxxxxxxxxxxxxxxxxx.onion (onion address we wrote down or saved before)
- Port: 22 (ssh port from hidden service and openssh-server (if we configured it manually))


Save your configs. That's it. 


## Establishing the Connection: ##

So finally we connect the app with our node. 
1) Open Orbot and start Tor
2) Start JuiceSSH from within Orbot
3) Select your setup node connection and wait for it to establish (may take a little while)
4) Send your first command on terminal


## Securing SSH: ##
There are several ways to secure your SSH connection with 2FA, MFA, client authentification, etc. I will just link some guides:
- [Yubikey](https://developers.yubico.com/yubico-pam/YubiKey_and_SSH_via_PAM.html)
- [OTP-2FA](https://www.simplified.guide/ssh/use-otp-2fa)

If you decide to do with OTP-2FA, JuiceSSH will ask for OTP-Code as a second factor aside from the specified user's password.

_______________________________________________________________

Written by [osito](https://github.com/blckbx).
  
If this guide was of help and you want to share some ♥ and contribution, please feel free to send a ⚡ tip to the ⚡ address:
  0x382f9cf667447bb8@ln.tips (osito)