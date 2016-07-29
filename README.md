# Nascent AirPlay Example

Documentation and setup scripts necessary for turning your Nascent Objects speaker device into an AirPlay-enabled speaker.


# Compatible Devices

This process should work with any Nascent Objects product that contains a speaker.  Some examples of supported products include:
 - [Cielo: An Alexa Speaker by John Whaley](http://shopnascent.myshopify.com/products/cielo-by-john-whaley)
 - [Red: A Bluetooth Speaker by Alexandre Jais](http://www.nascentobjects.com/red)
 - [Bloque: An Alexa Speaker by Robert She](http://shopnascent.myshopify.com/products/chalk-by-robert-she)
 - [Boo: An Alexa Speaker by Aline Pittner](http://shopnascent.myshopify.com/products/boo)
 - [Cubito: An Alexa Speaker by Minji Kim](http://shopnascent.myshopify.com/products/cubito)
 - [Duet: An Alexa Speaker by Shaun Kosoy](http://shopnascent.myshopify.com/products/duet)
 - [ioPlay: An Alexa Speaker for Lego Technic](http://shopnascent.myshopify.com/products/ioplay-by-antonio-borja)


# Connect to Your Device

Insert your main module and speaker module into your shape and plug it in.

Wait 2 minutes for it to boot.

Once booted, press the button on the back of your main module to enable wifi setup.  Once pressed, the button's light should blink on and off.

![Connect Image](/path/to/img.jpg)

The main module is now broadcasting as a wireless access point.  Connect your computer or mobile device to:

<table>
<tr><td>SSID:</td><td>NascentDevice</td></tr>
<tr><td>Password:</td><td>MadeFresh</td></tr>
</table>

Once successfully connected to the access point, open a web browser and navigate to:

http://192.168.0.1

Follow the instructions in your web browser to name your device and connect it to your home wireless network.

Once connected to your wireless network, connect your computer or mobile phone back to your home wireless network.

You should now be able to ssh into the device with the following credentials:

<table>
<tr><td>Host Name:</td><td><DEVICE_NAME>.local</td></tr>
<tr><td>Username:</td><td>root</td></tr>
<tr><td>Password:</td><td>prototype</td></tr>
</table>


# Installing Dependencies

You will be setting up AirPlay support using [shairport-synchttps://github.com/mikebrady/shairport-sync](https://github.com/mikebrady/shairport-sync).  To do that, you must first install a few dependencies:

## Avahi

opkg update
systemctl stop mdns
opkg install avahi
systemctl enable avahi-daemon
systemctl start avahi-daemon
systemctl disable mdns
opkg install avahi-dev

## libconfig
opkg install coreutils
wget --no-check-certificate http://www.hyperrealm.com/libconfig/libconfig-1.5.tar.gz
cd libconfig-1.5
./configure --prefix=/usr
make
make install
cd ..

## Misc Dependencies
opkg install libdaemon-dev
opkg install libpopt-dev
opkg install alsa-lib-dev

# Compiling Shairport-Sync
git clone https://github.com/mikebrady/shairport-sync

getent group shairport-sync &>/dev/null || groupadd -r shairport-sync >/dev/null
getent passwd shairport-sync &> /dev/null || useradd -r -M -g shairport-sync -s /usr/bin/nologin -G audio shairport-sync >/dev/null

cd shairport-sync
autoreconf -i -f
./configure --with-alsa --with-avahi --with-ssl=openssl --with-systemd
make
make install
cd ..

# Configure Shairport-Sync
You now need to configure shairport sync to use your speaker module.  You can manually configure /etc/shairport-sync.conf to whatever settings you like, but you can also use the included script to generate a config file that works with your nascent device.

git clone https://github.com/nascentobjects/AirPlayExample
cd AirPlayExample
bash ./generate_shairport_conf.sh
cd ./shairport-sync.conf /etc/

# Start and Enable Shairport-Sync Service
systemctl enable shairport-sync
systemctl start shairport-sync




