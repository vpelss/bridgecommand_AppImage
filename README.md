# bridgecommand_AppImage
bridgecommand_AppImage how to build

How to create an  AppImage for easy deployment!
Using https://github.com/bridgecommand/bc for demonstration.

See the following for appimage information.
https://docs.appimage.org/
https://www.appimagehub.com/

Making bridgecommand:

sudo apt install git make cmake gcc g++
sudo apt install cmake mesa-common-dev libxxf86vm-dev freeglut3-dev libxext-dev libxcursor-dev portaudio19-dev libsndfile1-dev libopenxr-dev

# get linuxdeploy's AppImage if you don’t have it
# https://github.com/linuxdeploy/linuxdeploy
# https://github.com/linuxdeploy/linuxdeploy/releases/
wget https://github.com/linuxdeploy/linuxdeploy/releases/download/continuous/linuxdeploy-x86_64.AppImage
chmod +x linuxdeploy-x86_64.AppImage

# fetch sources (you could as well use a tarball etc.)
git clone https://github.com/bridgecommand/bc.git

# configure build system for AppImage
# the flags below are the bare minimum that is needed, the app might define additional variables that might have to be set
cmake -Bbc/bin -Sbc/src -DCMAKE_INSTALL_PREFIX=/usr
# build the application on all CPU cores
make -C bc/bin -j$(nproc)

#set up AppImage file system
mkdir -p AppDir/usr/bin

cp -r bc/bin AppDir/usr
#can clean up AppDir/usr/bin first if we like
rm -r AppDir/usr/bin/BridgeCommand.app
rm -r AppDir/usr/bin/CMakeFiles
rm -r AppDir/usr/bin/controller
rm -r AppDir/usr/bin/createDeb
rm -r AppDir/usr/bin/editor
rm -r AppDir/usr/bin/iniEditor
rm -r AppDir/usr/bin/launcher
rm -r AppDir/usr/bin/libs
rm -r AppDir/usr/bin/multiplayerHub
rm -r AppDir/usr/bin/repeater
rm -r AppDir/usr/bin/*.exe
rm -r AppDir/usr/bin/*.dll

#place AppRun script in AppDir
cp AppRun AppDir
#place bridgecommand.desktop in AppDir
cp bridgecommand.desktop AppDir

#place bridgecommand.png in AppDir
cp bridgecommand.png AppDir

#set permissions
chmod +x AppDir/AppRun
chmod +x AppDir/usr/bin/bridgecommand*
chmod +x AppDir/bridgecommand.desktop

# run linuxdeploy and fix up generate an AppDir (add lib) and build AppImage
ARCH=x86_64 ./linuxdeploy-x86_64.AppImage -v 1 --appdir AppDir --output appimage
chmod 777 Bridge_Command-x86_64.AppImage

#run it
./Bridge_Command-x86_64.AppImage

------------------

How to make AppDir/AppRun :

#!/bin/bash
SELF=$(readlink -f "$0") #will set SELF to path with AppRun appended 
echo ${SELF}
HERE=${SELF%/*} #will set HERE to path without AppRun
echo ${HERE}
EXEC="${HERE}/usr/bin" #set path to bin folder. eg: /tmp/.mount_Bridge6DxuEO/usr/bin
cd ${EXEC}
echo ${EXEC}
ls -l
exec ./bridgecommand #run our app

------------------------

Mount AppImage for examination:
my.AppImage --appimage-mount 
/tmp/mount_myXXXX
# now, use another terminal or file manager to inspect the contents in the directory printed by --appimage-mount

OR

> mkdir mountpoint
> my.AppImage --appimage-offset
> 123456
> sudo mount my.AppImage mountpoint/ -o offset=123456
# you can now inspect the contents
> sudo umount mountpoint/

Analog to mounting AppImages, there is a simple command line switch to extract the contents of type 2 AppImages without external tools. Just call the AppImage with the parameter --appimage-extract. This will cause the runtime to create a new directory called squashfs-root, containing the contents of the AppImage’s AppDir.

------------------------






