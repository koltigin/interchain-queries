# interchain-queries
Interchain Queries

# Görev-9 Rehberi

## 🛰  Relayer Görevi 

| #     | Pts |  Task                                                                                                             | Evidence                                                                           | Instructions      |
| ----- | --- | ----------------------------------------------------------------------------------------------------------------- |:----------------------------------------------------------------------------------:| ----------------- |
| **9** | 750 | relay interchain queries using the new [v2 go relayer](https://github.com/cosmos/relayer/releases/tag/v2.0.0-rc4) | link to ICQ packets relayed and link to the configured relayer fork on your github | - |

## Relayer Kurulumunu Yapma
```
cd $HOME
git clone https://github.com/Stride-Labs/interchain-queries.git
cd interchain-queries
go build
sudo mv interchain-queries /usr/local/bin/icq
```

## Config Dosyasını Ayarlama
Aşağıdaki kodda şu bölümleri düzenleyiniz;
- `CUZDAN_ADINIZ`
- `rpc-addr`
- `grpc-addr`
```
cd $HOME && mkdir .icq
sudo tee $HOME/.icq/config.yaml > /dev/null <<EOF
default_chain: stride-testnet
chains:
  stride-testnet:
    key: CUZDAN_ADINIZ
    chain-id: STRIDE-TESTNET-4
    rpc-addr: http://127.0.0.1:16657    # Stride RPC Adresinizi Yazınız
    grpc-addr: http://127.0.0.1:16090     # Stride GRPC Adresinizi Yazınız
    account-prefix: stride
    keyring-backend: test
    gas-adjustment: 1.2
    gas-prices: 0.001ustrd
    key-directory: /root/.icq/keys
    debug: false
    timeout: 20s
    block-timeout: ""
    output-format: json
    sign-mode: direct
  gaia-testnet:
    key: CUZDAN_ADINIZ
    chain-id: GAIA
    rpc-addr: http://127.0.0.1:23657    # Gaia RPC Adresinizi Yazınız
    grpc-addr: http://127.0.0.1:23090   # Gaia GRPC Adresinizi Yazınız
    account-prefix: cosmos
    keyring-backend: test
    gas-adjustment: 1.2
    gas-prices: 0.001uatom
    key-directory: /root/.icq/keys
    debug: false
    timeout: 20s
    block-timeout: ""
    output-format: json
    sign-mode: direct
cl: {}

EOF
```

## Cüzdanı İçeri Aktarma
> NOT: Aynı cüzdanınızı kullanınız. Farklı cüzdanlar kullanmayınız. Node cüzdanınız, relayer çalıştırdığınız cüzdanınız olsun.
```
icq keys restore --chain stride-testnet wallet
icq keys restore --chain gaia-testnet wallet
```
> Cüzdan Mnemonic Kelimelerinizi Giriniz.

## Icq Servis Dosyası Oluşturma
```
sudo tee /etc/systemd/system/icqd.service > /dev/null <<EOF
[Unit]
Description=Interchain Query Service
After=network-online.target
[Service]
User=$USER
ExecStart=$(which icq) run --debug
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## Servisi Başlatma
```
sudo systemctl daemon-reload
sudo systemctl enable icqd
sudo systemctl restart icqd
```

## Log Kontrol Etme
```
journalctl -u icqd -f -o cat
```

**Logların görünmesi 10-15 dakika sürebilir, beklemelisiniz:**

### Örnek Çıktı aşağıdaki gibi olmalıdır;
```
store/bank/key
height parsed from GetHeightFromMetadata= 0
Fetching client update for height height 176886
store/bank/key
height parsed from GetHeightFromMetadata= 0
Fetching client update for height height 176886
Requerying lightblock
Requerying lightblock
Requerying lightblock
ICQ RELAYER | query.Height= 0
ICQ RELAYER | res.Height= 176885
Requerying lightblock
ICQ RELAYER | query.Height= 0
ICQ RELAYER | res.Height= 176885
Send batch of 4 messages
1 ClientUpdate message
1 SubmitResponse message
1 ClientUpdate message
1 SubmitResponse message
Sent batch of 2 (deduplicated) messages
```

**Tx'leri, explorerdan kontrol edip not alalım**

**Form**
> https://docs.google.com/forms/d/e/1FAIpQLSeoZEC5kd89KCQSJjn5Zpf-NQPX-Gc8ERjTIChK1BEbiVfMVQ/viewform

### Herkese Başarılar Dilerim.
