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
