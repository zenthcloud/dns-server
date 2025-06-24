# Zenth DNS Server

**Zenth DNS Server** is a high-performance, open-source domain name system (DNS) server built for the Zenth Cloud ecosystem by Sky Genesis Enterprise. It provides authoritative DNS services, dynamic updates, and full integration with Zenth network and identity components.

## 🌐 Features

- ✅ Authoritative DNS (IPv4/IPv6)
- ✅ Dynamic DNS updates (DDNS)
- ✅ Zone-based access control
- ✅ Integration with `dhcp-server` for automatic record updates
- ✅ Management via `api-server` and `panel-server`
- ✅ Built-in metrics, logs, and health checks
- ✅ DNSSEC-ready and DoT/DoH optional support
- ✅ Container-ready and minimal footprint

## 📦 Part of the Zenth Cloud Stack

Zenth DNS Server coordinates name resolution and zone management for all Zenth services:

- `dhcp-server` – Updates DNS records dynamically from DHCP leases
- `ldap-server` – Optionally resolve identity-based hostnames
- `vpn-server` / `firewall-server` – Name resolution for routing and policies
- `panel-server` – Admin interface for managing DNS zones and records
- `status-server` – Monitoring and visibility into DNS health
- `api-server` – Full programmatic control of records and zones

## 🛠️ Technology

- Based on a modern fork of **CoreDNS** or **Knot DNS**
- Written in Go for extensibility and performance
- JSON/YAML configuration support
- Support for DNS-over-HTTPS (DoH) and DNS-over-TLS (DoT)
- Logging, metrics, and plugin support

## 📖 Documentation

Setup guides, zone configuration, API examples and integration docs can be found in `/docs` or on [Documentations](https://docs.zenthcloud.com).

## 🔐 Security

- DNSSEC support for zone signing
- Configurable access control and ACLs
- Secure updates via TSIG or Zenth Auth tokens

## 🛡️ License

Zenth DNS Server is released under the **GNU Affero General Public License v3 (AGPLv3)**. See `LICENSE` for licensing terms.

---

For contributing, reporting issues, or requesting features, visit [github.com/zenthcloud](https://github.com/zenthcloud).
