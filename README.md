
<br>
<center><img src="images/xfce/look_1/desktop.png"></center>
<br>
<p align="center"><b>Easily Install Termux Gui Desktop </b></p>
<p align="center"><b>With Some Popular Gui Apps Directly In Termux</b></p>

<div align="center">

https://img.shields.io/github/issues/sabamdarif/termux-desktop)

</div>




## :warning: Index Links :point_down:

### 1. Check Basic Requirment: [from here](#requirements)
### 2. Check All Avilable Desktop Styles: [XFCE](xfce_styles.md) , [LXQT](lxqt_styles.md)
### 3. Check About Hardware Acceleration : [from here](hw-acceleration.md)
### 4. Check About Distro Container: [from here](proot-caontainer.md)
### 5. Check Natively Supported Apps list: [from here](applist.md)
### 6. Installation: [from here](#installation)
### 7. Uses: [from here](#uses)
### 8. See More: [from here](see-more.md)

<a name="requirements"></a>

## Minimum Requirements:
- No Root Required
- Android 7+ phone
- [Termux](https://termux.dev/en/) From [Github](https://github.com/termux/termux-app/releases) Or [Fdroid](https://f-droid.org/en/packages/com.termux/)
> Termux from Google Play is unmaintained due to API requirements So sould use the F-Droid one instead.
- 2GB of RAM 3GB of RAM
- 1.5 - 2 GB Of Internet
- 3 - 4 GB Of Free Storage
- VNC Client [RealVnc](https://play.google.com/store/apps/details?id=com.realvnc.viewer.android) Or [Nethunter Kex](https://store.nethunter.com/en/packages/com.offsec.nethunter.kex/)
- [Termux:X11](https://github.com/termux/termux-x11/releases)

<br>

## Default Look:

<center><img src="images/xfce/look_1/look.png"></center>
<br>

# See Other Styles: [Here](xfce_styles.md)

## Screenshots:
> All gui apps screenshot

### Browsers:

<center><img src="images/apps/firefox-chromium.png"></center>

### Image Editors:

<center><img src="images/apps/inkscape-gimp.png"></center>

### Code Editors:

<center><img src="images/apps/geany-vscode.png"></center>

### Media Players:

<center><img src="images/apps/parole-vlc.png"></center>

### Wine:

<center><img src="images/apps/wine.png"></center>

## See More Natively Supported Apps: [Here](applist.md)

## Want To Install More Apps (Like: Libreoffice) Which Are Not Avilable In Termux:

## Libre Office:

<center><img src="images/apps/container-libreoffice-2.png"></center>

## See How To Use Distro Container: [Click Here](proot-caontainer.md)

## Hardware Acceleration In Distro Container And Also In Termux:

<center><img src="images/pdrun-glmark2.png"></center>


## Know More About Hardware Acceleration: [Here](hw-acceleration.md)

<br>
<br>

<a name="installation"></a>

# Installation:

>NOTE: This only works on Termux

>NOTE: A Fresh Install Is Always Recommended / Best 

## Steps:

```
curl -L https://raw.githubusercontent.com/sabamdarif/termux-desktop/main/setup-termux-desktop -o setup-termux-desktop
```
```
chmod +x setup-termux-desktop
```
```
./setup-termux-desktop
```
## One Line Install:

```
curl -sL https://raw.githubusercontent.com/sabamdarif/termux-desktop/main/setup-termux-desktop | bash
```

<a name="uses"></a>

## Uses:

- **Type `tx11start` to star using Termux:11.**
- **Type `tx11stop` to stop Termux:11 server**
- **Type `vncstart` to start using Termux:11.**
- **Type `vncstop` to stop Termux:11 server**
- **Type `vncstop -f` to kill vncserver**

### If you select only one of them to access gui
- **Type `gui --start / gui -l` to start gui**
- **Type `gui --stop / gui -s` to stop gui**

### If you select both for gui access
- **Type `gui --start tx11/vnc or gui -l tx11/vnc` to start Termux:X11 or VNC**
- **Type `gui --stop tx11/vnc or gui -s tx11/vnc` to stop Termux:X11 or VNC**

- **Type `gui -kill` to kill both vncserver and Termux:x11 At Once**
- **Type `./setup-termux-desktop --change style` To Change Desktop Style**
- **Type `./setup-termux-desktop -r` To Remove Termux Desktop**

## If you like my work then don't forget to give a Star :blush:
