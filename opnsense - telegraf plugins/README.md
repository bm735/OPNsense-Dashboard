# Overview
These scripts collect custom information about interfaces, gateways and temperature within OPNsense.  Telegraf can then add these details to the standard data it collects within OPNsense which can be sent to any of the standard output sources. In this case we shall send to Influxdb v1.

There are 2 scripts.  telegraf_pfifgw.php is required but telegraf_temperature.sh is optional and because of different hardware may not work and can be removed.

### telegraf_pfifgw.php
This single script collects the following information for Interfaces and gateways.

  **Interfaces:**
  * Interface name
  * IP4 address
  * IP4 subnet
  * IP6 address
  * IP6 subnet
  * MAC address
  * Friendly name
  * Status (Online/Offline/Etc.)

  **Gateways:**
  * Interface name
  * Monitor IP
  * Source IP
  * GW Description
  * Delay
  * Stddev
  * Loss (%)
  * Status (Online/Offline/etc.)

### telegraf_temperature.sh
Collects emperature sensor information.


# Installing
These instructions are based on a clean install of OPNsense 25.1.8.
> [!NOTE]
> If you previously used the pkg install version of telegraf\
> Run `sudo pkg remove telegraf` to remove telegraf.\
> Delete the line that starts with telegraf in /usr/local/etc/sudoers.

## Preperation
> [!NOTE]
> Ansible can be used to automate some of the install and configuration but I have not tested it.  Further details can be found in the /ansible subfolder

## 1. Install OPNsense Telegraf plugin
Navigate to System -> Firmware -> Plugins -> Search for telegraf\
![Screenshot adding the default Telegraf plugin](/images/opnsense1.png)\
Click the plus icon to install

## 2. Configure the standard Telegraf inputs
Navigate to Services -> Telegraf -> Input\
Enable CPU, Total CPU, Memory, System, Network and PF Inputs. (Others can be added if you want to configure other dashboards)\
![Screenshot enabling inputs](/images/opnsense2.png)\
Click Save

\
\
Depending on the version of Influx installed choose either 3a or 3b
## 3a. Configure Telegraph to send to Influxdb v1
Navigate to Services -> Telegraf -> Output\
Enable Influx Output and fill in the following:\
Influx URL: Your InfluxDB URL, this will be the IP address or hostname of your system that is running InfluxDB. E.g http://192.168.1.10:8086\
Database: The name of the database in Infuxdb you want to use. E.g. opnsense\
Username: The username in Influxdb that Telegraf will connect with\
Password: The password for the username in Influxdb that Telegraf will connect with\
![Screenshot for Influx v2 config](/images/opnsense4.png)\
Click Save

## 3b. Configure Telegraph to send to Influxdb v2
Navigate to Services -> Telegraf -> Output\
Enable Influx v2 Output and fill in the following:\
Influx v2 Token: Your InfluxDB Token\
Influx v2 URL: Your InfluxDB URL, this will be the IP address or hostname of your system that is running InfluxDB. E.g http://192.168.1.10:8086\
Influx v2 Organization: Your InfluxDB Organization\
Influx v2 Bucket: Your InfluxDB Bucket\
![Screenshot for Influx v2 config](/images/opnsense3.png)\
Click Save

## 4. Install the Custom Plugins
Use putty or similar to ssh to opnsense and connect with the root or eqivalent user\

 * Create and permission folders
 * Add the php script to the sudoers file
 * Add to the sudoers file to block the php script logging every 10 seconds
 * Download the custom config and scripts
 * Apply permissions to the scripts


Run the following commands from the command line
```
mkdir /usr/local/etc
mkdir /usr/local/etc/telegraf.d
chmod 750 /usr/local/etc/telegraf.d

printf 'telegraf ALL=(root) NOPASSWD: /usr/local/bin/telegraf_pfifgw.php\n' | sudo tee -a /usr/local/etc/sudoers > /dev/null

printf 'Cmnd_Alias PFIFGW = /usr/local/bin/telegraf_pfifgw.php\n' | sudo tee -a /usr/local/etc/sudoers > /dev/null
printf 'Defaults!PFIFGW !log_allowed\n' | sudo tee -a /usr/local/etc/sudoers > /dev/null


curl "https://raw.githubusercontent.com/bm735/OPNsense-Dashboard/refs/heads/master/opnsense%20-%20telegraf%20plugins/custom.conf" -o /usr/local/etc/telegraf.d/custom.conf
curl "https://raw.githubusercontent.com/bm735/OPNsense-Dashboard/refs/heads/master/opnsense%20-%20telegraf%20plugins/telegraf_pfifgw.php" -o /usr/local/bin/telegraf_pfifgw.php
curl "https://raw.githubusercontent.com/bm735/OPNsense-Dashboard/refs/heads/master/opnsense%20-%20telegraf%20plugins/telegraf_temperature.sh" -o /usr/local/bin/telegraf_temperature.sh
chmod 755 /usr/local/bin/telegraf_temperature.sh /usr/local/bin/telegraf_pfifgw.php
```

> [!TIP]
> To test if the Temperature plugin will work\
> Run `sysctl dev.cpu` from the command line\
> If you receive an error similar to `sysctl: cannot stat /proc/sys/dev/cpu: No such file or directory` then it is not supported.
> Edit `/usr/local/etc/telegraf.d/custom.conf` and remove the line `"sh /usr/local/bin/telegraf_temperature.sh"`



