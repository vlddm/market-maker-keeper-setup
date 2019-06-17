Ubuntu 18.04

Fullnode setup:
```
mkdir -p ~/.local/share/io.parity.ethereum/
wget https://raw.githubusercontent.com/vlddm/market-maker-keeper-setup/master/config.toml -O ~/.local/share/io.parity.ethereum/config.toml
mkdir -p .config/systemd/user/
wget https://raw.githubusercontent.com/vlddm/market-maker-keeper-setup/master/parity.service -O .config/systemd/user/parity.service

mkdir -p ~/bin
cd ~/bin
wget https://releases.parity.io/ethereum/v2.4.7/x86_64-unknown-linux-gnu/parity
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.8.27-4bcc0a37.tar.gz
tar -xf geth-linux-amd64-1.8.27-4bcc0a37.tar.gz

sudo loginctl enable-linger $USER
systemctl --user daemon-reload
systemctl --user enable parity.service
systemctl --user start parity.service
```


Installing deps:
```
sudo apt-get install build-essential automake libtool pkg-config libffi-dev python3-dev python3-pip libsecp256k1-dev git
pip3 install --user virtualenv
source ~/.profile
```

Installing market-maker-keeper:
```
git clone https://github.com/makerdao/market-maker-keeper.git
cd market-maker-keeper
git submodule update --init --recursive
./install.sh
```

Creating wallet:
```
geth attach ~/.local/share/io.parity.ethereum/jsonrpc.ipc
personal.newAccount()
```

Put account address into config.toml ~/.local/share/io.parity.ethereum/config.toml and address into ~/.savepw file

Running:
```
bin/oasis-market-maker-keeper \
    --rpc-host 127.0.0.1 \
    --rpc-port 8180 \
    --rpc-timeout 30 \
    --eth-from [address of the generated Ethereum account] \
    --tub-address 0x448a5065aebb8e423f0896e6c5d525c040f59af3 \
    --oasis-address 0x14fbca95be7e99c15cc2996c6c9d841e54b79425 \
    --price-feed eth_dai \
    --buy-token-address [address of the quote token, could be DAI] \
    --sell-token-address [address of the base token, could be WETH] \
    --config [path to the json bands configuration file] \
    --smart-gas-price \
    --min-eth-balance 0.2
```
