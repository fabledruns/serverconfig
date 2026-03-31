# Build a Better Minecraft Server

This is the repository for the "Build a Better Minecraft Server" video. Use the docker-compose files
and .env files for your server setup for free, for both commercial and non-commercial use.

Minecraft Server Docker Compose file location: `./minecraftserver/docker-compose.yml`
Pelican Docker Compose file location: `./minecraftserver/pelican/docker-compose.yml`
Pelican Environment (.env) file location: `./minecraftserver/pelican/.env`

---

## Setup Guide

### 1. Update system

    sudo apt update && sudo apt upgrade -y

### 2. Install Docker

    sudo apt install -y ca-certificates curl gnupg

    sudo install -m 0755 -d /etc/apt/keyrings

    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

    echo \
    "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    $(. /etc/os-release && echo $VERSION_CODENAME) stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt update

    sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

### 3. Enable Docker

    sudo systemctl enable docker
    sudo systemctl start docker

Optional - run Docker without sudo (takes effect after re-login):

    sudo usermod -aG docker $USER
    newgrp docker

### 4. Fix Docker DNS

    sudo nano /etc/docker/daemon.json

Paste the following:

    {
      "dns": ["1.1.1.1", "8.8.8.8"]
    }

Then restart Docker:

    sudo systemctl restart docker

### 5. Clone the repo

    git clone https://github.com/fabledruns/serverconfig.git root
    cd ./root/minecraftserver/pelican

### 6. Set up the Pelican environment

    nano .env

At minimum, update these two values:

    APP_URL=http://YOUR_SERVER_IP
    DB_PASSWORD=your_strong_password

### 7. Start the Pelican panel

    docker compose up -d

### 8. Open the panel

Go to:

    http://YOUR_SERVER_IP

Create your admin account, then create a node.

### 9. Install Wings

    sudo mkdir -p /etc/pterodactyl
    cd /etc/pterodactyl

    sudo curl -L https://github.com/pterodactyl/wings/releases/latest/download/wings_linux_amd64 -o wings

    sudo chmod +x wings

### 10. Add Wings config

In the panel, go to your node and copy the config. Then:

    sudo nano /etc/pterodactyl/config.yml

Paste the config and save.

### 11. Run Wings

    sudo /etc/pterodactyl/wings

To run it in the background:

    sudo nohup /etc/pterodactyl/wings &

### 12. Create a Minecraft server

In the panel:

- Click "Create Server"
- Select the Purpur egg
- Set version to 1.21.1
- Allocate at least 2GB RAM

### 13. Start the server

Click Start in the panel. When the server is ready, you will see:

    Done (X.Xs)! For help, type "help"

---

## Useful Docker Commands

    docker compose stop                          # stop the server
    docker compose start                         # start it again
    docker compose down                          # stop and remove container (data stays in ./data)
    docker exec -it minecraft rcon-cli           # open interactive console via RCON

---

## Troubleshooting

| Problem                  | Fix                                      |
|--------------------------|------------------------------------------|
| Jar error on start       | Wrong egg selected or jar file missing   |
| Mojang download fails    | Check version string or DNS config       |
| Node shows as offline    | Wings config is wrong or Wings not running |

To test DNS inside Docker:

    docker run --rm alpine ping -c 3 piston-data.mojang.com
