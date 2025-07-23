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

## Deploy the project 

The Zenht Cloud DNS Authoritative Server depends on Boost, OpenSSL and Lua, and requires a compiler with C++-2017 support.

On Debian, the following is useful:

```
apt install g++ libboost-all-dev libtool make pkg-config default-libmysqlclient-dev libssl-dev libluajit-5.1-dev python3-venv
```

When building from git, the following packages are also required:

```
apt install autoconf automake ragel bison flex
# For Ubuntu, the following packages should be installed:
apt install libcurl4-openssl-dev luajit lua-yaml-dev libyaml-cpp-dev libtolua-dev lua5.3 autoconf automake ragel bison flex g++ libboost-all-dev libtool make pkg-config libssl-dev lua-yaml-dev libyaml-cpp-dev libluajit-5.1-dev libcurl4 gawk libsqlite3-dev python3-venv
# For DNSSEC ed25519 (algorithm 15) support with --with-libsodium
apt install libsodium-dev
# If using the gmysql (Generic MySQL) backend
apt install default-libmysqlclient-dev
# If using the gpgsql (Generic PostgreSQL) backend
apt install libpq-dev
# If using --enable-systemd (will create the service scripts so it can be managed with systemctl/service)
apt install libsystemd0 libsystemd-dev
# If using the geoip backend
apt install libmaxminddb-dev libmaxminddb0 libgeoip1 libgeoip-dev
```

Then generate the configure file:

autoreconf -vi
To compile a very clean version, use:
```
./configure --with-modules="" --disable-lua-records
make
```

# make install
This generates a Zenth Cloud DNS Authoritative Server binary with no modules built in.

See https://wiki.zenthcloud.com/dns/authoritative/backends/index.html for a list of available modules.

When ./configure is run without --with-modules, the bind and gmysql module are built-in by default and the pipe-backend is compiled for runtime loading.

To add multiple modules, try:
```
./configure --with-modules="bind gmysql gpgsql"
```
Note that you will need the development headers for PostgreSQL as well in this case.

See https://wiki.zenthcloud.com/dns/authoritative/appendices/compiling.html for more details.

If you run into C++11-related symbol trouble, please try passing CPPFLAGS=-D_GLIBCXX_USE_CXX11_ABI=0 (or 1) to ./configure to make sure you are compatible with the installed dependencies.

Compiling the Recursor
See README.md in pdns/recursordist/.

Compiling dnsdist
See README-dnsdist.md in pdns/.

Building the HTML documentation
The HTML documentation (as seen on the PowerDNS docs site) is built from ReStructured Text (rst) files located in docs. They are compiled into HTML files using Sphinx, a documentation generator tool which is built in Python.

Install the dependencies under "COMPILING", and run autoreconf if you haven't already:

```
autoreconf -vi
```
Enter the docs folder, and use make to build the HTML docs.

```
cd docs
make html-docs
```

The HTML documentation will now be available in html-docs.

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
