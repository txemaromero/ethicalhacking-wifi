# ethicalhacking-wifi

## What is ethical hacking and what is it used for?

Ethical hacking involves a hacker agreeing with an organisation or individual who authorizes the hacker to levy cyber-attacks on a system or network to expose potential vulnerabilities. An ethical hacker is also sometimes referred to as a white hat hacker. Many depend on ethical hackers to identify weaknesses in their networks, endpoints, devices, or applications. The hacker informs their client as to when they will be attacking the system, as well as the scope of the attack.

## WiFi ethical hacking

We should find out threats and vulnerabilities in those systems (and mitigate them).

For example, WiFi networks provide us with convenient and fast connectivity. However, the vulnerability of WiFi networks to online attacks makes them an attractive target for malicious hackers.

In this report, we explore common techniques used to demonstrate WiFi vulnerabilities (e.g., weak passwords).

## Implementation

We create 7 Linux Bash scripts:

show.sh (omitted file in the repository)  
usage: sh show.sh $1 (interface)  
output: all WiFi networks info

listen.sh (omitted file in the repository)  
usage: sh listen.sh $1 (BSSID) $2 (channel) $3 (interface)  
output: start listening for the handshake

deauth.sh (omitted file in the repository)  
usage: sh deauth.sh $1 (BSSID) $2 (client) $3 (interface)  
output: deauth attack

crack.sh (omitted file in the repository)  
usage: sh crack.sh  
output: key found

## Execution

We will be using Kali Linux. Firstly, install the dependencies:

```
sh initial_wifi.sh
```

Switch WiFi interface ($1) to monitor mode:

```
sh monitor.sh $1
```

Then, type and execute:

```
sh show.sh $1
```

WPA/WPA2 uses a 4-way handshake to authenticate devices to the network. You do not have to know anything about what that means, but you do have to capture one of these handshakes in order to crack the network password. These handshakes occur whenever a device connects to the network, for instance, when your neighbor returns home from work. We capture this handshake by directing to monitor traffic on the target network using the BSSID ($1) and channel ($2) values discovered from the previous command.

```
sh listen.sh $1 $2 $3
```

Now we wait. Once you have captured a handshake, you should see something like `[ WPA handshake: bc:d3:c9:ef:d2:67` at the top right of the screen, just right of the current time.
Once you have captured a handshake, press ctrl-c.
Now, leave `listen.sh` running and open a new terminal. We will use the `deauth.sh` script to send fake deauth packets to our victim client, forcing it to reconnect to the network and hopefully grabbing a handshake in the process.

```
sh deauth.sh $1 $2 $3
```

Once you have sent the deauth packets, head back over to your `listen.sh` process, and with any luck you should now see something like this at the top right: `[ WPA handshake: 9C:5C:8E:C9:AB:C0`. Now that you have captured a handshake you should be ready to crack the network password.

```
sh crack.sh
```

If the password is cracked you will see a `KEY FOUND!` message in the terminal followed by the plain text version of the network password.

## Detecting hidden SSID

When the SSID is not visible, we can scan this network using its BSSID ($1).

```
sh hidden/visible.sh $1 $2 $3
```

Just wait for someone to reconnect. And sure enough, after waiting for a while we see that a client has connected and we are able to retrieve the SSID.

hidden/visible.sh (omitted file in the repository)  
usage: sh visible.sh $1 (BSSID) $2 (client) $3 (interface)  
output: SSID network name

## DoS attack

A Linux Bash script performs a Denial of Service (DoS) attack by sending infinite deauthentication frames.

```
sh dos/inhibit.sh $1 $2 $3
```

dos/inhibit.sh (omitted file in the repository)  
usage: sh inhibit.sh $1 (BSSID) $2 (client) $3 (interface)  
output: DoS attack

## MAC spoofing

Finally, to change the Media Access Control (MAC) address of a device:

```
sh mac/change.sh $1
```

mac/change.sh (omitted file in the repository)  
usage: sh change.sh $1 (interface)  
output: changing its MAC address

## Reference

[Wordlists 24] Wordlists. Kali Linux Tools. Tool Documentation. June 2024.  
Available at: https://www.kali.org/tools/wordlists/

## Disclaimer

This software is intended solely for educational purposes and should not be used for illegal activities. The author disclaims responsibility for misuse of the software. Users are urged to act responsibly and ethically.
