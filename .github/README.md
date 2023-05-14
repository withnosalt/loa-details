This is a temporary fork for users who want to run loa-details on linux.

package.json uses a fork of meter-core that removes raw-packet-sniffer: https://github.com/withnosalt/meter-core 

this fork only sets the meter-core package to that repo

Warning: loa-details performs raw packet capture in an electron app. That's the main hurdle to getting it running on non-windows operating systems. 

# Running dev on linux

## Dependencies

install libpcap

install node 18 & npm & probably the typescript stuff that it will tell you is not found

please create an issue documenting your OS & the libraries you needed so I can update this section of the documentation

## Main repo setup

```
git clone --recurse-submodules https://github.com/lost-ark-dev/loa-details
cd loa-details
# replace lost-ark-dev/meter-core with withnosalt/meter-core
sed -i 's/\"meter\-core\"\: \"github\:lost\-ark\-dev\/meter\-core\"/\"meter\-core\"\: \"github\:withnosalt\/meter-core\"/g' package.json  
npm install
# grant electron packet capture permissions
sudo setcap "cap_net_raw+eip" node_modules/electron/dist/electron  
```

## ldconfig setup

Warning, this might mess with imports for other applications. Change `/path/to` to the directory you cloned loa-details into. If you don't know the full path, type `pwd` from your loa-details directory.

```
echo "/path/to/loa-details/node_modules/electron/dist" | sudo tee /etc/ld.so.conf.d/loa-details.conf
sudo ldconfig
```

You can revert this step with `sudo rm /etc/ld.so.conf.d/loa-details.conf || sudo ldconfig` After reverting, loa-details will no longer work, it will fail to find libffmpeg because setcap removes LD_LIBRARY_PATH env variable.

## run loa-details

from your loa-details directory:

```
npm run dev
```

# Common Errors

### Package x not found
```
rm -rf node_modules
rm package-lock.json
npm install
```
then run setcap again

### Can not open socket
setcap needs to be run again

### libffmpeg not found
perform ldconfig setup

# Misc 

### Port Mirroring

port mirroring works fine with loa-details out of the box

### ARM

If you have an rpi with a window manager the above linux dev instructions will probably work with raspbian.   An issue is welcome documenting exact steps if you get it to work or with questions if you run into issues.

Note, if you run a headless rpi it won't work due to electron.

### MacOS / OSX

OSX uses bsd's permission model. There is no setcap. That leaves running with sudo or similar approaches. Electron+chromium aren't meant to run with sudo.

There were forks of loa-details that (1) ran as a webserver and (2) had packet capture as a separate process. None are maintained currently, but either one of those changes would allow it to work on OSX.

### Build

If anyone wants to write a build guide that'd be great. I did not attempt to get a built app running, although npm run build runs successfully. Building would remove the need for the ldconfig step and be preferable. 
