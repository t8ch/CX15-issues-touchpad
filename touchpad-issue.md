# X1C5 touchpad problem
multitouch not working

see discussion in: https://forums.lenovo.com/t5/forums/v3_1/forumtopicpage/board-id/Special_Interest_Linux/page/8/thread-id/9645

I seem to have an ELAN touchpad

## feedback
```
grep -e "Using input driver 'libinput'" /var/log/Xorg.0.log
```

## install (custom) kernels from package
download and unzip 
```
dpkg -i *.deb
```
reboot and check version
```
~# uname -r
4.12.7-custom
```

## what I tried
- kernel github.com/rgba/ubuntu-linux-kernel-4.12.7-custom and got
```
dmesg | grep -E 'psmouse|rmi4|touch' 
[    0.000000] Command line: BOOT_IMAGE=/boot/vmlinuz-4.12.7-custom root=UUID=f3495aa2-a310-4eb0-8dd5-940ef2d70ecb ro quiet splash vt.handoff=7 psmouse.synaptics_intertouch=1
[    0.000000] Kernel command line: BOOT_IMAGE=/boot/vmlinuz-4.12.7-custom root=UUID=f3495aa2-a310-4eb0-8dd5-940ef2d70ecb ro quiet splash vt.handoff=7 psmouse.synaptics_intertouch=1
[    3.386037] psmouse serio1: ****[X1C] Touchpad Firmware Id = LEN0073 PNP0f13 *****
herfurtht@Lenovo:~$ xinput 
⎡ Virtual core pointer                    	id=2	[master pointer  (3)]
⎜   ↳ Virtual core XTEST pointer              	id=4	[slave  pointer  (2)]
⎜   ↳ PS/2 Synaptics TouchPad                 	id=10	[slave  pointer  (2)]
⎣ Virtual core keyboard                   	id=3	[master keyboard (2)]
    ↳ Virtual core XTEST keyboard             	id=5	[slave  keyboard (3)]
    ↳ Power Button                            	id=6	[slave  keyboard (3)]
    ↳ Video Bus                               	id=7	[slave  keyboard (3)]
    ↳ Sleep Button                            	id=8	[slave  keyboard (3)]
    ↳ AT Translated Set 2 keyboard            	id=9	[slave  keyboard (3)]
    ↳ ThinkPad Extra Buttons                  	id=11	[slave  keyboard (3)]
    ↳ Integrated Camera                       	id=12	[slave  keyboard (3)]
```
- boot options
```
i8042.nomux=1 i8042.reset
```
- ```
~$ dmesg | grep -E 'psmouse|touch'
[    3.595796] psmouse serio1: ****[X1C] Touchpad Firmware Id = LEN0073 PNP0f13 *****
```
