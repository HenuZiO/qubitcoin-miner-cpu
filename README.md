# QubitCoin CPU Miner

CPU version of the miner for QubitCoin mining, based on the [official GPU miner](https://github.com/super-quantum/qubitcoin-miner).

## Features

- Pure CPU implementation of quantum simulator
- No external quantum libraries required
- Support for AVX2 and AVX512 instructions
- Can work simultaneously with a GPU miner
- Developer fee: 5%
- Universal package supports both HiveOS and mmpOS

## System Requirements

### Supported Processors

- Processors with AVX2 or AVX512 instruction support
- Tested on Ryzen 7950x3d and Ryzen 7950x

### Operating Systems

- Ubuntu 24.04 (works "out of the box")
- Ubuntu 22.04 (requires GLIBC update - instructions below)
- macOS
- HiveOS (fully supported with universal package)
- mmpOS (fully supported with universal package)

## Performance

### Miner Performance Comparison:

- **Official GPU Miner**: ~4.5 kh/s on RTX 4070

- **Our CPU Miner**:

  - 12.6 kh/s on Ryzen 9 7950x (30 threads)
  - 11.9 kh/s on Ryzen 7950x3d (26 threads)

- **AllFather GPU Miner**: 20 kh/s on RTX 4070

### Combined Usage:

When using CPU miner and AllFather GPU miner simultaneously on the same system:

- Ryzen 7950x3d (26 threads): 11.9 kh/s
- RTX 4070 (AllFather GPU miner): 17.2 kh/s

- **Total Performance**: 29.1 kh/s

## HiveOS & mmpOS Setup

Now you can easily run the miner through HiveOS or mmpOS web interface using universal custom miner package!

**All dependencies (including GLIBC) will be automatically installed before miner startup. No manual installation required. Compatible with both HiveOS and mmpOS running Ubuntu 22.04.**

### Flight Sheet Configuration

**Basic Settings:**

- **Coin:** QTC
- **Wallet:** Create QTC wallet address and use it
- **Pool:** Configure in miner
- **Miner:** Custom

<img width="1108" height="270" alt="image" src="https://github.com/user-attachments/assets/8cbdc95f-85b3-4de1-8a61-18b8095356f5" />

**Miner Configuration (Setup miner config):**

- **Installation URL:** `https://github.com/HenuZiO/qubitcoin-miner-cpu/releases/download/cpu-1.0.1/qubitcoin_miner_cpu_custom.tar.gz`
- **Hash Algorithm:** Leave empty
- **Wallet and worker template:** `%WAL%.%WORKER_NAME%`
- **Pool URL:** `qubitcoin.luckypool.io:8611`
- **Pass:** `x` or leave empty
- **Extra config arguments:** Number of threads for mining, e.g., `-t 16` or `-t 30`

<img width="681" height="706" alt="image" src="https://github.com/user-attachments/assets/3b6574ae-feb0-44ab-9b94-264de4ad8989" />

### Ready-to-Use Flight Sheet JSON

You can import this JSON configuration directly into HiveOS:

```json
{
  "name": "QTC | LuckyPool",
  "isFavorite": false,
  "items": [
    {
      "coin": "QTC",
      "pool_ssl": false,
      "wal_id": 10869310,
      "dpool_ssl": false,
      "miner": "custom",
      "miner_alt": "qubitcoin_miner_cpu_custom",
      "miner_config": {
        "url": "qubitcoin.luckypool.io:8611",
        "pass": "x",
        "miner": "qubitcoin_miner_cpu_custom",
        "template": "%WAL%.%WORKER_NAME%",
        "install_url": "https://github.com/HenuZiO/qubitcoin-miner-cpu/releases/download/cpu-1.0.1/qubitcoin_miner_cpu_custom.tar.gz",
        "user_config": "-t 16"
      },
      "pool_geo": []
    }
  ]
}
```

**Note:** Don't forget to:

1. Replace the wallet ID (`wal_id`) with your actual wallet ID from HiveOS
2. Adjust the thread count in `user_config` (e.g., `-t 30` for 30 threads)

## Manual CLI Usage (Alternative Method)

**Note:** If you prefer manual setup or don't use HiveOS, you can run the miner directly via command line.

1. **Create directory and download miner:**

```bash
mkdir -p ~/qubitcoin-miner && cd ~/qubitcoin-miner
```

```bash
wget https://github.com/HenuZiO/qubitcoin-miner-cpu/releases/download/cpu-1.0.1/qubitcoin-miner-cpu.tar.gz
```

```bash
tar -xvf qubitcoin-miner-cpu.tar.gz && rm -rf qubitcoin-miner-cpu.tar.gz
```

```bash
sudo chmod +x ./qubitcoin-miner-cpu
```

2. **Install dependencies:**

```bash
sudo apt-get update && sudo apt-get install -y build-essential libtool autotools-dev automake pkg-config bsdmainutils python3 libevent-dev libboost-dev libsqlite3-dev libminiupnpc-dev libnatpmp-dev libzmq3-dev systemtap-sdt-dev
```

### Ubuntu 22.04

On Ubuntu 22.04, you also need to update GLIBC:

```bash
sudo apt update && sudo apt install -y dirmngr gnupg gpg && \
echo "deb [signed-by=/usr/share/keyrings/ubuntu-noble.gpg] http://archive.ubuntu.com/ubuntu noble main universe" | sudo tee /etc/apt/sources.list.d/noble-temp.list && \
gpg --keyserver keyserver.ubuntu.com --recv-keys 3B4FE6ACC0B21F32 && \
gpg --export 3B4FE6ACC0B21F32 | sudo tee /usr/share/keyrings/ubuntu-noble.gpg > /dev/null && \
gpg --keyserver keyserver.ubuntu.com --recv-keys 871920D1991BC93C && \
gpg --export 871920D1991BC93C | sudo tee -a /usr/share/keyrings/ubuntu-noble.gpg > /dev/null && \
sudo apt update && \
sudo DEBIAN_FRONTEND=noninteractive apt install -y -t noble libjansson4 libstdc++6 && \
sudo rm /etc/apt/sources.list.d/noble-temp.list && \
sudo apt update
```

### Ubuntu 24.04

Works without additional configuration after installing dependencies.

### Running with Screen (Recommended)

To keep the miner running after disconnecting from remote session, use `screen`:

1. **Start a new screen session:**

```bash
screen -S qubitcoin-miner-cpu
```

2. **Start the miner:**

```bash
./qubitcoin-miner-cpu -a qhash -o qubitcoin.luckypool.io:8611 -u your-wallet.worker -t thread-count
```

3. **Detach from screen session (miner continues running):**

```
Ctrl + A, then D
```

4. **Reattach to screen session:**

```bash
screen -r qubitcoin-miner-cpu
```

5. **Check active screen sessions:**

```bash
screen -ls
```

### Command Line Parameters

- `-a qhash`: Algorithm (qhash for QubitCoin)
- `-o stratum+tcp://pool-address:port`: Pool address
- `-u your-wallet.worker-name`: Wallet address and worker name
- `-p x`: Password (usually 'x')
- `-t thread-count`: Number of threads for mining

## Combined Usage with GPU Miner

You can run both CPU and GPU miners simultaneously for maximum performance. To do this, launch two separate miner instances on the same computer.

## Dependencies

- libcurl
- libssl
- libcrypto
- libjansson
- libevent
- libzmq
- libgmp

## About the Project

This miner represents a port of quantum computation implementation from GPU (cuStateVec) to a pure CPU implementation, allowing mining of QubitCoin without using a graphics processor.

## Donations

While this miner includes a developer fee, donations are greatly appreciated and help support continued development:

**BTC:**

```
15VTJnGqEb7xKc5KQ1hJEqjQGzon7hydnH
```

Happy mining!
