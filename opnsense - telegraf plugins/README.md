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

## Preperation
> [!TIP]
> If you previously used the pkg install version of telegraf\
> Run `sudo pkg remove telegraf` to remove telegraf.\
> Delete the line that starts with telegraf in /usr/local/etc/sudoers.

## 1. Install and configure the standard OPNsense Telegraf plugin
navigate to System -> Firmware -> Plugins -> Search for telegraf, and click the plus icon to install.





```

```
