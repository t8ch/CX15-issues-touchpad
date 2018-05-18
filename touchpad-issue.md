# X1C5 touchpad problem
multitouch not working

see discussion in: https://forums.lenovo.com/t5/forums/v3_1/forumtopicpage/board-id/Special_Interest_Linux/page/8/thread-id/9645

I seem to have an ELAN touchpad

## what finally worked
@ ubuntu 18.04;  4.15.0-20-generic
- remove comment in /etc/modprobe.d/touchpad.conf and run sudo update-initramfs -u
- now:
```
egrep -i 'synap|alps|etps|elan' /proc/bus/input/devices
N: Name="Synaptics TM3288-003"
N: Name="TPPS/2 Elan TrackPoint"
P: Phys=synaptics-rmi4-pt/serio1/input0
```
- accordingly, synaptics driver needs to be installed
- according to: https://unix.stackexchange.com/questions/428975/lenovo-x1-carbon-gen-6-2018-touchpad-and-trackpoint-issues-with-linux/431820#431820 comment out "i2c_i801" from /etc/modprobe.d/blacklist.conf
- adding "psmouse.synaptics_intertouch=1" to the GRUB_CMDLINE_LINUX_DEFAULT= line in /etc/default/grub
- sudo update-grub
- reload modprobe on startup:

sudo gedit /etc/rc.local and add above the EXIT 0 the following lines:

rmmod psmouse <br>
modprobe psmouse


----- RESOLVED -----

## feedback from system
```
grep -e "Using input driver 'libinput'" /var/log/Xorg.0.log
dmesg > dmesg.log
dmesg | grep -E 'psmouse|touch|rmi'
xinput

```

## install (custom) kernels from package
- download and unzip to /usr/src
- give permissions
```
sudo chmod 775 ubuntu-linux-kernel-4.12.7-custom-master
```
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
- boot options in
```
sudo gedit /etc/default/grub
```
and there
```
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash i8042.nomux=1 i8042.reset"
```
**no removed again**
