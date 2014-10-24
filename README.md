Docker! Skype! PulseAudio!
==========================

Run Skype inside an isolated [Docker](http://www.docker.io) container on your Linux desktop! See its sights via X11 forwarding! Hear its sounds through the magic of PulseAudio and SSH tunnels!

Known Issue: While audio works flawlessly during calls and Skype is perfectly usable, the notification sounds such as call ringing do not work.

Docker index
============

The easiest method to get Skype in docker is to download the already prepared image from the official Docker image index repository: [tomparys/skype](https://index.docker.io/u/tomparys/skype/). Follow further instructions there.

Building Instructions
=====================

In case you do not want to download the prepared image, you can built the image yourself using these following instructions.

1. Enable network access to your local PulseAudio devices

	gconftool --type bool --set /system/pulseaudio/modules/remote-access/enabled true

2. Restart PulseAudio

        sudo service pulseaudio restart
   
    or
   
        pulseaudio -k
        pulseaudio --start

    On some distributions, it may be necessary to completely restart your computer. You can confirm that the settings have successfully been applied using the `pax11publish` command. You should see something like this (the important part is in bold):

    > Server: {ShortAlphanumericString}unix:/run/user/1000/pulse/native **tcp:YourHostname:4713 tcp6:YourHostname:4713**
    
    > Cookie: ReallyLongAlphanumericString

3. [Install Docker](http://docs.docker.io/en/latest/installation/) if you haven't already

4. Clone this repository and get right in there

        git clone https://github.com/tomparys/docker-skype-pulseaudio.git && cd docker-skype-pulseaudio

5. Build the container (this will create an image named 'skype-pulseaudio')

	make

6. Run skype

	make run

7. Go use Skype in a safe container!


Frequently Asked Questions
==========================

Why would I want to do this?
----------------------------
There are a couple of reasons you might want to restrict Skype's access to your computer:

* It is proprietary Microsoft software
* The skype binary is disguised against decompiling, so nobody is (still) able to reproduce what it really does.
* It produces encrypted traffic even when you are not actively using Skype.


Is is fully safe then?
----------------------
Hell no!

* the X11 protocol is **not secure** at all, the skype process has full access
  to your display server. Especially it can take screenshots and record
  keystrokes. This can be mitigated by isolating the skype process in a
  separate display server (like a headless vnc server).
* the default iptables rules set by the docker daemon is to allow any input
  connection from the DOCKER interface. Insecured services running on your host
  are vulnerable.
* docker is not mature, there might be ways to escalate privileges

