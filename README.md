# Untrusted Setup for WAS-110

## Motivation

Plugging hardware with unknown firmware into your router could be critical attack vector for malicious actors. With that in mind this guide will cover using your Unifi UDM Pro/SE/Max to flash 8311 Community Firmware on an untrusted WAS-110.

## Steps

1. On your computer (Windows/Mac/Linux), download the latest [8311 Community Firmware](https://github.com/djGrrr/8311-was-110-firmware-builder/releases) release from GitHub and extract the files to a directory of your choice.

1. Create a new untrusted VLAN:
    - In the UDM WebUI navigate to `Settings` -> `Networks`
    - Then select `New Virtual Network`
    - Give the network a name
    - Select `Internal` as the zone
    - Uncheck `Auto-Scale Network`
    - Set `192.168.11.2` as the host addess and `24` as the netmask
    - Under advanced settings, choose the options to enable `Isolate Network` and to diable `Allow Internet Access`
    - Leave the DHCP server enabled
    - These settings ensure devices on this untrusted Virtual Network (VLAN) cannot communicate with other devices on your primary network(s)

1. Configure one of the UDM SFP+ ports to use the new untrusted VLAN we created.
    - In the UDM WebUI navigate to `Ports`
    - Select the port you intend to plug the WAS-110 into (if unsure, you can apply this setting to both SFP+ ports)
    - Set `Native VLAN / Network` to the new network you created in the previous step
    - Set `Tagged VLAN Management` to `Block All`
    - This configuration will limit the WAS-110 to the untrusted Virtual Network (VLAN) we created

1. Configure another port or WiFi network to use the same untrusted Virtual Network (VLAN)
    - If you have Unifi WiFi equipment and switches (or other hardware that supports VLANs) you can use WiFi for communicating to the WAS-110 on the untrusted Virtual Network (VLAN), otherwise skip this step
    - To do this, navigate to `Settings` -> `WiFi` in the UDM WebUI
    - Select `Create New`
    - Choose a name and password for the WiFi network
    - For `Network`, choose the untrusted Virtual Network (VLAN) you created in the previous steps
    - Other settings can be left on `Auto` or customized to your liking

1. If you can't or don't want to use the WiFi option, configure another port on the UDM to use the untrusted Virtual Network
    - If using WiFi skip this step
    - The steps to do this are the same as for the SFP+ port you already configured to use untrusted Virtual Network (VLAN)

1. Connect your computer (Windows/Mac/Linux) to the WiFi network or ethernet port that you configured to use the untrusted Virtual Network (VLAN).

1. Now that your PC can communicate with the WAS-110, follow the reamining steps from the PON dot WIKI article [Install the 8311 community firmware on the WAS-110](https://pon.wiki/guides/install-the-8311-community-firmware-on-the-was-110/#network-setup) to flash the Community Firware.

1. After the 8311 Community Firmware is installed on the WAS-110, you can configure its management interface to use an IP address on your internal network and delete the Untrusted Virutal Network (VLAN) and associated WiFi Network we created for the initial setup.
  - In the WAS-110 8311 Community Firmware WebUI navigate to `8133` -> `Configuration` -> `Management`
  - Set the IP address to one on your local network

    Ex. `192.168.1.2`

  - Set the subnet mask as that matches your local network

    Ex. `255.255.255.0`

  - After the WAS-110 reboots, you can now delete the "Untrusted" Virtual Network (VLAN) that was setup for this guide.
  - Also delete the assoicated WiFi network if you created one
  - Finally configure any ports you used back to your primary VLAN, to do this select the port and:
    - Set `Native VLAN / Network` back to your primary Virtual Network (VLAN)
    - Set `Tagged VLAN Management` back to `Allow All`
