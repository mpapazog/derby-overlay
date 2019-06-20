# derby-overlay

This is an alternative streaming overlay for CRG 3.9.5. It is designed to run as independent as possible from the scoreboard computer, so that the NSOs do not have to do anything out of their regular routine, and you, as a stream operator, have more control over how things are displayed in your broadcast and how to resolve possible issues without affecting the game.

The overlay has been developed and tested with OBS as the streaming application, but it might work with other mixing software that support browser windows, such as vMix, as well. Limitations and notes:
* The overlay is only compatible with the CRG 3.9.5 release. It will not work correctly with the beta branch. Once a new stable release is out, the overlay may be adapted to support it
* Your streamer must use a mixing resolution of 1920x1080 and the overlay must be defined as a browser element with resolution 1920x1080
* It is highly recommended to use separate computers to host the CRG scoreboard application, the webserver that provides this overlay and your streaming application.
* This overlay was developed and tested using the nginx webserver: http://nginx.org/
* Update: The overlay now uses the Roboto font. You can get it from Google: https://fonts.google.com/specimen/Roboto
* If you stumble into bugs, let me know

## What's new:

version 2.3-ish:
* Entrance animations for team and clock panels
* Clock transition animations for intermissions
* Team name change transition animations

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

* Copy the file/folder structure under `/html/` in this repository to `/html/` in the nginx folder

* Edit `overlaySettings.json`:
  * The file uses JSON format, so be careful when editing it. Syntax errors will cause the overlay to fail to load
  * Under `scoreboard>serverIp` and `scoreboard>serverPort` you will need to enter the ip_address:port of your scoreboard computer. Your streamer will use these to pull data from the websocket interface
  * Under `teamShortNames` and `teamColourMappings` you can enter custom scoreboard team name translation rules. This helps you have control of how teams are displayed during a tournament with multiple games. Some examples have been provided in the sample `overlaySettings.json` file
  * To disable sponsor banners, set option `overlaySettings>gameHasTimeoutSponsors` to `false`.

* To change styling elements of the overlay, edit `overlayStyle.css`. Interface colours can be modified by editing the following classes:
`.bgcolour-default`, `.font-colour-default`, `.font-colour-clockface`, `.bgcolour-clockface-gradient1`, `.bgcolour-clockface-gradient2`, `.colour-scheme-popup1` and `.colour-scheme-popup2`

* Start nginx:
  * Open a command line window
  * Navigate to the nginx folder
  * Run the command:
      `nginx`
      or
      `./nginx`

## Troubleshooting:

### The overlay does not load any team names or the period clock
There may be an error in `overlaySettings.json`, such as an incorrect scoreboard IP or a JSON syntax error. Alternatively, you may be missing internet connectivity. The overlay needs to download jQuery from Google to load.

### The overlay loads team names, but clocks are not running
Your CRG scoreboard is most likely exhibiting a websocket deadlock bug that affects version 3.9.5. Steps to recover from this state:
* Open the CRG SBO interface
* Start a new game
* Close all browser and overlay windows pulling data from CRG
* Restart the CRG application
* Reopen and verify if the deadlock is gone
* If the deadlock issue still exhibits itself, repeat these steps, click random stuff in the SBO interface, etc, until it clears. This may take some time, depending on how unlucky you are.

### Nginx won't start
The port number you are trying to use for nginx might be occupied by some other service. You can modify the listen port in `nginx.conf`:

```
listen       8080;
```
