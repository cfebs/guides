## Virtualbox dev environment w/ static IP

I can never find a great guide on this stuff, so these are more like notes to myself.

The goal here is to have a virtualbox running more or less headless. An ssh client is used separately to actually use the machine. I've found it to be fantastic for a quick dev setup.

## _lets go_

Assumes you have a vbox setup with Ubuntu server (this will work for 16.04) ready to go.

```
sudo apt-get install openssh-server
```

In the virtualbox settings:

![image](https://cloud.githubusercontent.com/assets/302375/15796985/4f4affd2-29d7-11e6-956c-c2dae71a6cc2.png)

Add a host only adapter to your machine. Virtualbox should come with one that is setup already.

You can find the settings for the Host Only adapters here:

![image](https://cloud.githubusercontent.com/assets/302375/15796993/7882053a-29d7-11e6-8c7c-4b8ef06a9aa4.png)

What has worked for me is picking an IP in the range defined here.

For example `192.168.56.101`

Ensure you reboot the virtualbox

```
ifconfig -a
```

Look for the new device name here, mine was `enp0s8`, it will be used in the following examples.

The interface is not configured yet and is not up on boot - lets do that.

```
sudo vim /etc/network/interfaces
```

A good step to just test out the host only device would be setting up the interface with DHCP.
You can do that with these lines (but skip if you want to straight to staticip)

```
auto enp0s8
iface enp0s8 inet dhcp
```

> If you then save file, reboot and run `ifconfig`  again, you should see your device with an IP. Try to ssh to it!

Now on to static (remove or comment the dhcp entry if you added it). Edit `/etc/network/interfaces` top add:


```
auto enp0s8
iface enp0s8 inet static
address 192.168.56.101
netmask 255.255.255.0
```

Save and reboot, run `ifconfig` again, your interface should be up!

Now try to ssh to it.

### Windows

**ssh clients**
- putty tray: https://github.com/FauxFaux/PuTTYTray/releases
- chrome secure shell: https://goo.gl/XWXWrI

quickly to add this to your hosts file (open `cmd` as **Administrator**)

```
echo(>>%SystemRoot%\System32\drivers\etc\hosts&echo 192.168.56.101 devbox >> %SystemRoot%\System32\drivers\etc\hosts
```
