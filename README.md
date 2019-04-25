# lnd-hands-on
This text is for lnd library hands on. We use lnd with bitcoind.  

## What is Lightning Network?
- ビットコインのLayer2技術。オンチェーンではなくオフチェーンで送金し、ビットコインのスケーラビリティ問題を解決する。

## How Lightning Network work?
1. AさんとBさんでチャネルを開く
  - AさんとBさんでマルチシグのアドレスを作る
  - Aさんがそのアドレスに送金（デポジット）する（funding transactionを生成する）
2. BさんがAさんにインボイス（請求書）を発行する
3. AさんがインボイスをもとにBさんに送金する
4. チャネルをクローズする（closing transactionを生成する）
  - チャネルでのやりとりをもとに、マルチシグアドレスからAさん、Bさんに送金される

## Installing bitcoind
```
sudo apt-add-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install bitcoind
```
cf. https://bitcoin.org/en/full-node#ubuntu-1604

## Running bitcoind
```
sudo mkdir /datadrive/.bitcoin
sudo chown username /datadrive/.bitcoin
vi .bitcoin/bitcoin.conf
bitcoind
```
```
# bitcoin.conf
server=1
txindex=1
daemon=1
testnet=3
#externalip=X.X.X.X
maxconnections=10
rpcuser=user
rpcpassword=password
minrelaytxfee=0.00000000
incrementalrelayfee=0.00000010
zmqpubrawblock=tcp://127.0.0.1:28332
zmqpubrawtx=tcp://127.0.0.1:28333
```
```
# 進行状況は以下のログを確認（30分以上はかかる。。）
tail -f .bitcoin/testnet3/debug.log
```

## Installing golang (for lnd)
```
wget https://dl.google.com/go/go1.12.3.linux-amd64.tar.gz
sha256sum go1.12.3.linux-amd64.tar.gz | awk -F " " '{ print $1 }'
sudo tar -C /usr/local -xzf go1.12.3.linux-amd64.tar.gz
export PATH=$PATH:/home/username/go/bin:/usr/local/go/bin
go
```

## Installing lnd
```
go get -d github.com/lightningnetwork/lnd
cd ~/go/src/github.com/lightningnetwork/lnd/
sudo apt install make
make && make install
```

## Running lnd
```
vi ~/.bash_profile
source ~/.bash_profile
mkdir -p ~/.lnd/user/alice
mkdir -p ~/.lnd/user/bob
lnd-alice
lncli-alice create
Do you have an existing cipher seed mnemonic you want to use? (Enter y/n): n
lncli-alice newaddress np2wkh # failed until bitcoind sync is finished
```
```
export PATH=$PATH:/home/username/go/bin:/usr/local/go/bin
alias lncli-alice="lncli --lnddir /home/ap/.lnd/user/alice --rpcserver=localhost:10001 --no-macaroons"
alias lncli-bob="lncli --lnddir /home/ap/.lnd/user/bob --rpcserver=localhost:10002 --no-macaroons"
alias lnd-alice="/home/ap/go/bin/lnd --bitcoin.testnet --lnddir=/home/ap/.lnd/user/alice"
alias lnd-bob="/home/ap/go/bin/lnd --bitcoin.testnet --lnddir=/home/ap/.lnd/user/bob"
```

```
[Application Options]
debuglevel=info
maxpendingchannels=10
datadir=~/.lnd/user/alice/data
tlscertpath=~/.lnd/user/alice/tls.cert
no-macaroons=true

# Specify the interfaces to listen on for gRPC connections.  One listen
# address per line.
# Only ipv4 localhost on port 10001:
  rpclisten=localhost:10001
  listen=localhost:10011
  restlisten=localhost:8001

[Bitcoin]
bitcoin.active=1
bitcoin.testnet=1
bitcoin.node=bitcoind

[Bitcoind]
bitcoind.rpchost=localhost
bitcoind.rpcuser=user
bitcoind.rpcpass=password
bitcoind.zmqpubrawblock=tcp://127.0.0.1:28332
bitcoind.zmqpubrawtx=tcp://127.0.0.1:28333
```

```
[Application Options]
debuglevel=info
maxpendingchannels=10
datadir=~/.lnd/user/bob/data
tlscertpath=~/.lnd/user/bob/tls.cert
no-macaroons=true

# Specify the interfaces to listen on for gRPC connections.  One listen
# address per line.
# Only ipv4 localhost on port 10002:
  rpclisten=localhost:10002
  listen=localhost:10012
  restlisten=localhost:8002

[Bitcoin]
bitcoin.active=1
bitcoin.testnet=1
bitcoin.node=bitcoind

[Bitcoind]
bitcoind.rpchost=localhost
bitcoind.rpcuser=user
bitcoind.rpcpass=password
bitcoind.zmqpubrawblock=tcp://127.0.0.1:28332
bitcoind.zmqpubrawtx=tcp://127.0.0.1:28333
```
cf. https://dev.lightning.community/tutorial/01-lncli/index.html
