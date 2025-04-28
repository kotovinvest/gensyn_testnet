<h2 align=center>Gensyn Testnet Node Guide</h2>

## 💻 System Requirements

| Requirement                         | Details                                                     |
|-------------------------------------|-------------------------------------------------------------|
| **CPU Architecture**                | `arm64` or `amd64`                                          |
| **Recommended RAM**                 | 24 GB                                                       |
| **CUDA Devices (Recommended)**      | `RTX 3090`, `RTX 4070`, `RTX 4090`, `A100`, `H100`          |
| **Python Version**                  | Python >= 3.10 (For Mac, you may need to upgrade)           |


## 🛜 Connect via SSH (Only for GPU)

> **Note**  
> This step is only required if you running this node on GPU, if you want to use VPS or WSL, then skip this step and login to your VPS server using username and password received from the VPS provider and then move to installation section.

1. First open a terminal (this could be either WSL / Codespace / Command Prompt)
2. Use this below command to generate SSH-Key:
```
ssh-keygen
```
It will ask 3 questions:
- Enter file in which to save the key (/home/codespace/.ssh/id_rsa):
- Enter passphrase (empty for no passphrase):
- Enter same passphrase again:

Press `Enter` 3 times.

After that you will get a message like:
```
Your public key has been saved in /home/codespace/.ssh/id_rsa.pub
```
3. View your public key:

- Linux/macOS (WSL): 
  ```
  cat /home/codespace/.ssh/id_rsa.pub
  ```
- Command Prompt:
  ```
  type \home\codespace\.ssh\id_rsa.pub
  ```
- PowerShell:
  ```
  Get-Content \home\codespace\.ssh\id_rsa.pub
  ```

4. Copy this public key and paste it into the settings on your hosting provider.
5. Now copy the SSH command provided by the GPU provider, for example:
```
ssh -p 69 root@69.69.69.69
```
6. Connect to your GPU server by pasting the command in terminal.

## 📥 Installation

**Install sudo**
```
apt update && apt install -y sudo
```

**Install other dependencies**
```
sudo apt update && sudo apt install -y python3 python3-venv python3-pip curl wget screen git lsof nano unzip iproute2
```

**Install Node.js and npm**
```
curl -sSL https://raw.githubusercontent.com/zunxbt/installation/main/node.sh | bash
```

**Create a screen session**
```
screen -S gensyn
```

**Run the swarm**
```
cd $HOME && rm -rf gensyn-testnet && git clone https://github.com/kotovinvest/gensyn_testnet.git && chmod +x gensyn_testnet/gensyn.sh && ./gensyn_testnet/gensyn.sh
```

During setup, it will ask:
```
Would you like to push models you train in the RL swarm to the Hugging Face Hub? [y/N]:
```
- Write: `N`

When you see the interface, detach from the screen session:
- Press `Ctrl + A` then `D`

## 🔄️ Back up swarm.pem

After running the Gensyn node, it is **essential** to back up the `swarm.pem` file from your server to your local PC. Losing this file will result in loss of your contribution.

There are two methods:

### Method 1 (Very Simple)

Ensure you are in `rl-swarm` folder, then run:
```
[ -f backup.sh ] && rm backup.sh; curl -sSL -O https://raw.githubusercontent.com/zunxbt/gensyn-testnet/main/backup.sh && chmod +x backup.sh && ./backup.sh
```

- 1️⃣ **VPS/GPU/WSL to PC**  
  Visit the provided URL and press `Ctrl + S` to save the files.

- 2️⃣ **One VPS/GPU/WSL to another VPS/GPU/WSL**  
  Use the commands shown on your destination server.

### Method 2 (Manual)

If automatic backup fails, refer to the manual guide (Click Here).

## 🟢 Node Status

### 1. Check Logs
Reconnect to your screen session:
```
screen -r gensyn
```
If everything is running fine, detach again with `Ctrl + A`, then `D`.

### 2. Check Wins
Visit: [https://gensyn-node.vercel.app/](https://gensyn-node.vercel.app/)

- Enter your Peer-ID from the logs.
- The more wins you have, the better.

> **Note**  
> If you see `0x0000000000000000000000000000000000000000` in Connected EOA Address, your contribution is not being recorded. In that case, delete `swarm.pem` and restart from scratch with a new email.

## ⚠️ Troubleshooting

### 🔴 Daemon failed to start in 15.0 seconds

If you encounter this issue:

1. Run:
```
nano $(python3 -c "import hivemind.p2p.p2p_daemon as m; print(m.__file__)")
```

2. Find the line:
```
startup_timeout: float = 15,
```
Change it to:
```
startup_timeout: float = 120,
```
3. Save the changes (`Ctrl + X`, then `Y`, then `Enter`).

4. Restart the swarm:
```
./run_rl_swarm.sh
```
