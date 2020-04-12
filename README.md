# Install Fedora 31 On Macbook Pro 2013 Notes

I used Windows, Linux, and for last year macOS.
From All of this I still like and want to use linux for some reasons including 
same development and deployment enviroment for my work.

This is my fourth attempt to install linux on this macbook pro 2013 that is my daily driver for work and programming jobs I do.

Installing linux on macbook pro this age is like installing linux on any other sort of laptop computer, but post installation steps are a little too much.

After installing Fedora 31 on this macbook pro I Had some issues related to:
1. WiFi Network Adapter(broadcom bcm 4360)
2. Heating Fan Speed
3. Intel IRIS Graphic Adapter
4. Webcam - I don't Use webcam but maybe later I found a walkaround and will add to this.


Here are some help from over the internet to fix these issues and I just listing the ones that worked for me.

## Fixing WiFi
For wifi I just used this:

https://www.cyberciti.biz/faq/fedora-linux-install-broadcom-wl-sta-wireless-driver-for-bcm43228/

### and as a summary:

* Enable RPM Fusion Repo

```
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```
* Update yum and install updates regarding to kernel
```
sudo yum update
```
* and after installing all updates needed just a simple old reboot
* install kmod-wl
```
sudo dnf install kmod-wl
```
* and finaly another reboot and you will have wifi.

for more info check that link above



## Fan Control and Speed
The problem I have with fan is it won't speed up to cool down CPU and I worried that may way for serious problems later on. For CPU Fan speed control I used 'mbpfan', it has fairly simple and straight forward installation and configuration.

For FanControl I used this

https://github.com/linux-on-mac/mbpfan

### and as summary:
* check that you have 'coretemp' and 'applesmc' kernel modules 
```
lsmod | grep -e applesmc -e coretemp
```
* Check from link above to see if your MBP is compatible
* install mbpfan - for fedora 30+
```
sudo dnf install mbpfan
```
* Make sure it start at boot 
```
sudo systemctl enable mbpfan.service
```
* and my current configuration looks like this in ```/etc/mbpfan.conf```
```
min_fan1_speed = 1600
max_fan1_speed = 6150
low_temp = 54
high_temp = 58
max_temp = 75
polling_interval = 1
```
* and Lastly start mbpfan.service by
```
sudo systemctl start mbpfan.service
```
for more info check that link above


## Intel Iris Graphic Adapter

My problem on this vga adapter was that fedora some how did not use it as a vga and handle all load through CPU it self. That mostly ends in more cpu usage and fan spin up quite any time even web browsing so I got to this config file.
I end up with this.
* create 20-intel-iris.config file in ```/etc/X11/xorg.conf.d/``` directory.

* Added this Config in 20-intel-iris.conf
```
Section "Device"
   Identifier  "Intel Graphics"
   Driver      "intel"
   Option      "SwapBuffersWait" "0"
   Option      "DRI" "3"
EndSection
```
* Reboot

and now cpu usage is normal no to much fan spin up no heating.

## Some Other kernel Modules
I used this 

https://gist.github.com/roadrunner2/1289542a748d9a104e7baec6a92f9cd7

and that helps with boosting and temp.
just follow the part about keyboard and trackpad, in that part, you will build some drivers and use them.
```
sudo modprobe intel_lpss_pci spi_pxa2xx_platform applespi apple-ib-tb
```