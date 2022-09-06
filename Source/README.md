<p style="font-size:14px" align="right">
<a href="https://t.me/BeritaCryptoo" target="_blank">Join our telegram <img src="https://user-images.githubusercontent.com/50621007/183283867-56b4d69f-bc6e-4939-b00a-72aa019d1aea.png" width="30"/></a>
<a href="https://twitter.com/BeritaCryptoo" target="_blank">Join our twitter <img src="https://user-images.githubusercontent.com/108946833/184274157-08210464-fa03-493d-b01c-2420c67a524f.jpg" width="30"/></a>
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/44331529/183239082-09722b8d-9cc7-49a1-9d93-15ce3ab8d752.png">
</p>

# Source Testnet

|  Komponen |  Persyaratan Minimum |
| ------------ | ------------ |
| CPU  | 4 or more physical CPU cores  |
| RAM | At least 8GB of memory (RAM) |
| Penyimpanan  | At least 160GB of SSD disk storage |
| koneksi | At least 100mbps network bandwidth |

## Perangkat Lunak

|Komponen | Persyaratan Minimum |
| ------------ | ------------ |
| OS | Ubuntu 20.04 | 

## Install Otomatis
```console
wget -O source.sh https://raw.githubusercontent.com/xsons/TestnetNode/main/Source/source.sh && chmod +x source.sh && ./source.sh
```
Opsi 2 (manual)

Anda dapat mengikuti [panduan manual](https://github.com/xsons/TestnetNode/blob/main/Source/Manual.md) jika Anda lebih suka mengatur node secara manual

## Install Snapshots
```bash
# install the node as standard, but do not launch. Then we delete the .data directory and create an empty directory
sudo systemctl stop sourced
rm -rf $HOME/.source/data/
mkdir $HOME/.source/data/

# download archive
cd $HOME
wget http://116.202.236.115:8000/sourcedata.tar.gz

# unpack the archive
tar -C $HOME/ -zxvf sourcedata.tar.gz --strip-components 1
# !! IMPORTANT POINT. If the validator was created earlier. Need to reset priv_validator_state.json  !!
wget -O $HOME/.source/data/priv_validator_state.json "https://raw.githubusercontent.com/xsons/StateSync-snapshots/main/priv_validator_state.json"
cd && cat .source/data/priv_validator_state.json
{
  "height": "0",
  "round": 0,
  "step": 0
}

# after unpacking, run the node
# don't forget to delete the archive to save space
cd $HOME
rm sourcedata.tar.gz
# start the node
sudo systemctl restart sourced && journalctl -u sourced -f -o cat
```
### Buat Dompet atau Pulihkan Dompet
```
sourced keys add <wallet name>
  or
sourced keys add <wallet name> --recover
```
### Periksa Set Dompet
```
sourced debug addr <wallet address>
```
Dapatkan Token Faucet Di Discord
>- [Discord Official Source](https://discord.gg/MgcfAgrD)

### Buat Validator
```console
sourced tx staking create-validator \
--amount=1000000usource \
--pubkey=$(sourced tendermint show-validator) \
--moniker=<moniker> \
--chain-id=sourcechain-testnet \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--fees=100usource \
--from=<walletName> \
--identity="" \
--website="" \
--details="" \
-y
```
### Dapatkan daftar validator
- Untuk Validator Aktif
```console
sourced q staking validators -o json --limit=3000 \
| jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' \
| jq -r '.tokens + " - " + .description.moniker' \
| sort -gr | nl
```
- Untuk Validator Tidak Aktif
```console
sourced q staking validators -o json --limit=3000 \
| jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' \
| jq -r '.tokens + " - " + .description.moniker' \
| sort -gr | nl
```
## Perintah yang berguna
### Manajemen Pelayanan
Periksa log
```console
journalctl -fu sourced -o cat
```
Memulai layanan
```console
sudo systemctl start sourced
```
Hentikan layanan
```console
sudo systemctl stop sourced
```
Mulai ulang layanan
```console
sudo systemctl restart sourced
```
## Informasi simpul
Informasi sinkronisasi
```console
sourced status 2>&1 | jq .SyncInfo
```
Info validator
```console
seid status 2>&1 | jq .ValidatorInfo
```
Informasi simpul
```console
sourced status 2>&1 | jq .NodeInfo
```
Tampilkan id simpul
```console
sourced tendermint show-node-id
```
### Operasi dompet
Daftar dompet
```console
sourced keys list
```
Pulihkan dompet
```console
sourced keys add <wallet name> --recover
```
Hapus dompet
```console
sourced keys delete <wallet name>
```
Cadangan Kunci Pribadi
```console
sourced keys export <wallet name> --unarmored-hex --unsafe
```
Dapatkan saldo dompet
```console
sourced query bank balances <wallet address>
```
Transfer dana
```console
sourced tx bank send <wallet address> <to sei wallet address> 10000000usource
```
Pemungutan suara
```console
sourced tx gov vote 1 yes --from <wallet name> --chain-id=sourcechain-testnet
```
### Staking, Delegasi, dan Hadiah
Delegasikan saham
```console
sourced tx staking delegate <valoper address> 10000000usource --from=<waller name> --chain-id=sourcechain-testnet --gas=auto
```
Delegasikan ulang stake dari validator ke validator lain
```console
sourced tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000usource --from=<wallet name> --chain-id=sourcechain-testnet --gas=auto
```
Tarik semua hadiah
```console
sourced tx distribution withdraw-all-rewards --from=<wallet name> --chain-id=sourcechain-testnet1 --gas=auto
```
Tarik hadiah dengan komisi
```console
sourced tx distribution withdraw-rewards <your valoper address> --from=<wallet name> --commission --chain-id=sourcechain-testnet
```

### Manajemen Validator
Edit validator
```console
sourced tx staking edit-validator \
  --moniker=<your moniker> \
  --identity=<your_keybase_id> \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=sourcechain-testnet \
  --from=<wallet name>
 ```
Unjail validator
```console
sourced tx slashing unjail \
  --broadcast-mode=block \
  --from=<your wallet name> \
  --chain-id=sourcechain-testnet \
  --gas=auto
 ```
## Hapus node

Hapus node Perintah ini akan menghapus node sepenuhnya dari server. Gunakan dengan risiko Anda sendiri!
```console
sudo systemctl stop sourced && \
sudo systemctl disable sourced && \
rm /etc/systemd/system/sourced.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf .source && \
rm -rf source && \
rm -rf source.sh \
rm -rf $(which sourced)
```