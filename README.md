# Install en configure Spotifyd on Raspberry Pi 4 Ubuntu 20.0.4.2 LTS

Source : https://spotifyd.github.io/spotifyd/installation/Raspberry-Pi.html

Introduction


Spotifyd
Raspberry Pi install guide
This guide will help you to install spotifyd on a Raspberry Pi 4 with Ubuntu 20.0.4.2 LTS and have it always running.

Download
Download the latest ARMv6 from https://github.com/Spotifyd/spotifyd/releases (use wget)

Gunzip the file: 

gunzip spotifyd-*.gz 

You will now see a .tar file called spotifyd-*.tar.
Unpak the tar file: 

tar xvf spotifyd-*.tar

Copy the file to location /usr/bin/ 

sudo cp spotifyd /usr/bin/

Systemd daemon file
Create a systemd service file 

sudo nano /etc/systemd/user/spotifyd.service
and copy the config to it. 

[Unit]
Description=A spotify playing daemon
Documentation=https://github.com/Spotifyd/spotifyd
Wants=sound.target
After=sound.target
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/usr/bin/spotifyd --no-daemon
Restart=always
RestartSec=12

[Install]
WantedBy=default.target

if you want to run as user instead of root or have some Failed to get D-Bus connection: Connection refused, you define spotifyd.service in your home directory:

mkdir -p ~/.config/systemd/user/
nano ~/.config/systemd/user/spotifyd.service
systemctl --user daemon-reload

Config file

Create your config:

mkdir ~/.config/spotifyd/
nano ~/.config/spotifyd/spotifyd.conf

Input the following data. Change the username, password, device_name, and the bitrate.

[global]
username = "USER" # Spotify e-mail adres name 
password = "PASS"
backend = "alsa"
device = alsa_audio_device # Given by `aplay -L`
mixer = "HDMI" # Given by ' amixer' 
volume-controller = "alsa" # or alsa_linear, or softvol
#onevent = command_run_on_playback_event
device_name = "name_in_spotify_connect"
bitrate = 96|160|320
cache_path = "cache_directory"
volume-normalisation = true
normalisation-pregain = -10
Start the service

systemctl --user start spotifyd.service

Now see if you can find it in the normal Spotify client (Devices in right bottom corner). Retry the above steps if you can't find it.

Starting spotifyd at boot

sudo loginctl enable-linger <username>
systemctl --user enable spotifyd.service
The first command is required to enable your user to run long-running services. Without it systemd would kill the spotifyd process as soon as you log out, and only run it when you log in. Now spotifyd is always running on the Pi, so you can use it as a listening device remotely!

Where to look for in 'aplay -L' 

aplay -L

Example output:
null
    Discard all samples (playback) or generate zero samples (capture)
default
    Playback/recording through the PulseAudio sound server
samplerate
    Rate Converter Plugin Using Samplerate Library
speexrate
    Rate Converter Plugin Using Speex Resampler
jack
    JACK Audio Connection Kit
oss
    Open Sound System
pulse
    PulseAudio Sound Server
upmix
    Plugin for channel upmix (4,6,8)
vdownmix
    Plugin for channel downmix (stereo) with a simple spacialization
hw:CARD=b1,DEV=0
    bcm2835 HDMI 1, bcm2835 HDMI 1
    Direct hardware device without any conversions
plughw:CARD=b1,DEV=0
    bcm2835 HDMI 1, bcm2835 HDMI 1
    Hardware device with all software conversions
sysdefault:CARD=b1
    bcm2835 HDMI 1, bcm2835 HDMI 1
    Default Audio Device
dmix:CARD=b1,DEV=0
    bcm2835 HDMI 1, bcm2835 HDMI 1
    Direct sample mixing device
usbstream:CARD=b1
    bcm2835 HDMI 1
    USB Stream Output
hw:CARD=Headphones,DEV=0
    bcm2835 Headphones, bcm2835 Headphones
    Direct hardware device without any conversions
plughw:CARD=Headphones,DEV=0
    bcm2835 Headphones, bcm2835 Headphones
    Hardware device with all software conversions
sysdefault:CARD=Headphones
    bcm2835 Headphones, bcm2835 Headphones
    Default Audio Device
dmix:CARD=Headphones,DEV=0
    bcm2835 Headphones, bcm2835 Headphones
    Direct sample mixing device
usbstream:CARD=Headphones
    bcm2835 Headphones
    USB Stream Output
    
The device name to chose would be in this case :  hw:CARD=b1,DEV=0
    

Where to look for in 'amixer' 

amixer

(example output) 

Simple mixer control 'HDMI',0
  Capabilities: pvolume pvolume-joined pswitch pswitch-joined
  Playback channels: Mono
  Limits: Playback -10239 - 400
  Mono: Playback 400 [100%] [4.00dB] [on]
  
The mixer would be in this case : HDMI  
  
