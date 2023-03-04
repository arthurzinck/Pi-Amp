# Pi-Amp Project 

## the short version of the Documentation of IQAudio

### Update the PI 

``` 
rpi-update
apt-get update
apt-get dist-upgrade
```

### Load the Driver IQQudio


Edit the boot comfig : `/boot/config.txt`
And add : 
- Default options : 
    ```
    dtoverlay=iqaudio-dacplus
    ```
- This will give a "one-shot" unmute when kernel module loads
    ```
    dtoverlay=iqaudio-dacplus,unmute_amp
    ```
- This will unmute amp when ALSA device opened by a client. Mute, with 5 second delay
when ALSA device closed. (Re-opening the device within the 5 second close
window, will cancel mute.)
    ```
    dtoverlay=iqaudio-dacplus,auto_mute_amp
    ```
### Remove the Standard Rpi audio card

To remove the standard Raspberry PI audio card, comment out the {dtparam=audio=on} device
tree parameter in your `/boot/config.txt` and reboot.

Edit the file `/boot/config.txt`
```
# Enable audio (loads snd_bcm2835)
#dtparam=audio=on
dtoverlay=vc4-kms-v3d,noaudio
```

Don't forget to : 
```
sync
sudo reboot
```

Once restarted SSH into the Pi and check that the audio drivers / card is available to ALSA.
```
aplay -l
```

If the IQaudIO board and drivers have been installed correctly you should see something similar to:

```
aplay -l
**** List of PLAYBACK Hardware Devices ****
card 0: IQaudIODAC [IQaudIODAC], device 0: IQaudIO DAC HiFi pcm512x-hifi-0 []
Subdevices: 0/1
Subdevice #0: subdevice #0
```

## Raspotify

### update and upgrade

```
apt update && apt upgrade
```
### Install necessary package

```
apt install -y apt-transport-https curl
```

### Install the Raspotify Repo 

```
curl -sSL https://dtcooper.github.io/raspotify/key.asc | sudo tee /usr/share/keyrings/raspotify-archive-keyrings.asc >/dev/null
echo 'deb [signed-by=/usr/share/keyrings/raspotify-archive-keyrings.asc] https://dtcooper.github.io/raspotify raspotify main' | sudo tee /etc/apt/sources.list.d/raspotify.list
```

### Install Raspotify
```
apt update
apt install raspotify
```

### Configure Raspotify

```
# /etc/raspotify/conf -- Arguments/configuration for librespot

# A non-exhaustive list of librespot options and flags.

# Please see https://github.com/dtcooper/raspotify/wiki &
# https://github.com/librespot-org/librespot/wiki/Options
# for configuration details and a full list of options and flags.

# You can also find a full list with `librespot -h`.

# To avoid name collisions environment variables must be prepended with
# `LIBRESPOT_`, so option/flag `foo-bar` becomes `LIBRESPOT_FOO_BAR`.

# Invalid environment variables will be ignored.

# Raspotify defaults may vary from librespot defaults.
# Commenting out the environment variable will fallback to librespot's default
# unless otherwise noted.

# Flags are either on (uncommented) or off (commented),
# their values are otherwise not evaluated (but the "=" is still needed).

# Only log warning and error messages.
LIBRESPOT_QUIET=

# Automatically play similar songs when your music ends.
LIBRESPOT_AUTOPLAY=

# Disable caching of the audio data.
# Enabling audio data caching can take up a lot of space
# if you don't limit the cache size with LIBRESPOT_CACHE_SIZE_LIMIT.
# It can also wear out your Micro SD card. You have been warned. 
LIBRESPOT_DISABLE_AUDIO_CACHE=

# Disable caching of credentials.
# Caching of credentials is not necessary so long as
# LIBRESPOT_DISABLE_DISCOVERY is not set.
LIBRESPOT_DISABLE_CREDENTIAL_CACHE=

# Play all tracks at approximately the same apparent volume.
LIBRESPOT_ENABLE_VOLUME_NORMALISATION=

# Enable verbose log output.
#LIBRESPOT_VERBOSE=

# Disable zeroconf discovery mode.
#LIBRESPOT_DISABLE_DISCOVERY=

# Options will fallback to their defaults if commented out,
# otherwise they must have a valid value.

# Device name.
# Raspotify defaults to "raspotify (*hostname)".
# Librespot defaults to "Librespot".
#LIBRESPOT_NAME="Raspotify"

# Bitrate (kbps) {96|160|320}. Defaults to 160.
#LIBRESPOT_BITRATE="320"

# Output format {F64|F32|S32|S24|S24_3|S16}. Defaults to S16.
#LIBRESPOT_FORMAT="S16"

# Displayed device type. Defaults to speaker.
#LIBRESPOT_DEVICE_TYPE="speaker"

# Limits the size of the cache for audio files.
# It's possible to use suffixes like K, M or G, e.g. 16G for example.
# Highly advised if audio caching isn't disabled. Otherwise the cache
# size is only limited by disk space.
#LIBRESPOT_CACHE_SIZE_LIMIT=""

# Audio backend to use, alsa or pulseaudio. Defaults to alsa.
#LIBRESPOT_BACKEND="alsa"

# Username used to sign in with.
# Credentials are not required if LIBRESPOT_DISABLE_DISCOVERY is not set.
#LIBRESPOT_USERNAME=""

# Password used to sign in with.
#LIBRESPOT_PASSWORD=""

# Audio device to use, use `librespot --device ?` to list options.
# Defaults to the system's default.
#LIBRESPOT_DEVICE="default"

# Initial volume in % from 0 - 100.
# Defaults to 50 For the alsa mixer: the current volume.
#LIBRESPOT_INITIAL_VOLUME="50"

# Volume control scale type {cubic|fixed|linear|log}.
# Defaults to log.
#LIBRESPOT_VOLUME_CTRL="log"

# Range of the volume control (dB) from 0.0 to 100.0.
# Default for softvol: 60.0.
# For the alsa mixer: what the control supports.
#LIBRESPOT_VOLUME_RANGE="60.0"

# Pregain (dB) applied by volume normalisation from -10.0 to 10.0.
# Defaults to 0.0.
#LIBRESPOT_NORMALISATION_PREGAIN="0.0"

# Threshold (dBFS) at which point the dynamic limiter engages
# to prevent clipping from 0.0 to -10.0.
# Defaults to -2.0.
#LIBRESPOT_NORMALISATION_THRESHOLD="-2.0"

# The port the internal server advertises over zeroconf 1 - 65535.
# Ports <= 1024 may require root privileges.
#LIBRESPOT_ZEROCONF_PORT=""

# HTTP proxy to use when connecting.
#LIBRESPOT_PROXY=""

# ### This is NOT a librespot option or flag. ###
# This modifies the behavior of the Raspotify service.
# If you have issues with this option DO NOT file a bug with librespot.
# 
# By default librespot "download buffers" tracks, meaning that it downloads
# the tracks to disk and plays them from the disk and then deletes them when
# the track is over. This practice is very common, many other audio frameworks
# and players do the exact same thing as a disk based tmp cache is easy to use
# and very resilient. That being said there may be cases where a user may want
# to minimize disk read/writes.
#
# Commenting this out will cause librespot to use a tmpfs so that provided there
# is enough RAM to hold the track nothing is written to disk but instead to a tmpfs.
# See https://github.com/dtcooper/raspotify/discussions/567
# And https://www.kernel.org/doc/html/latest/filesystems/tmpfs.html
TMPDIR=/tmp
```
