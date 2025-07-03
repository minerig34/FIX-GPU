# Works for macOS Sierra and High Sierra (Improved version)
> Taken from [StackExchange](https://apple.stackexchange.com/questions/267581/gpu-problem-boot-hangs-on-grey-screen/295805#295805)

> Thanks to [LangLangC](https://apple.stackexchange.com/users/251859/langlangc)

*For temperature and other improvements* see https://gist.github.com/cdleon/d16e7743e6f056fedbebc329333d79df

## This method leaves brightness control enabled

#### Reset SMC
> Shutdown, unplug everything except power and hold
```shell
leftShift + Ctrl + Option + Power 
```
> I holded for 5 seconds, but holding just for an instant should work

#### Reset NVRAM: Power up and hold
```shell
Command + Option + p + r
```
> Until you hear the startup chime two times.

#### Shutdown computer

#### Power up and boot into Single User Recovery by holding
> if you are on high sierra 10.13.6+ you might need to use `Command + r` instead
```shell
Command + r + s
```

#### Disable SIP (This takes a bit to complete so wait for it)
```shell
csrutil disable
```

#### Disable Discrete GPU on boot by running
```shell
nvram fa4ce28d-b62f-4c99-9cc3-6815686e30f9:gpu-power-prefs=%01%00%00%00
```

#### Enable verbose boot mode (This will show text everytime you boot up, shutdown or restart your mac)
```shell
nvram boot-args="-v"
```

#### Reboot
```shell
reboot
```

#### Boot into Single User-mode by holding
```shell
Command + s
```
> It might look as if it hanged, but press enter and you should see the shell (root#)

#### Mount root partition writeable
```shell
/sbin/mount -uw /
```

#### Make a kext-backup directory
```shell
mkdir -p /System/Library/Extensions-off
```

#### Move ONLY ONE offending kext out of the way
```shell
mv /System/Library/Extensions/AMDRadeonX3000.kext  /System/Library/Extensions-off/
```

#### Inform the system to update its kextcache:
```shell
touch /System/Library/Extensions/
```

#### Reboot
```shell
reboot
```
> It will show a bunch of text in the screen (don't be alarmed, let it finish) and then it will restart again
> In second restart it will show text in the screen again and then it will show normal login screen
> Your computer now should work properly (dGPU off and iGPU on, shown as **i** in [gfxCardStatus](https://gfx.io/)

# BIG CAVEAT FOR APPLE UPDATES
If an update that contains changes to the AMD drivers is about to take place it is advisable to move back the AMDRadeonX3000.kext to its default location before the update process. Otherwise the updater writes at least another kext of a different version to its default location or at worst you end up with an undefined state of partially non-matching drivers.

After any system update the folder /System/Library/Extensions has to be checked for the offending kext. Its presence there will lead to e.g. a boot hang on Yosemite and Sierra, an overheating boot-loop in High Sierra.
