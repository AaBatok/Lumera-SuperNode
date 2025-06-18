# Lumera-SuperNode

### Minimal Spesifikasi
| CPU       | RAM         | Disk         |
|-----------|-------------|--------------|
| `8 vCPU` | `16 GB` | `100+ GB SSD` |
- OS : Ubuntu 22.04
- Buka Port 4444 & 4445
---

### 1. Install SuperNode Binary
- Download SuperNode Binary Versi Terbaru :
```
curl -LO https://github.com/LumeraProtocol/supernode/releases/download/v1.5.0/supernode-linux-amd64
```
- Jadikan File bisa di Eksekusi :
```
chmod +x supernode-linux-amd64
```
- Pindahkan ke Direktori Global :
```
sudo mv supernode-linux-amd64 /usr/local/bin/supernode
```
---
### 2. Initialize SuperNode Configuration
- Bikin Direktori SuperNode :
```
# Create SuperNode home directory
mkdir -p ~/.supernode
sudo chown $USER ~/.supernode -R
```
- Masuk ke Direktori :
```
cd .supernode
```
- Bikin config.yml :
```
nano config.yml
```
- Isi Data ini :
```
supernode:
  key_name: mykey
  identity: "ADRESS_LUMERA"                   # Will be populated after key creation
  ip_address: 0.0.0.0           # Your server's public IP or 0.0.0.0
  port: 4444                    # gRPC/API port

keyring:
  backend: file                 # Options: file|os|test
  dir: keys                     # Directory for key storage

p2p:
  listen_address: 0.0.0.0
  port: 4445                    # DO NOT CHANGE - Required for P2P communication
  data_dir: data/p2p
  bootstrap_nodes: ""
  external_ip: ""               # Leave blank for auto-detection

lumera:
  grpc_addr: "grpc.lumera.io:443"    # Public endpoint (no local node required)
  chain_id: "lumera-testnet-1"

raptorq:
  files_dir: raptorq_files
```
Ganti **ADRESS_LUMERA** dengan Adress Lumera kalian

- Cek File Kebaca atau Tidak :
```
# Check configuration file exists and is readable
cat ~/.supernode/config.yml
# Should display the configuration content
```
- Cek apakah sudah Versi Terbaru v1.5.0 :
```
supernode version
```
![image](https://github.com/user-attachments/assets/4544af40-eca2-4ec7-ab3c-56b893f4ad1e)

---

### 3. SuperNode Key Management
- Import Phrase Wallet yang di pake Validator sebelumnya :
```
supernode keys recover mykey --mnemonic "PHRASE KALIAN" -c ~/.supernode/config.yml
```
Kalau berhasil muncul seperti Gambar ini :

![image](https://github.com/user-attachments/assets/1c7cce44-1cdd-4abc-b0cc-b1453632dfd3)

---

### 4. SuperNode Service Deployment
- Bikin Systemd Service
```
sudo tee /etc/systemd/system/supernode.service <<EOF
[Unit]
Description=Lumera SuperNode
After=network-online.target

[Service]
User=$USER
ExecStart=/usr/local/bin/supernode start -c $HOME/.supernode/config.yml
Restart=on-failure
RestartSec=5
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF
```
- Start SuperNode Service
```
# Reload systemd configuration
sudo systemctl daemon-reload

# Enable and start SuperNode service
sudo systemctl enable --now supernode

# Monitor service logs
journalctl -u supernode -f
```
- Verifikasi :
```
# Check service status
sudo systemctl status supernode
# Should show "active (running)" status

# Check recent logs
journalctl -u supernode --since "5 minutes ago"
# Should show initialization and connection logs
```
**ABAIKAN** bila status restart terus dan exiting code, langsung saja ke registrasi SuperNode

---

### 5. SuperNode Registration
- Kalau ngerjain SuperNode di VPS berbeda dengan yg di pake Validator
- Running command ini di VPS yg di pake Validator

- Registrasi Supernode
```
lumerad tx supernode register-supernode \
  ADRESS_VALIDATOR 10.0.0.x v1.0.11 ADRESS_LUMERA \
  --from wallet \
  --gas 80000  --gas-prices 0.025ulume --chain-id lumera-testnet-1
```
Ganti **ADRESS_VALIDATOR dan ADRESS_LUMERA** sesuai Adress Kalian

Ganti x dengan nomer yang belum kepake di IP [Dashboard SuperNode](https://portal.testnet.lumera.io/lumera-testnet-1/supernodes)

Ganti nama wallet sesuai wallet kalian yang ada di **lumerad keys list**

**Selamat SuperNode kalian sudah Aktif**
