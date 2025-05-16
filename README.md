# 🚀 Drosera Network - Node Installation & Operation Guide

* This guide walks you through installing and running a node for Drosera Network.

⚙️ RPC OF ALCHEMY

* GO TO [ALCHEMY](https://auth.alchemy.com/?redirectUrl=https%3A%2F%2Fdashboard.alchemy.com%2Fsignup%3Fa%3D) AND LOGIN
* TAKE YOUR RPC [HERE](https://dashboard.alchemy.com/chains/eth?network=ETH_HOLESKY)

  *Need login first to take rpc

⚙️ Recommended System Requirements

CPU: 2 Cores

RAM: 4 GB

Storage: 20 GB

📌 Join the official [Discord server](https://discord.gg/ACskWhKV3Q)

⚙️ Remind for local running.

Use this with powershell with admin:
```bash
netsh interface portproxy add v4tov4 listenport=31313 listenaddress=0.0.0.0 connectport=31313 connectaddress=127.0.0.1
netsh interface portproxy add v4tov4 listenport=31314 listenaddress=0.0.0.0 connectport=31314 connectaddress=127.0.0.1
```
Then allow the ports through the Windows firewall.

* Open port 31314 and 31313 in your router wifi (ask chat GPT)

# 1. Install Dependencies (Ubuntu 24.04.2 LTS)

Update system:
```bash
sudo apt update && sudo apt upgrade -y
```
Install required packages:
```bash
sudo apt install -y curl ufw iptables build-essential git wget lz4 jq make gcc nano automake autoconf tmux htop nvme-cli \
libgbm1 pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip
```
Install Docker (skip this step if you are download docker already):
```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove -y $pkg; done

sudo apt install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo docker run hello-world
```
# 2. Environment Setup
Install Drosera CLI:
```bash
curl -L https://app.drosera.io/install | bash
source ~/.bashrc
droseraup
```
Install Foundry:
```bash
curl -L https://foundry.paradigm.xyz | bash
source ~/.bashrc
foundryup
```
Install Bun:
```bash
curl -fsSL https://bun.sh/install | bash
source ~/.bashrc
```
# 3. Deploy Contract & Trap
```bash
mkdir my-drosera-trap && cd my-drosera-trap
```
Git config:
set email
```bash
git config --global user.email "your_email@example.com"
```
set username
```bash
git config --global user.name "your_username"
```
Initialize project:
```bash
forge init -t drosera-network/trap-foundry-template
```
Install & build:
```bash
bun install
forge build
```
* Ignore build warnings.

Deploy Trap:
```bash
DROSERA_PRIVATE_KEY=your_private_key drosera apply
```
* Use a wallet with Holesky ETH. Type ofc when prompted.
# 4. Verify Trap on Dashboard
* Visit: app.drosera.io
* Connect EVM wallet → Check under Traps Owned
![image](https://github.com/user-attachments/assets/9c39eea0-0aaf-417d-8552-765ff33f8a5e)
# 5. Send Bloom Boost to Trap
* Go to your trap → Click Send Bloom Boost
* Send between 0.1 and 2 Holesky ETH
![image](https://github.com/user-attachments/assets/2f5216fd-fdf9-4732-96d0-959b3fbce479)
# 6. Get Sample Block
```bash
drosera dryrun
```
# 🛠️ Operator Setup
# 1. Whitelist Operator
Edit config file:
```bash
cd ~
cd my-drosera-trap
nano drosera.toml
```
Append the following (the example you need to change to your toml file):
```bash
ethereum_rpc = "https://eth-holesky.g.alchemy.com/v2/your-api-key"
drosera_rpc = "https://relay.testnet.drosera.io"
eth_chain_id = 17000
drosera_address = "0xea08f7d533C2b9A62F40D5326214f39a8E3A32F8"

[traps.mytrap]
path = "out/HelloWorldTrap.sol/HelloWorldTrap.json"
response_contract = "0xdA890040Af0533D98B9F5f8FE3537720ABf83B0C"
response_function = "helloworld(string)"
cooldown_period_blocks = 33
min_number_of_operators = 1
max_number_of_operators = 2
block_sample_size = 10
private_trap = true
address = "this is your trap anddress" // can take it in app.drosera.io -> login -> click trap owner its address trap config
whitelist = ["your_EVM_wallet_address"]
```
    

Reapply trap config:
```bash
DROSERA_PRIVATE_KEY=your_private_key drosera apply
```
# 2. Install Drosera Operator CLI
```bash
cd ~
sudo apt update && sudo apt install -y tar

curl -LO https://github.com/drosera-network/releases/releases/download/v1.17.2/drosera-operator-v1.17.2-x86_64-unknown-linux-gnu.tar.gz
tar -xvf drosera-operator-v1.17.2-x86_64-unknown-linux-gnu.tar.gz

sudo cp drosera-operator /usr/bin
drosera-operator --version
```
# 3. Pull Docker Image
```bash
docker pull ghcr.io/drosera-network/drosera-operator:latest
```
# 4. Register Operator
```bash
drosera-operator register --eth-rpc-url https://eth-holesky.g.alchemy.com/v2/your-api-key --eth-private-key your_private_key
```
# 5. Open Firewall Ports
```bash
sudo ufw allow ssh
sudo ufw allow 22/tcp
sudo ufw allow 22/udp
sudo ufw allow 31313/tcp
sudo ufw allow 31313/udp
sudo ufw allow 31314/tcp
sudo ufw allow 31314/udp
sudo ufw enable
sudo ufw reload
```
# 6. Run Node
## Option 1: Docker
```bash
git clone https://github.com/HeavenOG/Drosera-Network && cd Drosera-Network
cp .env.example .env
nano .env
```
Update .env with private key and VPS IP

edit and replace your rpc in:
```bash
nano docker-compose.yaml
```

RUN NODE:
```bash
docker compose up -d
docker logs -f drosera-node
```
Check open ports:
```bash
sudo ss -tuln
```
## Option 2: SystemD Service
Create service:
```bash
sudo nano /etc/systemd/system/drosera.service
```
Paste content:
```bash
sudo tee /etc/systemd/system/drosera.service > /dev/null <<EOF
[Unit]
Description=Drosera Node
After=network-online.target

[Service]
User=$USER
Restart=always
ExecStart=$(which drosera-operator) node --db-file-path $HOME/.drosera.db --network-p2p-port 31313 --server-port 31314 \
    --eth-rpc-url https://eth-holesky.g.alchemy.com/v2/your-api-key \
    --eth-private-key your_private_key \
    --drosera-address 0xea08f7d533C2b9A62F40D5326214f39a8E3A32F8 \
    --listen-address 0.0.0.0 \
    --network-external-p2p-address your_vps_ip \
    --disable-dnr-confirmation true

[Install]
WantedBy=multi-user.target
```
* if you run on local network-external-p2p-address 0.0.0.0

Start service:
```bash
sudo systemctl daemon-reload
sudo systemctl enable drosera
sudo systemctl start drosera
journalctl -u drosera.service -f
```
# 7. Opt-in to Trap
* On the dashboard → Click Opt in to your trap
![image](https://github.com/user-attachments/assets/5189b5cb-cb46-4d10-938a-33f71951dfc2)
# 8. Monitor Node Status
* Green blocks will appear on the dashboard if your node is working properly
![image](https://github.com/user-attachments/assets/9ad08265-0ea4-49f7-85e5-316677245254)

LOG ISSUE:
* if you see this log its mean Drosera rpc is down, don't wory it will be fix by team soon now you need to patient and follow Drosera discord to see annoucements.

![image](https://github.com/user-attachments/assets/c1c687f1-2938-4ac5-bea0-623e901966a0)

* if you see all of red block its mean your port are not working well or your ip you put in drosera.toml is wrong.

![image](https://github.com/user-attachments/assets/520f55db-af50-414e-8b08-e343fe81f19b)


---
## Credits

This guide is based on the original work by [0xmoei](https://github.com/0xmoei/Drosera-Network) who provided the initial Drosera node setup instructions. This version was expanded and formatted for clarity and ease of use.
