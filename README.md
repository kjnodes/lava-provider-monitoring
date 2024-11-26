<p align="center">
  <img height="75" height="auto" src="https://services.kjnodes.com/assets/images/logos/lava.png">
</p>

# Lava Provider Monitoring stack

This project provides a monitoring solution for Lava Provider using Prometheus, Grafana, and Alertmanager. The stack enables real-time data visualization, monitoring, and alerting for your node's health and performance.

## Installation

Follow these steps to install the necessary dependencies and deploy the monitoring stack.

### 1. Install Docker

Install Docker, a containerization platform required for running the monitoring services:

```bash
sudo apt-get update
sudo apt-get -y install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

sudo apt-get -y install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo systemctl enable docker.service containerd.service
sudo systemctl start docker.service containerd.service
```

### 2. Clone the Monitoring Stack Repository

Clone the repository that contains the monitoring stack configuration:

```bash
rm -rf $HOME/lava-provider-monitoring
git clone https://github.com/kjnodes/lava-provider-monitoring.git $HOME/lava-provider-monitoring
cd $HOME/lava-provider-monitoring
```

## Pre-Configuration

Before deploying the monitoring stack, configure Alerting and Prometheus settings.

### 1. Set Up Telegram Alerting

Configure Alertmanager to send notifications via Telegram. Update the `YOUR_TELEGRAM_BOT_TOKEN` and `YOUR_TELEGRAM_USER_ID` in the Alertmanager configuration file.

| KEY | VALUE |
|---------------|-------------|
| YOUR_TELEGRAM_USER_ID | Your Telegram user ID can be obtained from [@userinfobot](https://t.me/userinfobot). |
| YOUR_TELEGRAM_BOT_TOKEN | Get your bot token from [@botfather](https://telegram.me/botfather). Follow the steps outlined [here](https://core.telegram.org/bots#6-botfather) to create a new token. |

Edit the configuration file:

```bash
vim alertmanager/alertmanager.yml
```

Example configuration:

```yml
receivers:
  - name: 'telegram'
    telegram_configs:
      - send_resolved: true
        bot_token: '74064354354:AfeDFge7zdw-oJBOyf1CuEryo9gwpFfcw'
        chat_id: 442175262
        message: '{{ template "telegram.message" . }}'
```

### 2. Configure Prometheus

Set up Prometheus by specifying the `IP` address and `ports` for your node services. Modify the `NODE_IP` and `METRIC_PORT` in the configuration file:

```bash
vim prometheus/prometheus.yml
```

Example configuration:

```yml
scrape_configs:
  - job_name: prometheus
    metrics_path: /metrics
    static_configs:
      - targets:
          - localhost:9090
  - job_name: lava-provider
    metrics_path: /metrics
    static_configs:
      - targets:
          - 1.2.3.4:7780   # lava provider metrics endpoint
        labels:
          instance: mainnet
  - job_name: lava-provider-cache
    metrics_path: /metrics
    static_configs:
      - targets:
          - 1.2.3.4:7780   # lava provider cache metrics port
        labels:
          instance: mainnet
```

## Monitoring stack deployment

```bash
docker compose up -d
```

## Data Visualization Using Grafana

Follow these steps to access and use the Lava Provider Dashboard in Grafana:

1. Open Grafana in your web browser (default port: 9999).

2. Log in using the default credentials `admin/admin`, then set a new password.

3. Navigate to the `Dashboards` page to access the `Lava Provider Dashboard`.

## Dashboard contents

TBD

## Alerting and Notifications

Alertmanager triggers alerts and sends notifications via Telegram when configured conditions are met.

### Alerting Rules (Conditions)

1. Alert if instance is down.
2. Alert if provider is frozen.

Example of Telegram notification:

<div style="text-align: center;">
    <img src="images/telegram-alerts.png" alt="image" width="500" />
</div>

## Clean Up All Container Data

> **Warning:** This will remove all container monitoring stack data.

To stop and remove the monitoring stack and associated data, execute:

```
cd $HOME/lava-provider-monitoring
docker compose down --volumes
```

## Accessing the Monitoring Stack UI

You can access the monitoring tools using these ports:
- Prometheus: 9090
- Alertmanager: 9093
- Grafana: 9999
