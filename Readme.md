Ubuntu 18.04 is recommended

Fullnode setup:
```
mkdir -p ~/.local/share/io.parity.ethereum/
wget https://raw.githubusercontent.com/vlddm/market-maker-keeper-setup/master/config.toml -O ~/.local/share/io.parity.ethereum/config.toml
```
If you have ssd storade edit downloaded config.toml to reflect that.
Set `db_compaction="ssd"`

Next:
```
mkdir -p ~/.config/systemd/user/
wget https://raw.githubusercontent.com/vlddm/market-maker-keeper-setup/master/parity.service -O ~/.config/systemd/user/parity.service

mkdir -p ~/bin
cd ~/bin
wget https://releases.parity.io/ethereum/v2.4.7/x86_64-unknown-linux-gnu/parity
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.8.27-4bcc0a37.tar.gz
tar -xf geth-linux-amd64-1.8.27-4bcc0a37.tar.gz --strip 1

sudo loginctl enable-linger $USER
systemctl --user daemon-reload
systemctl --user enable parity.service
systemctl --user start parity.service
```
Check if node is running well: 
`ps auxww | fgrep parity` should return process with full agruments and metadata.
If not user `journalctl --user -u parity.service` to check logs


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

Put account address into config.toml ~/.local/share/io.parity.ethereum/config.toml and password into ~/.savepw file

Running:
```
bin/oasis-market-maker-keeper \
    --rpc-host 127.0.0.1 \
    --rpc-port 8545 \
    --rpc-timeout 30 \
    --eth-from [address of the generated Ethereum account] \
    --tub-address 0x448a5065aebb8e423f0896e6c5d525c040f59af3 \
    --oasis-address 0x39755357759cE0d7f32dC8dC45414CCa409AE24e \
    --price-feed eth_dai \
    --buy-token-address  0x89d24A6b4CcB1B6fAA2625fE562bDD9a23260359 \
    --sell-token-address 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2 \
    --config [path to the json bands configuration file] \
    --smart-gas-price \
    --min-eth-balance 0.2
```
