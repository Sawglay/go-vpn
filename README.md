# Self-Hosted WireGuard VPN

A personal VPN for a Windows laptop and an iPhone, powered by WireGuard on an Ubuntu cloud server. With the tunnel active, IPv4 internet traffic leaves through the server, so websites see the server's public IP. The project starts with manual device setup and can grow into a full-stack device management dashboard..

**Project status:** Planning. The MVP and dashboard milestones below describe the intended build.

## MVP scope

- Run one WireGuard server on an Ubuntu virtual private server (VPS).
- Connect one Windows laptop and one iPhone using the official WireGuard clients.
- Give each device its own key pair and tunnel IP so its access can be revoked independently.
- Route IPv4 internet traffic and DNS through the tunnel. Verify that IPv6 cannot bypass it before relying on the setup.
- Verify connectivity, reconnection, and device revocation.
