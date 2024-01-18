# Install updates and dependencies
```
sudo apt-get update
sudo apt install jq python3-pip -y
```

# Install docker
```
if ! command -v docker &> /dev/null
then
    echo -e "\e[1m\e[32m3.1 Installing Docker... \e[0m" && sleep 1
    sudo apt-get install ca-certificates curl gnupg lsb-release wget -y
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
    sudo chmod a+r /usr/share/keyrings/docker-archive-keyring.gpg
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
    sudo apt-get update
    sudo apt-get install docker-ce docker-ce-cli containerd.io -y
fi
```

# Install docker compose
```
docker-compose version
if [ $? -ne 0 ]
then
    echo -e "\e[1m\e[32m4.1 Installing Docker Compose... \e[0m" && sleep 1
	docker_compose_version=$(wget -qO- https://api.github.com/repos/docker/compose/releases/latest | jq -r ".tag_name")
	sudo wget -O /usr/bin/docker-compose "https://github.com/docker/compose/releases/download/${docker_compose_version}/docker-compose-`uname -s`-`uname -m`"
	sudo chmod +x /usr/bin/docker-compose
fi
```

# Clone repository
```
cd $HOME && rm -rf lava-provider-monitoring
git clone https://github.com/kj89/lava-provider-monitoring.git
```
