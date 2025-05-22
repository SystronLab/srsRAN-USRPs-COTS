# Open5GS Installation Guide

## Step 1: Update and Install MongoDB 8.0

```bash
sudo apt update
sudo apt install gnupg
curl -fsSL https://pgp.mongodb.com/server-8.0.asc | sudo gpg -o /usr/share/keyrings/mongodb-server-8.0.gpg --dearmor

echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-8.0.gpg] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/8.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-8.0.list

sudo apt update
sudo apt install -y mongodb-org
sudo systemctl start mongod
```

## Step 2: Add Open5GS Repository and Install

```bash
sudo add-apt-repository ppa:open5gs/latest
sudo apt update
sudo apt install open5gs
```

## Step 3: Install Node.js (Required for WebUI)

```bash
sudo apt update
sudo apt install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg

NODE_MAJOR=20
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_MAJOR.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list

sudo apt update
sudo apt install nodejs -y
```

## Step 4: Install WebUI

```bash
curl -fsSL https://open5gs.org/open5gs/assets/webui/install | sudo -E bash -
```

## Step 5: Modify Configuration Files

Check the `.yaml` files in `/etc/open5gs/` and make sure they match the ones given in the open5gs folder in this repo.

## Step 6: Stop All Open5GS Services

```bash
sudo systemctl stop open5gs-mmed
sudo systemctl stop open5gs-sgwcd
sudo systemctl stop open5gs-smfd
sudo systemctl stop open5gs-amfd
sudo systemctl stop open5gs-sgwud
sudo systemctl stop open5gs-upfd
sudo systemctl stop open5gs-hssd
sudo systemctl stop open5gs-pcrfd
sudo systemctl stop open5gs-nrfd
sudo systemctl stop open5gs-scpd
sudo systemctl stop open5gs-seppd
sudo systemctl stop open5gs-ausfd
sudo systemctl stop open5gs-udmd
sudo systemctl stop open5gs-pcfd
sudo systemctl stop open5gs-nssfd
sudo systemctl stop open5gs-bsfd
sudo systemctl stop open5gs-udrd
sudo systemctl stop open5gs-webui
```

## Step 7: Restart All Open5GS Services

```bash
sudo systemctl restart open5gs-mmed
sudo systemctl restart open5gs-sgwcd
sudo systemctl restart open5gs-smfd
sudo systemctl restart open5gs-amfd
sudo systemctl restart open5gs-sgwud
sudo systemctl restart open5gs-upfd
sudo systemctl restart open5gs-hssd
sudo systemctl restart open5gs-pcrfd
sudo systemctl restart open5gs-nrfd
sudo systemctl restart open5gs-scpd
sudo systemctl restart open5gs-seppd
sudo systemctl restart open5gs-ausfd
sudo systemctl restart open5gs-udmd
sudo systemctl restart open5gs-pcfd
sudo systemctl restart open5gs-nssfd
sudo systemctl restart open5gs-bsfd
sudo systemctl restart open5gs-udrd
sudo systemctl restart open5gs-webui
```

## Step 8: Check Status of Open5GS Services

```bash
systemctl list-units --type=service | grep open5gs
```

## Step 9: Register Subscriber Information

Access the WebUI at [http://localhost:9999](http://localhost:9999) and log in with the following credentials:

- **Username**: `admin`
- **Password**: `1423`

> Tip: You can change the password in the **Account** menu.

### To Add Subscriber Information:

1. Go to the **Subscriber** menu.
2. Click the **+** button to add a new subscriber.
3. Fill in the required fields:
   - **IMSI**
   - **Security Context** (K, OPc, AMF)
   - **APN** (Should match the name of the APN on the phone)
   - **Type** (Type should be IPv4)
4. Click the **SAVE** button to store the subscriber.

## Step 10: Add Route for UE WAN Connectivity

To allow your UE to access the internet via the PGWU/UPF, enable IP forwarding and configure NAT.

### Enable IPv4/IPv6 Forwarding

```bash
sudo sysctl -w net.ipv4.ip_forward=1
sudo sysctl -w net.ipv6.conf.all.forwarding=1
```

### Add NAT Rules

```bash
sudo iptables -t nat -A POSTROUTING -s 10.45.0.0/16 ! -o ogstun -j MASQUERADE
sudo ip6tables -t nat -A POSTROUTING -s 2001:db8:cafe::/48 ! -o ogstun -j MASQUERADE
```

### Configure Firewall (UFW)

Check the firewall status and disable it if necessary:

```bash
sudo ufw status
sudo ufw disable
sudo ufw status
```

### Optional Security Rules

- Accept packets on the `ogstun` interface:

```bash
sudo iptables -I INPUT -i ogstun -j ACCEPT
```

- Prevent UEs from accessing the host running the UPF:

```bash
sudo iptables -I INPUT -s 10.45.0.0/16 -j DROP
sudo ip6tables -I INPUT -s 2001:db8:cafe::/48 -j DROP
```

- Block UE-originated traffic from reaching other network functions (replace `x.x.x.x/y` accordingly):

```bash
sudo iptables -I FORWARD -s 10.45.0.0/16 -d x.x.x.x/y -j DROP
```
