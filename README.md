# Aztec-Sequencer-Node
Aztec Sequencer Node Guideline 
A Simple and Comprehensive Guide to Aztec Sequencer Node Setup
   This will guide you on how to set your node and earn an Apprentice Role in the Aztec Discord server.

   Hardware needed to be a sequencer?
25 Mbps up/down bandwidth - 8 Core CPU - 16 GB RAM - 1 TB NVMe SSD (Typical consumer desktop or laptop will work.)

 Install Prerequisites:
shell

sudo apt update && sudo apt upgrade -y && \
sudo apt autoremove && \
screen -S Aztec-Sequencer

YOU CAN SKIP INSTALL DOCKER, IF YOU ALREADY INSTALLED

➡️
Install Docker
shell

sudo apt install apt-transport-https ca-certificates curl software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg && \
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt update -y && sudo apt upgrade -y && \
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin && \
sudo systemctl status docker

Install Aztec Sandbox:
shell

bash -i <(curl -s https://install.aztec.network)

Update Aztec Tool
aztec-up alpha-testnet or /root/.aztec/bin/aztec-up alpha-testnet

Setting Node Config:
➡️
Create Aztec Sequencer Folder
shell

mkdir aztec-sequencer && cd sequencer

Create .env file
shell

echo -e "VALIDATOR_PRIVATE_KEY=EDIT-WITH-ETH-PRIVATE-KEY\nP2P_IP=EDIT-WITH-YOUR-VPS-IP" > .env

edit EDIT-WITH-ETH-PRIVATE-KEY and EDIT-WITH-YOUR-VPS-IP before next step


Create docker-compose.yml file
cat <<EOF > docker-compose.yml
version: '3.8'

services:
  node:
    image: aztecprotocol/aztec:0.85.0-alpha-testnet.5
    container_name: aztec-sequencer
    environment:
      ETHEREUM_HOSTS: "https://ethereum-sepolia-rpc.publicnode.com" # Replace with your EL endpoint
      L1_CONSENSUS_HOST_URLS: "https://ethereum-sepolia-beacon-api.publicnode.com" # Replace with your CL endpoint
      DATA_DIRECTORY: /data
      VALIDATOR_PRIVATE_KEY: \${VALIDATOR_PRIVATE_KEY} # Load from .env or pass manually
      P2P_IP: \${P2P_IP} # Load from .env or pass manually
      LOG_LEVEL: debug
    entrypoint: >
      sh -c 'node --no-warnings /usr/src/yarn-project/aztec/dest/bin/index.js start --network alpha-testnet --node --archiver --sequencer'
    network_mode: host
    volumes:
      - /home/my-node/node:/data
    restart: unless-stopped
EOF
🟢
 You can custom RPC with this example
    ports:
      - 50500:40400/tcp
      - 50500:40400/udp
      - 9090:8080
Source: https://docs.aztec.network/next/the_aztec_network/guides/run_nodes/how_to_run_sequencer#using-a-docker-compose

Running your Aztec Sequencer Node:
shell

cd $HOME/aztec-sequencer && docker compose up -d

  wait for a log like this 

  ![image](https://github.com/user-attachments/assets/fe703946-9459-4d55-addb-014528a5ee21)

  Wait a few minutes for the sync to complete.
First, you need to detach from the screen before proceeding to the next step, Press Ctrl + A, Then Click D
to return to the log screen use :
screen -r aztec

et Your Block Number:
Once you're outside the session, run the following command. Replace <YOUR_IP_ADDRESS> with your VPS IP:
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://<YOUR_IP_ADDRESS>:8080 | jq -r ".result.proven.number"

Run this command. Replace <YOUR_IP_ADDRESS> with your VPS IP, and <BLOCK_NUMBER> with the block number you just got:
curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params":["<BLOCK_NUMBER>","<BLOCK_NUMBER>"],"id":67}' \
http://<YOUR_IP_ADDRESS>:8080 | jq -r ".result"
This will show your proof — copy and save it too.

Get the Role:
Join the Aztec Discord server, Join [HERE](https://discord.gg/aztec)
Run the command /operator start in the #operator channel.
Fill in your wallet address, block number, and proof.

Contribute to the project











