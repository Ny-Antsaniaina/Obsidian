# create android home sdk

```sh
mkdir ~/Android
mkdir ~/Android/Sdk
```

# download cmd tools

Download `cmdline-tools` on https://developer.android.com/studio#command-line-tools-only
Put in the folder `~/Android/Sdk`

# set up env

Put this on `~/.bashrc`

```sh
export ANDROID_HOME=$HOME/Android/Sdk
export ANDROID_SDK_ROOT=$HOME/Android/Sdk

export PATH=$PATH:$ANDROID_HOME/cmdline-tools/bin
```

Source `.bashrc`

# Install utilities

```sh
sdkmanager --sdk_root=$ANDROID_SDK_ROOT "platform-tools" "emulator" "build-tools;36.0.0"
```


update `~./bashrc`

```sh
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/bin:$ANDROID_HOME/emulator:$ANDROID_HOME/platform-tools
```

Source `.bashrc`

# Download android image

```sh
sdkmanager --sdk_root=$ANDROID_SDK_ROOT "system-images;android-35;google_apis;x86_64"
```

# Create AVD

```sh
avdmanager create avd -n Pixel35 -k "system-images;android-35;google_apis;x86_64"
```

# Update Pixel35 config

edit `~/.android/avd/Pixel35.avd/config.ini`

```sh
hw.keyboard = yes
hw.lcd.density = 120
hw.lcd.vsync = 30
hw.ramSize=8G
image.sysdir.1=system-images/android-35/google_apis/x86_64/
vm.heapSize = 128M
```

# open emulator

```
emulator -avd Pixel35
```

# update logcat size

```sh
adb logcat -G 10M
```

# update logcat timeout

```sh
adb shell settings put system screen_off_timeout 600000
```

connect android to pc 

adb pair  adress ip et port phone 

abd connect a.ip (sans code)


berifier si mon pc est connecté avec mon phone :  ip a | grep inet 

adb devices

npx create-expo-app@latest 

npm run android 


reste project :

npm run reset-project

```
const tab = new Array(20).fill(0).map(()=> new Array(20).fill(0))
```