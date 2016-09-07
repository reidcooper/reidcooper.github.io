---
layout: post
title:  Etherpad On Raspberry Pi
date:   2015-04-19 15:00:00
summary: Basic installation of Etherpad on Raspberry Pi
categories: programs
---

Here is my guide for Etherpad Set up on Raspberry Pi

Requirements:
Raspbian Installed and Configured

Steps:

1. Run "sudo apt-get update"
2. Run "sudo apt-get upgrade"
3. Run "sudo apt-get install gzip git-core curl python libssl-dev pkg-config build-essential npm"
4. Run "cd ~"
5. Run "git clone https://github.com/ether/etherpad-lite"
6. Time to install Node.js
    * Run "wget http://node-arm.herokuapp.com/node_latest_armhf.deb"
    * Run sudo dpkg -i node_latest_armhf.deb
7. Type "node -v" to determine the version, it must be at least version v.0.10.xx
    * mine says v0.12.1 which should be yours but anything over 10 is good
8. Add node to your bashrc path
    * Run "nano ~/.bashrc"
    * At the bottom of the file add, "export PATH=$PATH:/usr/local/bin"
    * Save and exit
    * Run "source ~/.bashrc"
9. cd into the location where you cloned the etherpad repo, most likely "cd ~/etherpad-lite/"
10. Run "sh /bin/run.sh"
11. For the first time, its going to take a long time, let it do its thing and wait until you see something like this (If you get a bunch of errors and warnings, don't panic yet. Try to re-run the step 10 command):

	```
	[2015-04-19 14:26:46.821] [WARN] console - DirtyDB is used. This is fine for testing but not recommended for production.
    [2015-04-19 14:27:29.814] [INFO] console - CODEPAD needs ep_codepad parameters in settings.json.
    [2015-04-19 14:27:33.518] [INFO] console - Installed plugins: ep_adminpads, ep_codepad, ep_cursortrace, ep_desktop_notifications, ep_etherpad-lite, ep_fileupload, ep_webrtc
    [2015-04-19 14:27:35.686] [INFO] console - Report bugs at https://github.com/ether/etherpad-lite/issues
    [2015-04-19 14:27:35.747] [INFO] console - Your Etherpad version is 1.5.6 (d31523a)
    [2015-04-19 14:27:39.898] [INFO] console - You can access your Etherpad instance at http://0.0.0.0:9001/
	[2015-04-19 14:27:39.903] [INFO] console - The plugin admin page is at http://0.0.0.0:9001/admin/plugins
       ```
    (If you get a bunch of errors and warnings, don't panic yet. Try to re-run the step 10 command)

12. In your web browser, type in "YOUR PI ADDRESS:9001"
13. Create a pad and select go
14. Now you have a pad, that URL is shared between any device that wants to connect to this pad
15. Quit the script from terminal, "ctrl+c"
16. Time to add Admin Rights:
	* Run "cd ~/etherpad-lite/"
    * Make a copy of the files, "cp settings.json settings.json-backup"
    * Run "sudo nano settings.json"
    * Edit anything you want here, but we want the admin stuff, look for the section called users
    * Uncomment the section, its a block quote, and change the password to whatever you want, you can also add other users here
    * Change the IP address to your pi's address which is located towards the top of the file
17. Admin should be working now
18. Run the server again, "sh ~/etherpad-lite/bin/run.sh"
19. Navigate to "http://YOUR PI ADDRESS:9001/admin/plugins" in your browser once the server is running and log in with the admin credentials you made
20. Here, you can add plugins. Just click the ones you want and install. Don't navigate away from this page until they are done.
21. For video conferencing, use "webrtc"
22. For drawing, use "draw"
23. Once, that is done installing, Restart the etherpad server. Everything should work now, including video chat and draw. If it doesn't work, then you may have to follow the steps below.

Install Draw:

1. Run "sudo apt-get update && sudo apt-get install libcairo2-dev libjpeg8-dev libpango1.0-dev libgif-dev build-essential g++"
2. Run "cd ~/etherpad-lite/"
3. Run "git clone git://github.com/JohnMcLear/draw.git"
4. Run "cd draw"
5. Run "sh bin/run.sh"
6. This is going to take a long time to develop, so be patient, if it doesn't work or your pipe breaks, delete the draw folder and restart from step 3.
7. Run "cd draw"
8. Edit the settings.json file and edit it for the IP address. Put your IP address of the pi there but keep the port there.
9. Run "cd ~/etherpad-lite/" or wherever you installed etherpad
10. Open up the settings.json and add these to the file,

	```
    "ep_draw" {
        "host": "YOUR PI's ADDRESS:9002"
    }
    ```

Source(s):

* http://pcgeeks.bugs3.com/2013/09/how-to-install-etherpad-on-a-raspberry-pi/
* https://learn.adafruit.com/node-embedded-development/installing-node-dot-js
https://github.com/johnmclear/draw