# [Run-fullnode-Sui](https://docs.sui.io/build/fullnode)

[<img width="200" alt="665875" src="https://user-images.githubusercontent.com/93165931/181920110-c9385bd6-5d22-4fa9-98e2-c37e2d282fda.png">
](https://docs.sui.io/)

### Installing plugins

```
sudo apt update && sudo apt install curl -y < "/dev/null"\
echo -e '\n\e[42mInstall software\e[0m\n' && sleep 1
apt-get update && DEBIAN_FRONTEND=noninteractive TZ=Etc/UTC apt-get install -y --no-install-recommends tzdata git ca-certificates curl build-essential libssl-dev pkg-config libclang-dev 
cmake jq
echo -e '\n\e[42mInstall Rust\e[0m\n' && sleep 1
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source $HOME/.cargo/env

```

### Git clone and remote conection:

```
rm -rf /var/sui/db /var/sui/genesis.blob $HOME/sui
mkdir -p /var/sui/db
cd $HOME
git clone https://github.com/MystenLabs/sui.git
cd sui
git remote add upstream https://github.com/MystenLabs/sui
git fetch upstream
```

![image](https://user-images.githubusercontent.com/93165931/181920404-5a8396c4-190f-47c9-b894-f5fff979b837.png)

```
git checkout --track upstream/devnet

```

![image](https://user-images.githubusercontent.com/93165931/181920496-ae8df11c-2213-457e-9869-facb8b8ce224.png)

### Sync your Node:

```
cp crates/sui-config/data/fullnode-template.yaml /var/sui/fullnode.yaml
#curl -fLJO https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blob
wget -O /var/sui/genesis.blob https://github.com/MystenLabs/sui-genesis/raw/main/devnet/genesis.blob
sed -i.bak "s/db-path:.*/db-path: \"\/var\/sui\/db\"/ ; s/genesis-file-location:.*/genesis-file-location: \"\/var\/sui\/genesis.blob\"/" /var/sui/fullnode.yaml
cargo build --release
mv ~/sui/target/release/sui-node /usr/local/bin/
mv ~/sui/target/release/sui /usr/local/bin/
sed -i.bak 's/127.0.0.1/0.0.0.0/' /var/sui/fullnode.yaml

```
Post build, receive the success confirmation message, `SuiNode started`!

### Make a copy and edit of the configuration file:

```
echo "[Unit]
Description=Sui Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=/usr/local/bin/sui-node --config-path /var/sui/fullnode.yaml
Restart=on-failure
LimitNOFILE=10000

[Install]
WantedBy=multi-user.target" > $HOME/suid.service

mv $HOME/suid.service /etc/systemd/system/
sudo tee <<EOF >/dev/null /etc/systemd/journald.conf
Storage=persistent
EOF

```

### Restart Node:

```
sudo systemctl daemon-reload
sudo systemctl enable suid
sudo systemctl restart suid

```


### Stop Node:

```
sudo systemctl stop suid

```

### Delete Node:

```
sudo systemctl stop suid

sudo systemctl disable suid

rm -rf ~/sui /var/sui/

rm /etc/systemd/system/suid.service

```
### Check your logs

```
journalctl -u suid -f -o cat

```
### Check status Node:

```
curl -s -X POST http://127.0.0.1:9000 -H 'Content-Type: application/json' -d '{ "jsonrpc":"2.0", "method":"rpc.discover","id":1}' | jq .result.info
```

### Send ip-server to discord [channel](https://discord.com/channels/916379725201563759/986662676073709568)!

### You can check the operation of the node in the explorer: 
- [Sui Explorer](https://explorer.devnet.sui.io/)
- [SUITESTER](https://node.sui.zvalid.com/)

