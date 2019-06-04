# derby-overlay

This is an alternative streaming overlay for CRG 3.9.5. It is designed to run as independent as possible from the scoreboard computer, so that the NSOs do not have to do anything out of their regular routine, and you, as a streamer, have more control on how things are displayed in your broadcast and how to resolve possible issues.

The overlay has been developed and tested with OBS as the streaming application, but it might work with other mixing software that supports browser windows, such as vMix. Limitations and notes:
* The overlay is only compatible with the CRG 3.9.5 release. It will not work correctly with the beta branch. Once a new stable release is out, the overlay may be adapted to support it
* Your streamer must use a mixing resolution of 1920x1080 and the overlay must be defined as a browser element with resolution 1920x1080
* It is highly recommended to use separate computers to host the CRG scoreboard application, the webserver that provides this overlay and your streaming application.
* This overlay was developed and tested using the nginx webserver: http://nginx.org/
* The overlay has only been tested with the DIN font. If you plan to use a different font, check your font size to avoid text overflow. This font is not included in Windows by default, and needs to be downloaded separately. The font will need to be installed on your streamer computer
* If you stumble into bugs, let me know


## Installation and startup:

Steps to install the overlay on nginx:
* Download nginx
* Edit the file `/conf/nginx.conf` in the nginx folder
  * Find the following section in the file:
  ```
  location / {
      root   html;
      index  index.html index.htm;
  }
  ```

  ... and replace it with the following:

  ```
  location / {
      root   html;
      autoindex on;
  }
  ```

* Copy the files in this repository to `/html/` in the nginx folder
* Edit `overlaySettings.json`:
  * The file uses JSON format, so be careful when editing it. Syntax errors will cause the overlay to fail to load
  * Under `scoreboard>serverIp` and `scoreboard>serverPort` you will need to enter the ip_address:port of your scoreboard computer. Your streamer will use these to pull data from the websocket interface
  * Under `teamShortNames` and `teamColourMappings` you can enter custom scoreboard team name translation rules. This helps you have control of how teams are displayed during a tournament with multiple games. Some examples have been provided in the smaple `overlaySettings.json` file

* To change stylng elements of the overlay, edit `overlayStyle.css`

* Start nginx:
  * Open a command line window
  * Navigate to the nginx folder
  * Run command:
      `nginx`

## Troubleshooting:

### The overlay does not load any team names or the period clock
There is probably an error in `overlaySettings.json`, such as an incorrect scoreboard IP or a JSON syntax error

### The overlay loads team names, but clocks are not running
Your CRG scoreboard is most likely exhibiting a websocket deadlock bug that affects version 3.9.5. Steps to recover from this state:
* Open the CRG SBO interface
* Start a new game
* Close all browser and overlay windows pulling data from CRG
* Restart the CRG application
* Reopen and verify if the deadlock is gone
* If the deadlock issue still exhibits itself, repeat these steps, click random stuff in the SBO interface, etc, until it clears. This may take some time, depending on how unlucky you are.
