# lnd-hands-on

## bitcoindのインストール
```
sudo apt-add-repository ppa:bitcoin/bitcoin
sudo apt-get update
sudo apt-get install bitcoind
```
参考 https://bitcoin.org/en/full-node#ubuntu-1604

## bitcoind起動
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
# 進行状況は以下のログを確認（20分くらいはかかる。。）
tail -f .bitcoin/testnet3/debug.log
```
