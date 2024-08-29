# linea-on-akash

This is template to run [Linea L2 node](https://docs.linea.build/) on [Akash Network](https://akash.network/).

---

## Deploy Ubuntu

- Deploy Ubuntu on Akash using deploy.yaml file from this repo.
- Create an SSH connection with URI (only domain name) and forwarded port 22 from leases tab in Akash Console.

## Download Geth and Run a Linea node

- Download Geth binary (only supports Geth up to v1.13.15 or lower) and unzip it.
```
wget https://gethstore.blob.core.windows.net/builds/geth-linux-amd64-1.13.15-c5ba367e.tar.gz && tar -xvf geth-linux-amd64-1.13.15-c5ba367e.tar.gz && cd geth-linux-amd64-1.13.15-c5ba367e
```

- Download the genesis file.
```
wget https://docs.linea.build/files/geth/mainnet/genesis.json
```

- Bootstrap your node.
```
./geth --datadir ./geth-linea-data init ./genesis.json
```

- Create tmux session.
```
tmux new -t linea
```

- Start the Geth client
```
./geth \
--datadir ./geth-linea-data \
--networkid 59144 \
--rpc.allow-unprotected-txs \
--txpool.accountqueue 50000 \
--txpool.globalqueue 50000 \
--txpool.globalslots 50000 \
--txpool.pricelimit 1000000 \
--txpool.pricebump 1 \
--txpool.nolocals \
--http --http.addr '0.0.0.0' --http.port 8545 --http.corsdomain '*' --http.api 'web3,eth,txpool,net' --http.vhosts='*' \
--ws --ws.addr '0.0.0.0' --ws.port 8546 --ws.origins '*' --ws.api 'web3,eth,txpool,net' \
--bootnodes "enode://069800db9e6e0ec9cadca670994ef1aea2cfd3d88133e63ecadbc1cdbd1a5847b09838ee08d8b5f02a9c32ee13abeb4d4104bb5514e5322c9d7ee19f41ff3e51@3.132.73.210:31002,enode://a8e03a71eab12ec4b47bb6e19169d8e4dc7a58373a2476969bbe463f2dded6003037fa4dd5f71e15027f7fc8d7340956fbbefed67ddd116ac19a7f74da034b61@3.132.73.210:31003,enode://97706526cf79df9d930003644f9156805f6c8bd964fc79e083444f7014ce10c9bdd2c5049e63b58040dca1d4c82ebef970822198cf0714de830cff4111534ff1@18.223.198.165:31004,enode://24e1c654a801975a96b7f54ebd7452ab15777fc635c1db25bdbd4425fdb04e7f4768e9e838a87ab724320a765e41631d5d37758c933ad0e8668693558125c8aa@18.223.198.165:31000,enode://27010891d960f73d272a553f72b6336c6698db3ade98d631f09c764e57674a797be5ebc6829ddbb65ab564f439ebc75215d20aa98b6f351d12ea623e7d139ac3@3.132.73.210:31001,enode://228e1b8a4931e46f383e30721dac21fb8fb4e5e1b32c870e13b25478c82db3dc1cd9e7ceb93d302a766466b55638cc9c5cbfc43aa48fa41ced19baf365951f76@3.1.142.64:31002,enode://c22eb0d40fc3ad5ea710aeddea906567778166bfe18c157955e8c39b23a46c45db18a0fa2ba07f2b64c81178a8c796aec2a29151533920ead06fcdfc6d8d03c6@47.128.192.57:31004,enode://8ce733abe39fd7ae0a278b9893f85c1193c611a3886168690dd843435460f22cc4d61f9e8d0ace7f5905836a665319a31cccdaacdada2acc69972c382ecce7db@3.1.142.64:31003,enode://b7c1b2bed65a855f7a2104aac9a14674dfdf018fdac763415b373b29ce18cdb81d36328ba4e5c9f12629f3a50c3e8f9ee048f22dbdbe93a82813da89c6b81334@51.20.235.126:31004,enode://95270e0550848a72fb141cf27f1c4ea10714edde365b411dc0fa06c81c0f282ce155eb9fa472b6b8bb9ee98395eeaf4c5a7b02a01fe58b37ea98ba152eda4c37@13.50.94.193:31000,enode://72013391755f24f08567b932feeeec4c893c06e0b1fb480890c83bf87fd277ad86a5ab9cb586db9ae9970371a2f8cb0c96f6c9f69045abca0fb801db7f047138@51.20.235.126:31001" \
--syncmode full \
--metrics \
--verbosity 3
```
When node starts to sync you can exit from your tmux session with key combination ctrl+b and then d.

## Confirm the node is running
You can call the JSON-RPC API methods to confirm the node is running. For example, call eth_syncing to return the synchronization status. For example the starting, current, and highest block, or false if not synchronizing (or if the head of the chain has been reached).
```
curl localhost:8545 \
        -X POST \
        -H "Content-Type: application/json" \
        -d '{"jsonrpc":"2.0","method":"eth_syncing","params":[],"id":1}'
```
You should get a result similar to:
```
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "startingBlock": "0x0",
    "currentBlock": "0x5d228",
    "highestBlock": "0x3cedec"
  }
}
```
