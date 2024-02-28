# Penumbra

Penumbra Node Installation Instructions </br>
### [Official site](https://protocol.penumbra.zone/main/index.html)

### System requirements: </br>
CPU: 4 Core </br>
RAM: 8 Gb </br>
SSD: 200 Gb </br>
OS: Ubuntu 20.04 LTS </br>

### Network configurations: </br>
Outbound - allow all traffic </br>
Inbound - open the following ports :</br>
1317 - REST </br>
26657 - TENDERMINT_RP </br>
26656 - Cosmos </br>
Running as a Provider? Add these specific ports: </br>
22221 - provider port </br>
22231 - provider port </br>
22241 - provider port </br>
    
# Manual configuration of the Lava node with Cosmovisor
Required packages installation </br>
```
sudo apt update
sudo apt upgrade -y
sudo apt install -y curl git jq lz4 build-essential
sudo apt install -y unzip logrotate git jq sed wget curl coreutils systemd
sudo apt install make curl tar wget jq build-essential -y
sudo apt install make clang pkg-config libssl-dev -y 
```


### Install Rust.
```
sudo apt install curl build-essential gcc make -y
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh   (SET 1)
source ~/.profile
source ~/.cargo/env
```

### Go installation.
```
cd $HOME
sudo rm -rf /usr/local/go
curl -L https://go.dev/dl/go1.21.6.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> $HOME/.bash_profile
source .bash_profile
go version
```

### Install and start CometBFT.
```
cd /root
git clone https://github.com/cometbft/cometbft.git
cd cometbft
git checkout v0.37.2
go mod tidy
go build -o cometbft ./cmd/cometbft
mv cometbft /root/cometbft/
make install
ulimit -n 4096
Apt install screen
screen -S cometbft
cometbft start --home ~/.penumbra/testnet_data/node0/cometbft
```

### Install and start pcli.
```
git clone https://github.com/penumbra-zone/penumbra
cd penumbra && git fetch && git checkout v0.68.0 && cargo update
cargo build --release --bin pcli
cargo build --release --bin pd
pcli --version
cargo run --bin pd --release -- testnet unsafe-reset-all
cargo run --bin pd --release -- testnet join
screen -S pd
cd penumbra
cargo run --bin pd --release -- start --home ~/.penumbra/testnet_data/node0/pd
```

### Becoming a Validator

# Create wallet key new
```
cargo run --quiet --release --bin pcli init soft-kms generate
cargo run --quiet --release --bin pcli view address 0
```

### We receive tokens from the tap in the [discord](https://discord.gg/YSkrnprKMk)

Go to the #testnet-faucet branch and specify your penumbra wallet(penumbra...)

### Create the Validator

Before creating a validator, enter the command and check that you have false. This means that the Node has synchronized and you can create a validator:
```
cargo run --quiet --release --bin pcli view sync
```

### Check the balance before creating for the presence of tokens
```
cargo run --quiet --release --bin pcli view balance
```

Please enter your details below in quotation marks where required

```
cargo run --release --bin pcli validator definition template
```
Copy and write the value value from the priv_validator_key.json file to notepad
```
grep -A3 pub_key ~/.penumbra/testnet_data/node0/cometbft/config/priv_validator_key.json
```
Delete the old one and create a new validator file
```
rm -rf validator.toml && nano validator.toml
```

Next, you need to perform a number of actions:
We insert the data shown in the screenshot above.
Changing the value copied from priv_validator_key.json.
Adding the node name to the "name" parameter
, Adding the site and description (optional)
, changing the "enabled" parameter from false to true
The "sequence_number" parameter is changed from 0 to 1
Save the changes using the keyboard shortcut Ctrl +X > y> Enter

Uploading the changes to the validator file
```
cargo run --release --bin pcli -- validator definition upload --file validator.toml
```

We will find out the wallet and validator addresses using the following commands
```
cargo run --quiet --release --bin pcli view address 0
cargo run --release --bin pcli -- validator identity
```
Creating variables with the address of the wallet and validator
```
PENUMBRA_WALLET=address wallet
PENUMBRA_VALID=address validator

echo "export PENUMBRA_WALLET="${PENUMBRA_WALLET}"" >> $HOME/.bash_profile 
echo "export PENUMBRA_VALID="${PENUMBRA_VALID}"" >> $HOME/.bash_profile 
source $HOME/.bash_profile 
echo -e "\npenumbra_wallet > ${PENUMBRA_WALLET}.\n" 
echo -e "\npenumbra_valid > ${PENUMBRA_VALID}.\n" 
```
Delegating tokens to our validator
```
cargo run --release --bin pcli -- tx delegate 1000penumbra --to $PENUMBRA_VALID
```
We check our validator in the list
```
cargo run --release --bin pcli -- query validator list -i
```

### Update
```
rm -rf penumbra-zone
git clone https://github.com/penumbra-zone/penumbra
cd penumbra && git fetch && git checkout v0.68.0 && cargo update
cargo build --release --bin pcli
cargo build --release --bin pd
pcli --version
cargo run --bin pd --release -- testnet unsafe-reset-all
cargo run --bin pd --release -- testnet join
screen -S pd
cd penumbra
cargo run --bin pd --release -- start --home ~/.penumbra/testnet_data/node0/pd

Current network:penumbra-zone
Current version:v0.68.0
```
