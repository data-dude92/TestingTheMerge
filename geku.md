Compiled with a lot of help from here: https://notes.ethereum.org/@launchpad/kiln

# Environment

This guide has been tested as working on:

- a clean install of Ubuntu 20.04.4.

Please submit a PR if you are able to get it working in other environments.

# Install pre-requisites:

## golang

```
wget https://go.dev/dl/go1.17.8.linux-amd64.tar.gz
sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.17.8.linux-amd64.tar.gz

export PATH=$PATH:/usr/local/go/bin
go version
```

## build tools

```
sudo apt install -y make git gcc
```

## java

```
sudo apt install default-jre
```

# Create sandbox

## clone the config

```
git clone https://github.com/eth-clients/merge-testnets.git
cd merge-testnets/kiln
```

## generate a token

```
openssl rand -hex 32 | tr -d "\n" > "/tmp/jwtsecret"
```

# Build clients

## Geth (Execution Layer / EL)

```
git clone -b merge-kiln-v2 https://github.com/MariusVanDerWijden/go-ethereum.git
cd go-ethereum 
export PATH=$PATH:/usr/local/go/bin
make geth

cd ..
```

## teku (Consensus Layer / CL)

```
git clone https://github.com/ConsenSys/teku.git
cd teku
./gradlew installDist

cd ..
```

# Run

## Geth (EL)

```
./go-ethereum/build/bin/geth init genesis.json  --datadir "geth-datadir"
./go-ethereum/build/bin/geth --datadir "geth-datadir" --port 30304 --http --http.api="engine,eth,web3,net,debug" --http.corsdomain "*" --networkid=1337802 --syncmode=full --authrpc.jwtsecret=/tmp/jwtsecret --bootnodes "enode://c354db99124f0faf677ff0e75c3cbbd568b2febc186af664e0c51ac435609badedc67a18a63adb64dacc1780a28dcefebfc29b83fd1a3f4aa3c0eb161364cf94@164.92.130.5:30303,enode://d41af1662434cad0a88fe3c7c92375ec5719f4516ab6d8cb9695e0e2e815382c767038e72c224e04040885157da47422f756c040a9072676c6e35c5b1a383cce@138.68.66.103:30303,enode://91a745c3fb069f6b99cad10b75c463d527711b106b622756e9ef9f12d2631b6cb885f831d1c8731b9bc7177cae5e1ea1f1be087f86d7d30b590a91f22bc041b0@165.232.180.230:30303,enode://b74bd2e8a9f0c53f0c93bcce80818f2f19439fd807af5c7fbc3efb10130c6ee08be8f3aaec7dc0a057ad7b2a809c8f34dc62431e9b6954b07a6548cc59867884@164.92.140.200:30303" console
```

## teku (CL)

Open a new Terminal window.

```
./teku/build/install/teku/bin/teku --data-path "datadir-teku" --network kiln --p2p-discovery-bootnodes "enr:-Iq4QMCTfIMXnow27baRUb35Q8iiFHSIDBJh6hQM5Axohhf4b6Kr_cOCu0htQ5WvVqKvFgY28893DHAg8gnBAXsAVqmGAX53x8JggmlkgnY0gmlwhLKAlv6Jc2VjcDI1NmsxoQK6S-Cii_KmfFdUJL2TANL3ksaKUnNXvTCv1tLwXs0QgIN1ZHCCIyk,enr:-Iq4QMCTfIMXnow27baRUb35Q8iiFHSIDBJh6hQM5Axohhf4b6Kr_cOCu0htQ5WvVqKvFgY28893DHAg8gnBAXsAVqmGAX53x8JggmlkgnY0gmlwhLKAlv6Jc2VjcDI1NmsxoQK6S-Cii_KmfFdUJL2TANL3ksaKUnNXvTCv1tLwXs0QgIN1ZHCCIyk,enr:-KG4QFkPJUFWuONp5grM94OJvNht9wX6N36sA4wqucm6Z02ECWBQRmh6AzndaLVGYBHWre67mjK-E0uKt2CIbWrsZ_8DhGV0aDKQc6pfXHAAAHAyAAAAAAAAAIJpZIJ2NIJpcISl6LTmiXNlY3AyNTZrMaEDHlSNOgYrNWP8_l_WXqDMRvjv6gUAvHKizfqDDVc8feaDdGNwgiMog3VkcIIjKA,enr:-MK4QI-wkVW1PxL4ksUM4H_hMgTTwxKMzvvDMfoiwPBuRxcsGkrGPLo4Kho3Ri1DEtJG4B6pjXddbzA9iF2gVctxv42GAX9v5WG5h2F0dG5ldHOIAAAAAAAAAACEZXRoMpBzql9ccAAAcDIAAAAAAAAAgmlkgnY0gmlwhKRcjMiJc2VjcDI1NmsxoQK1fc46pmVHKq8HNYLkSVaUv4uK2UBsGgjjGWU6AAhAY4hzeW5jbmV0cwCDdGNwgiMog3VkcIIjKA" --ee-endpoint http://localhost:8551 --Xee-version kilnv2 --ee-jwt-secret-file "/tmp/jwtsecret" --log-destination console
```