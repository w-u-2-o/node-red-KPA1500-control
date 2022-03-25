# node-red-KPA1500-control
 KPA1500 amplifier remote control for node-red on Windows.
 
 SCREENSHOTS

 Screenshots can be found in the Github Wiki entry for this repository.
 
 REVISION 2
 
 Changed from TCP/IP to UDP for communications. This allows for multiple connections, and for the Elecraft KPA1500 Remote Control application to run concurrently should it be necessary.
 
 Added polling for ATU functions to the low speed polling loop.
 
 Increased polling rate from 20 messages per second to 50 messages per second in order to accomodate the additional polling commands and to further smooth gauge behavior. The KPA1500 has accepted this rate over UDP with no difficulty.
 
 Added controls for ATU inline/bypass and ATU tune stop/start. As with the other controls, status is obtained an displayed by polling the amp for its true state, not assuming that state was obtained.
 
 Added a display of the contents of the KPA LCD display. The code checks for OVR (soft fault) and FAULT (hard fault) messages. OVR messages are shown with a yellow (caution) background, FAULT messages are shown with a red (warning) background. This was initially added as a convenient way of displaying additional ATU status, but also proved to be a better way of displaying fault status.
 
 Removed polling for fault codes and the fault code pop-up. The pop-up was blocking the dashboard UI and preventing immediate action to correct a fault situation. 
 
 DESCRIPTION (updated to match Revision 2)
 
 The main flow:
 
 This flow presents the command and control interface. The state of the UI is driven wholly by polling the amp and makes no assumptions based on commands issued. This way the true state of the amp is known.
 
 The following controls are provided: Power: on/off, Mode: operate/standby, ATU inline/bypass, ATU tune start/stop, and network settings. ATU tune start/stop is inhibited when ATU is in bypass mode.
 
 UDP is used for communications. This allows for multiple connections, and for the Elecraft KPA1500 Remote Control application to run concurrently should it be necessary.
 
 There are two polling loops, a high speed (approx. 10Hz/command) loop for things like power and SWR (PWI, PC, PWF, SW and VG commands), and a low speed loop (approx 1.4Hz/command) for things like fan speed, etc. (OS, BN, FS, TM, DS, AM and TP commands). Together, both loops and all commands poll at an aggregate rate of approx. 50 commands/sec.
 
 The amp is incredibly picky about batches of commands coming in and will often respond with garbled messages when presented with long strings of commands. Therefore the commands are metered out in a closed loop fashion with no command going until the answer to the previous command has been received. Low speed commands are also carefully interspersed with high speed commands so that the various gauges and readouts will be as smooth as possible.
 
 The UI cheats a litle when the amp is in standby. Normally input RF power is not reported on the FWD power gauge in standby, but this is handled in the flow.
 
 The contents of the KPA LCD display are shown. The code checks for OVR (soft fault) and FAULT (hard fault) messages. OVR messages are shown with a yellow (caution) background, FAULT messages are shown with a red (warning) background. This was initially added as a convenient way of displaying additional ATU status, but also proved to be a better way of displaying fault status. Hard fault reset occurs by changing the amp back to operate mode just as it does on the front panel.
 
 There is a link-in node to accept commands from other flows. These are also metered into the amp stream. This allows other flows to effect control such as for band switching or station macro operations.
 
 There is a link-out node to send ATU tune-complete (FL) responses to station macro operations. It's worth noting I have used this to create a fully automated tune macro with Thetis SDR software: click the tune macro button and Thetis and the ATU complete a tune operation fully autonomously.
 
  The settings flow:
 
 The settings flow allows for no hardcoding of network parameters. An IP address must be used, an FQDN may fail, and will fail where MAC lookup is concerned. A MAC is required for remote power control, but this flow conveniently will do the arp table lookup for you, just put in the IP and port, click save, and the flow will take it from there.
 
 No checking is done on the settings inputs, so type carefully! But you shouldn't be able to break anything.
 
 Amp network settings are saved in c:\node-red\kpa_settings.json. The directory will be created if it is missing.
 
 Developed under node 16.12.0 and npm 8.1.0, and KPA1500 firmware version 2.58.
 