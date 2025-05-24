⸻

🧱 Aztec Network Sequencer Node Setup Guide (Testnet)

Run your own Sequencer Node on the Aztec alpha testnet to participate in the network and potentially qualify for future incentives.

⸻

📋 Table of Contents
	1.	System Requirements
	2.	Prerequisites
	3.	Installation
	4.	Running the Node
	5.	Validator Registration
	6.	Discord Role Verification
	7.	Maintenance & Updates
	8.	Useful Commands
	9.	Resources

⸻

🖥️ System Requirements

Minimum:
	•	CPU: 4 cores
	•	RAM: 8 GB
	•	Storage: 100 GB SSD
	•	Network: 25 Mbps up/down

Recommended:
	•	CPU: 8 cores
	•	RAM: 16 GB
	•	Storage: 1 TB SSD

⸻

⚙️ Prerequisites

1. Update System Packages

sudo apt-get update && sudo apt-get upgrade -y

2. Install Dependencies

sudo apt install curl wget git build-essential jq nano -y

3. Install Docker

sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker

4. Install Docker Compose

sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

5. Install Aztec CLI

bash -i <(curl -s https://install.aztec.network)


⸻

🚀 Installation

1. Set Environment Variables

Replace the placeholders with your actual values:

export ETHEREUM_HOSTS="<Your Sepolia RPC URL>"
export L1_CONSENSUS_HOST_URLS="<Your Sepolia Beacon RPC URL>"
export VALIDATOR_PRIVATE_KEY="<Your Ethereum Private Key>"
export COINBASE="<Your Ethereum Wallet Address>"
export P2P_IP=$(curl -s ipv4.icanhazip.com)

Note: You can obtain Sepolia RPC URLs from providers like Alchemy and Beacon RPC URLs from Chainstack.

2. Start the Sequencer Node

aztec start --node --archiver --sequencer \
  --network alpha-testnet \
  --l1-rpc-urls $ETHEREUM_HOSTS \
  --l1-consensus-host-urls $L1_CONSENSUS_HOST_URLS \
  --sequencer.validatorPrivateKey $VALIDATOR_PRIVATE_KEY \
  --sequencer.coinbase $COINBASE \
  --p2p.p2pIp $P2P_IP


⸻

🧾 Running the Node
	•	Check Logs:

docker logs -f $(docker ps -q --filter ancestor=aztecprotocol/aztec)


	•	Verify Synchronization:
After a few minutes, check the latest synced block:

curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getL2Tips","params":[],"id":67}' \
http://localhost:8080 | jq -r ".result.proven.number"



⸻

🛡️ Validator Registration

Note: Validator registration may be limited. If you encounter a quota error, wait for the next available slot.

aztec add-l1-validator \
  --l1-rpc-urls $ETHEREUM_HOSTS \
  --private-key $VALIDATOR_PRIVATE_KEY \
  --attester $COINBASE \
  --proposer-eoa $COINBASE \
  --staking-asset-handler 0xF739D03e98e23A7B65940848aBA8921fF3bAc4b2 \
  --l1-chain-id 11155111


⸻

🤖 Discord Role Verification
	1.	Join the Aztec Discord.
	2.	Navigate to the #operators | start-here channel.
	3.	Run the /operator start command.
You’ll be prompted to provide:
	•	Address: Your Ethereum wallet address.
	•	Block Number: Obtained from the synchronization step above.
	•	Proof: Generate using:

curl -s -X POST -H 'Content-Type: application/json' \
-d '{"jsonrpc":"2.0","method":"node_getArchiveSiblingPath","params":["<BLOCK_NUMBER>","<BLOCK_NUMBER>"],"id":67}' \
http://localhost:8080 | jq -r ".result"


Replace <BLOCK_NUMBER> with the number obtained earlier.

⸻

🔄 Maintenance & Updates

1. Stop the Node

docker stop $(docker ps -q --filter ancestor=aztecprotocol/aztec)

2. Remove Old Data

rm -rf ~/.aztec/alpha-testnet/data/

3. Update Aztec CLI

aztec-up alpha-testnet

4. Restart the Node

Repeat the Start the Sequencer Node step above.

⸻

🛠️ Useful Commands
	•	Check Node Status:

docker ps


	•	View Logs:

docker logs -f $(docker ps -q --filter ancestor=aztecprotocol/aztec)


	•	Stop Node:

docker stop $(docker ps -q --filter ancestor=aztecprotocol/aztec)


	•	Remove Docker Containers:

docker rm $(docker ps -a -q --filter ancestor=aztecprotocol/aztec)



⸻

📚 Resources
	•	Aztec Official Documentation
	•	Aztec GitHub Repository
	•	Aztec Discord Community
	•	Aztec Twitter

⸻

Feel free to customize this README to suit your needs. If you require assistance with setting up a prover node, deploying smart contracts using Noir, or automating this process with scripts, don’t hesitate to ask!
