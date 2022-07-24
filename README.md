# Meduza-stakewars-iii-tutorial

# This is a tutorial on how to set up and run validator for Near shardnet to participate in Stake Wars III

## Useful links

Wallet: https://wallet.shardnet.near.org/

Explorer: https://explorer.shardnet.near.org/ 

## Rent server
#### Server Requirements
Please see the hardware requirement below:

| Hardware       | Chunk-Only Producer  Specifications                                   |
| -------------- | ---------------------------------------------------------------       |
| CPU            | 4-Core CPU with AVX support                                           |
| RAM            | 8GB DDR4                                                              |
| Storage        | 500GB SSD                                                             |

We rent dedicated server from https://www.hetzner.com/

AX41-NVME - running cost 40,80 euro per month,no installation fees at the moment

![image](https://user-images.githubusercontent.com/89131777/180642847-dbc95e4b-c264-45c6-9fbf-6a8d5eedb29f.png)

![image](https://user-images.githubusercontent.com/89131777/180642892-0b15fb54-fa58-4dea-bd02-e6eaf4b09ce2.png)

We use MobaxTerm terminal https://mobaxterm.mobatek.net/ to connect to our server 

![image](https://user-images.githubusercontent.com/89131777/180642995-df368363-21ae-4c13-ab36-a0e11f94ba2e.png)

## Setup enviroment

### First, let's make sure the linux machine is up-to-date.
```bash
sudo apt update && sudo apt upgrade -y
```

### Install developer tools, Node.js, and npm

#### Install developer tools:
```bash
sudo apt install -y git binutils-dev libcurl4-openssl-dev zlib1g-dev libdw-dev libiberty-dev cmake gcc g++ python docker.io protobuf-compiler libssl-dev pkg-config clang llvm cargo
```
#####  Install Python pip:

```bash
sudo apt install python3-pip
```
##### Set the configuration:

```bash
USER_BASE_BIN=$(python3 -m site --user-base)/bin
export PATH="$USER_BASE_BIN:$PATH"
```

##### Install Building env
```bash
sudo apt install clang build-essential make
```

##### Install Rust & Cargo
```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

##### Source the environment
```
source $HOME/.cargo/env
```

#### Install `Node.js` and `npm`:
```bash
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -  
sudo apt install build-essential nodejs
PATH="$PATH"
```

##### Check `Node.js` and `npm` version:
```bash
node -v
```
> v18.x.x
```bash
npm -v
```
> 8.x.x

![image](https://user-images.githubusercontent.com/89131777/180643168-8283bf9a-229d-4008-894f-7b68efbbffcf.png)

##### Install NEAR-CLI

```bash
sudo npm install -g near-cli
```

Now that NEAR-CLI is installed, let's test out the CLI and use the following commands to interact with the blockchain as well as to view validator stats. There are three reports used to monitor validator status:

##### Environment
The environment will need to be set each time a new shell is launched to select the correct network.

Networks:
- GuildNet
- TestNet
- MainNet
- **Shardnet** (this is the network we will use for Stake Wars)

Command:
```bash
export NEAR_ENV=shardnet
```

You can also run this command to set the Near testnet Environment persistent:
```
echo 'export NEAR_ENV=shardnet' >> ~/.bashrc
source ~/.bashrc
```
#### NEAR CLI Commands Guide:

###### Proposals
A proposal by a validator indicates they would like to enter the validator set, in order for a proposal to be accepted it must meet the minimum seat price.

Command:
```
near proposals
```
![image](https://user-images.githubusercontent.com/89131777/180643407-b8fc39c1-3f53-4893-b1bd-6121b8606bd0.png)

###### Validators Current
This shows a list of active validators in the current epoch, the number of blocks produced, number of blocks expected, and online rate. Used to monitor if a validator is having issues.

Command:
```
near validators current
```
![image](https://user-images.githubusercontent.com/89131777/180643435-735b1272-73f1-4e27-b18a-6db42211a95a.png)

###### Validators Next
This shows validators whose proposal was accepted one epoch ago, and that will enter the validator set in the next epoch.

Command:
```
near validators next
```
![image](https://user-images.githubusercontent.com/89131777/180643456-ac89f2e2-783f-4e54-b8bc-211edf9802e8.png)

## Create a wallet and deploy staking pool contract

#### Clone `nearcore` project from GitHub
First, clone the [`nearcore` repository](https://github.com/near/nearcore).

```
git clone https://github.com/near/nearcore
cd nearcore
git fetch
```

Checkout to the commit needed. Please refer to the commit defined in [this file](https://github.com/near/stakewars-iii/blob/main/commit.md). 
```
git checkout <commit>
```

#### Compile `nearcore` binary
In the `nearcore` folder run the following commands:

```
cargo build -p neard --release --features shardnet
```
The binary path is `target/release/neard`. If you are seeing issues, it is possible that cargo command is not found. Compiling `nearcore` binary may take a little while.

Check neard version

```
~/nearcore/target/release/neard --version
```

![image](https://user-images.githubusercontent.com/89131777/180644277-4766b19d-0378-4b26-b640-cfaa002c9a5e.png)

#### Initialize working directory

In order to work properly, the NEAR node requires a working directory and a couple of configuration files. Generate the initial required working directory by running:

```
./target/release/neard --home ~/.near init --chain-id shardnet --download-genesis
```

Now delete old config.json and genesis.json and load new ones

```
sudo apt-get install awscli -y
rm ~/.near/config.json ~/.near/genesis.json
wget -O ~/.near/config.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/config.json
wget -O ~/.near/genesis.json https://s3-us-west-1.amazonaws.com/build.nearprotocol.com/nearcore-deploy/shardnet/genesis.json
```

### Create a wallet on shardnet

Go to https://wallet.shardnet.near.org/ and create a wallet. After create wallet, you will be received 500 Near on shardnet for run a validator node.

### Authorize Wallet Locally

A full access key needs to be installed locally to be able to sign transactions via NEAR-CLI.


* You need to run this command:

```bash
near login
```

> Note: This command launches a web browser allowing for the authorization of a full access key to be copied locally.
1 – Copy the link in your browser

![image](https://user-images.githubusercontent.com/89131777/180643680-feab8e31-5663-4625-90a6-8eed5dd173f4.png)

2 – Grant Access to Near CLI

3 – After Grant, you will see a page like this, go back to console

![img](https://github.com/near/stakewars-iii/blob/main/challenges/images/4.png)

4 – Enter your wallet and press Enter

![Screenshot_2](https://user-images.githubusercontent.com/89131777/180643699-26eb30ab-9ba0-458c-a9c1-b168ae9f229d.png)

### Create validator_key.json

```
cp ~/.near-credentials/shardnet/YOUR_WALLET.json ~/.near/validator_key.json
nano ~/.near/validator_key.json
```
Edit “account_id” => xx.factory.shardnet.near, where xx is your PoolName
Change private_key to secret_key
```
{
“account_id”:”xx.factory.shardnet.near”,
“public_key”:”ed25519:Et3AfNfESrDggakdcncdsjdslcsxcnxxRntjZn1VA”,
“secret_key”:”ed25519:************************************”
}
```

### Setup Systemd

```
sudo tee /etc/systemd/system/neard.service > /dev/null <<EOF 
[Unit] 
Description=NEARd Daemon Service 
[Service] 
Type=simple 
User=$USER 
#Group=near 
WorkingDirectory=$HOME/.near ExecStart=$HOME/nearcore/target/release/neard run 
Restart=on-failure 
RestartSec=30 
KillSignal=SIGINT 
TimeoutStopSec=45 
KillMode=mixed 
[Install] 
WantedBy=multi-user.target 
EOF
```

reload daemon, enable service on system startup and start neard as a service

```
sudo systemctl daemon-reload 
sudo systemctl enable neard 
sudo systemctl start neard 
```

watch logs

```
journalctl -n 100 -f -u neard
```

Make log output in pretty print

Command:

```
sudo apt install ccze
```
View Logs with color

Command:

```
journalctl -n 100 -f -u neard | ccze -A
```

![image](https://user-images.githubusercontent.com/89131777/180644879-9a01dc75-34af-40f7-8cd0-19215cbbc31d.png)


### Deploy staking pool contract

Calls the staking pool factory, creates a new staking pool with the specified name, and deploys it to the indicated accountId.

```bash
near call factory.shardnet.near create_staking_pool '{"staking_pool_id": "<pool id>", "owner_id": "<accountId>", "stake_public_key": "<public key>", "reward_fee_fraction": {"numerator": 5, "denominator": 100}, "code_hash":"DD428g9eqLL8fWUxv8QSpVFzyHi1Qd16P8ephYCTmMSZ"}' --accountId="<accountId>" --amount=30 --gas=300000000000000
```

From the example above, you need to replace:

* **Pool ID**: Staking pool name, the factory automatically adds its name to this parameter, creating {pool_id}.{staking_pool_factory}
Examples:   

- If pool id is meduza will create : `meduza.factory.shardnet.near`

* **Owner ID**: The SHARDNET account (i.e. meduza.shardnet.near) that will manage the staking pool.
* **Public Key**: The public key in your **validator_key.json** file.
* **5**: The fee the pool will charge (e.g. in this case 5 over 100 is 5% of fees).
* **Account Id**: The SHARDNET account deploying and signing the mount tx.  Usually the same as the Owner ID.

> Be sure to have at least 30 NEAR available, it is the minimum required for storage.

#### Transactions Guide
##### Deposit and Stake NEAR

Command:
```bash
near call <staking_pool_id> deposit_and_stake --amount <amount> --accountId <accountId> --gas=300000000000000
```
##### Unstake NEAR
Amount in yoctoNEAR.

Run the following command to unstake:
```bash
near call <staking_pool_id> unstake '{"amount": "<amount yoctoNEAR>"}' --accountId <accountId> --gas=300000000000000
```
To unstake all you can run this one:
```bash
near call <staking_pool_id> unstake_all --accountId <accountId> --gas=300000000000000
```
##### Withdraw

Unstaking takes 2-3 epochs to complete, after that period you can withdraw in YoctoNEAR from pool.

Command:
```bash
near call <staking_pool_id> withdraw '{"amount": "<amount yoctoNEAR>"}' --accountId <accountId> --gas=300000000000000
```
Command to withdraw all:
```bash
near call <staking_pool_id> withdraw_all --accountId <accountId> --gas=300000000000000
```

##### Ping
A ping issues a new proposal and updates the staking balances for your delegators. A ping should be issued each epoch to keep reported rewards current.

Command:
```bash
near call <staking_pool_id> ping '{}' --accountId <accountId> --gas=300000000000000
```
Balances
Total Balance
Command:
```bash
near view <staking_pool_id> get_account_total_balance '{"account_id": "<accountId>"}'
```
##### Staked Balance
Command:
```bash
near view <staking_pool_id> get_account_staked_balance '{"account_id": "<accountId>"}'
```
##### Unstaked Balance
Command:
```bash
near view <staking_pool_id> get_account_unstaked_balance '{"account_id": "<accountId>"}'
```
##### Available for Withdrawal
You can only withdraw funds from a contract if they are unlocked.

Command:
```bash
near view <staking_pool_id> is_account_unstaked_balance_available '{"account_id": "<accountId>"}'
```
##### Pause / Resume Staking
###### Pause
Command:
```bash
near call <staking_pool_id> pause_staking '{}' --accountId <accountId>
```
###### Resume
Command:
```bash
near call <staking_pool_id> resume_staking '{}' --accountId <accountId>
```


#### Becoming a Validator
In order to become a validator and enter the validator set, a minimum set of success criteria must be met.

* The node must be fully synced
* The `validator_key.json` must be in place
* The contract must be initialized with the public_key in `validator_key.json`
* The account_id must be set to the staking pool contract id
* There must be enough delegations to meet the minimum seat price. See the seat price [here](https://explorer.shardnet.near.org/nodes/validators).
* A proposal must be submitted by pinging the contract
* Once a proposal is accepted a validator must wait 2-3 epoch to enter the validator set
* Once in the validator set the validator must produce great than 90% of assigned blocks

Check running status of validator node. If “Validator” is showing up, your pool is selected in the current validators list.

### Monitor and make alerts 

#### Log Files

Systemd Command:
```
journalctl -n 100 -f -u neard | ccze -A
```

![image](https://user-images.githubusercontent.com/89131777/180645350-a2cbbfce-9891-478a-b008-4d651091a190.png)

**Log file sample:**

```
INFO stats: # 1266804 2nEwizdUWMbyUUHwPh1JzMVSNYLYrSBs2HehbjR2Goe9 Validator | 100 validators 30 peers ⬇ 566 kB/s ⬆ 890 kB/s 0.70 bps 2.27 Tgas/s CPU: 58%, Mem: 4.69 GB
```

* **Validator**: A “Validator” will indicate you are an active validator
* **100 validators**: Total 100 validators on the network
* **30 peers**: You current have 30 peers. You need at least 3 peers to reach consensus and start validating
* **#1266804**: block – Look to ensure blocks are moving

#### RPC
Any node within the network offers RPC services on port 3030 as long as the port is open in the nodes firewall. The NEAR-CLI uses RPC calls behind the scenes. Common uses for RPC are to check on validator stats, node version and to see delegator stake, although it can be used to interact with the blockchain, accounts and contracts overall.

Find many commands and how to use them in more detail here:

https://docs.near.org/docs/api/rpc

Command:
```
sudo apt install curl jq
```
###### Common Commands:
####### Check your node version:
Command:
```
curl -s http://127.0.0.1:3030/status | jq .version
```
####### Check Delegators and Stake
Command:
```
near view <your pool>.factory.shardnet.near get_accounts '{"from_index": 0, "limit": 10}' --accountId <accountId>.shardnet.near
```
####### Check Reason Validator Kicked
Command:
```
curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.prev_epoch_kickout[] | select(.account_id | contains ("<POOL_ID>"))' | jq .reason
```
####### Check Blocks Produced / Expected
Command:
```
curl -s -d '{"jsonrpc": "2.0", "method": "validators", "id": "dontcare", "params": [null]}' -H 'Content-Type: application/json' 127.0.0.1:3030 | jq -c '.result.current_validators[] | select(.account_id | contains ("POOL_ID"))'
```

### Good luck!
