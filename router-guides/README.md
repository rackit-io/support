## Introduction

This guide is part of [rackit.io](https://rackit.io/docs) docs, where you'll find all of our setup guides and reference material.

Port forwarding tells your router to send incoming traffic that arrives on a specific port to a specific device on your network. It's the one bit of setup your router won't do for you, and it's required for MIA (My Internet Appliance) to accept VPN connections from the outside world.

The good news: it's a five minute job. This guide walks you through the general process, then gives you click-by-click examples for three of the most popular home routers — the TP-Link Archer BE9700, NETGEAR Nighthawk and eero.

By the end of this guide, TCP/UDP ports 500 and 4500 will be forwarded to your device and you'll know how to verify it worked.

## How Port Forwarding Works

Your router has one public IP address, shared by every device in your home. When traffic arrives from the internet, the router has no idea which device it's meant for — so by default, it drops it. A port forwarding rule is your instruction: "anything arriving on port 500 or 4500 belongs to this device."

MIA runs an IPSec (ikev2) VPN (see the [full tech spec](/features/tech-spec/)), which uses:

- **Port 500 (TCP/UDP)** - IKE, the key exchange that sets up the tunnel
- **Port 4500 (TCP/UDP)** - NAT traversal, which carries the tunnel through your router

These ports are currently static and not configurable. Forward both, and you're done.

## Before You Begin

Make sure you have:

- Admin access to your router (the password is usually printed on a sticker on the router itself)
- Your device plugged in and powered on
- Your device's local IP address (check your router's list of connected devices — look for `mia`)

**One important step first: reserve your device's IP address.** Routers hand out local IPs dynamically, which means your device's address can change after a reboot — silently breaking your forwarding rules. Every router below supports DHCP reservation (sometimes called "address reservation" or "static lease"). Reserve the IP first, then forward to it.

## TP-Link Archer BE9700

TP-Link routers are configured through the web interface at [http://tplinkwifi.net](http://tplinkwifi.net) (or `192.168.0.1`), or through the Tether app. These steps use the web interface.

**Reserve the IP:**

1. Log in and go to **Advanced** > **Network** > **DHCP Server**
2. Under **Address Reservation**, click **Add**
3. Select your MIA device from the client list and click **Save**

**Forward the ports:**

1. Go to **Advanced** > **NAT Forwarding** > **Port Forwarding**
2. Click **Add**
3. Fill in the rule:
   - **Service Name**: `mia-ike`
   - **Device**: select your MIA device (or enter its reserved IP)
   - **External Port**: `500`
   - **Internal Port**: `500`
   - **Protocol**: `All`
4. Click **Save**
5. Repeat steps 2-4 with **Service Name** `mia-nat-t` and port `4500`

Both rules should now show as enabled in the Port Forwarding list.

## NETGEAR Nighthawk

NETGEAR routers are configured through the web interface at [http://routerlogin.net](http://routerlogin.net) (or `192.168.1.1`). The Nighthawk app doesn't support port forwarding, so use a browser for this one.

**Reserve the IP:**

1. Log in and go to **ADVANCED** > **Setup** > **LAN Setup**
2. Under **Address Reservation**, click **Add**
3. Select your MIA device, then click **Apply**

**Forward the ports:**

1. Go to **ADVANCED** > **Advanced Setup** > **Port Forwarding / Port Triggering**
2. Make sure **Port Forwarding** is selected as the service type
3. Click **Add Custom Service**
4. Fill in the rule:
   - **Service Name**: `mia-ike`
   - **Protocol**: `TCP/UDP`
   - **External Port Range**: `500`
   - **Internal Port Range**: `500`
   - **Internal IP Address**: your MIA device's reserved IP
5. Click **Apply**
6. Repeat steps 3-5 with **Service Name** `mia-nat-t` and port `4500`

## eero

eero has no web interface — everything happens in the eero app on your phone. Helpfully, eero requires a DHCP reservation before it will forward a port, so the app walks you through both at once.

1. Open the **eero app**
2. Tap **Settings** > **Network Settings** > **Reservations & Port Forwarding**
3. Tap **Add a Reservation** and select your MIA device
4. Once reserved, tap the device, then tap **Open a Port**
5. Fill in the rule:
   - **Name**: `mia-ike`
   - **External Port**: `500`
   - **Internal Port**: `500`
6. Tap **Save**
7. Repeat steps 4-6 with the name `mia-nat-t` and port `4500`

Note: if you subscribe to eero Plus, make sure your MIA device isn't in a blocked profile, or the forwarded traffic will still be dropped.

## Verify Your Setup

The simplest test: turn off Wi-Fi on your phone (so you're on cellular, outside your network) and connect to your VPN. If the tunnel comes up, your ports are forwarded correctly.

You can also log in to your rackit.io dashboard — once your MIA device can accept connections, it will report as reachable.

## Troubleshooting

**Rules are saved but connections still fail?**
- Your ISP may use CGNAT (carrier-grade NAT), which means you don't have a true public IP. Compare the WAN IP shown in your router against [whatismyip.com](https://www.whatismyip.com) — if they differ, contact your ISP and ask for a public IP.
- Check for double NAT: if your ISP's modem is also a router, you'll need to forward the ports there too, or put the modem in bridge mode.

**Rules worked, then stopped after a reboot?**
- Your device's local IP changed. Set a DHCP reservation (see above) and update the rules to point at the reserved address.

**Can't find your device in the router's client list?**
- Confirm the device is powered on and the ethernet cable is seated
- Look for it under a different name — some routers show the MAC address instead

Still stuck? Email us at [admin@rackit.io](mailto:admin@rackit.io) — helping you configure port forwarding is part of the deal.

Looking for something else? Browse the rest of [our documentation](/docs/).
