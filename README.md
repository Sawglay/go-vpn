# Self-Hosted WireGuard VPN

A personal VPN for a Windows laptop and an iPhone, powered by WireGuard on an Ubuntu cloud server. With the tunnel active, IPv4 internet traffic leaves through the server, so websites see the server's public IP. The project starts with manual device setup and can grow into a full-stack device management dashboard..

**Project status:** Planning. The MVP and dashboard milestones below describe the intended build.

## MVP scope

- Run one WireGuard server on an Ubuntu virtual private server (VPS).
- Connect one Windows laptop and one iPhone using the official WireGuard clients.
- Give each device its own key pair and tunnel IP so its access can be revoked independently.
- Route IPv4 internet traffic and DNS through the tunnel. Verify that IPv6 cannot bypass it before relying on the setup.
- Verify connectivity, reconnection, and device revocation.

## Architecture

| Component | Stack | Responsibility |
| --- | --- | --- |
| VPN client | Official WireGuard apps for Windows and iOS | Stores the device's private key and starts or stops the tunnel. |
| VPN gateway | Ubuntu VPS and WireGuard | Accepts registered devices and forwards tunneled traffic to the internet. |
| Admin dashboard (planned) | Next.js | Lets the owner view, enroll, and revoke devices. |
| Management API (planned) | FastAPI | Authenticates the owner and applies authorized peer changes to the gateway. |

The device connects directly to the gateway over WireGuard. The planned dashboard and API manage access. Browsing traffic flows through the gateway.

## Set up the MVP

**Prerequisites.** An Ubuntu VPS with a public IPv4 address, SSH access, and the ability to allow inbound UDP on the cloud firewall. Check the provider's monthly price and bandwidth allowance before provisioning.

1. **Install WireGuard on Ubuntu.** Run `sudo apt update` and `sudo apt install wireguard`.
2. **Set up the gateway.** Generate its key pair, choose an unused tunnel subnet such as `10.66.66.0/24`, and configure a WireGuard interface such as `wg0`. Allow the chosen UDP port, for example `51820/udp`, in the cloud and host firewalls. Enable IPv4 forwarding, allow the required forwarded traffic, and configure source NAT on the server's internet-facing interface. Follow the [Ubuntu default gateway guide](https://ubuntu.com/server/docs/how-to/wireguard-vpn/vpn-as-the-default-gateway/) for the routing setup.
3. **Enroll the Windows laptop.** Create a tunnel in the official client. Register the laptop's public key and its individual tunnel address, for example `10.66.66.2/32`, on the server. Set the client's peer to the server's public key and public endpoint. Configure `0.0.0.0/0` as the server peer's IPv4 route on the client and use a DNS resolver reachable through the tunnel.
4. **Start the gateway and connect.** For a saved `/etc/wireguard/wg0.conf` managed by systemd, run `sudo systemctl enable --now wg-quick@wg0`. Activate the Windows tunnel, generate traffic, and run `sudo wg show wg0` on the server to check for a recent handshake and transferred bytes.
5. **Add the iPhone.** Repeat the enrollment with a fresh device key pair and a different tunnel address, such as `10.66.66.3/32`. Test the connection on cellular data.

The IPv4 route in step 3 covers IPv4 only. Handle IPv6 separately on each client by routing it through a configured IPv6 tunnel or blocking direct IPv6 traffic while the VPN is active. Test the chosen behavior on both clients.

## MVP verification

| Check | Expected result |
| --- | --- |
| Handshake and traffic | The server shows a recent handshake and increasing transfer counters after the client generates traffic. |
| Public IPv4 | With the tunnel active, an IPv4 lookup reports the gateway's public IP and websites load. |
| DNS and IPv6 | DNS follows the intended route. IPv6 uses the tunnel or is blocked while the VPN is active. |
| Reconnection and tunnel loss | The iPhone reconnects after switching between Wi-Fi and cellular. With the gateway unreachable, actual traffic behavior matches the configured policy. |
| Device revocation | Removing a peer from the live gateway and its saved configuration cuts off that device. A second enrolled device can still connect. |

If there is no handshake, check the endpoint, UDP firewall rules, and public keys. If there is a handshake but websites fail to load, check IP forwarding, firewall forwarding rules, source NAT, and DNS.

## Keys and privacy

- Keep each private key on its own device or server. Share public keys for enrollment. Keep real configurations containing private keys and exported setup QR codes out of Git and screenshots.
- Keep Ubuntu patched and restrict administrative access to the gateway.
- WireGuard encrypts the device-to-gateway connection. HTTPS protects web content on its way to the destination. Websites can still identify users through their accounts and cookies. [EFF VPN privacy guide](https://ssd.eff.org/module/choosing-vpn-thats-right-you)

