# Observability

## Prometheus + Grafana on Amazon EC2

### Connect to EC2
```bash
ssh -i <key.pem> ubuntu@<EC2-IP>
```

### Install Node Exporter

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.10.2/node_exporter-1.10.2.linux-amd64.tar.gz
```

Installation Steps:

```bash
tar -xvf node_exporter-1.10.2.linux-amd64.tar.gz
cd node_exporter-1.10.2.linux-amd64
./node_exporter &
```

Verification

```bash
http://<EC2-IP>:9100/metrics
```

### Install Prometheus


Same GitHub redirect issue

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.53.0/prometheus-2.53.0.linux-amd64.tar.gz
```

### Installation Steps
```bash
tar -xvf prometheus-2.53.0.linux-amd64.tar.gz
cd prometheus-2.53.0.linux-amd64
```
Configure Prometheus
```bash
nano prometheus.yml
```
Edit the file with this:
```bash
global:
  scrape_interval: 15s

scrape_configs:
  - job_name: "node_exporter"
    static_configs:
      - targets: ["localhost:9100"]
```
Start Prometheus
```bash
./prometheus --config.file=prometheus.yml &
```
Verification: `http://<EC2-IP>:9090`

Check: Status → Targets → UP

### Install Grafana
```bash
sudo rm -f /etc/apt/sources.list.d/*.list

sudo apt-get install -y apt-transport-https wget gnupg

sudo mkdir -p /etc/apt/keyrings
sudo wget -O /etc/apt/keyrings/grafana.asc https://apt.grafana.com/gpg-full.key
sudo chmod 644 /etc/apt/keyrings/grafana.asc

echo "deb [signed-by=/etc/apt/keyrings/grafana.asc] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list

sudo apt-get update
sudo apt-get install -y grafana
```

Start Grafana
```bash
sudo systemctl daemon-reload
sudo systemctl enable grafana-server
sudo systemctl start grafana-server
```
Access: `http://<EC2-IP>:3000`

Default:

Username: admin
Password: admin

If you are running on EC2 then you need to point out the IP of the EC2:
```bash
sudo nano /etc/grafana/grafana.ini
```

```bash
[server]
domain = <EC2-IP>
root_url = http://<EC2-IP>:3000/
serve_from_sub_path = false
```
Restart
```bash
sudo systemctl restart grafana-server
```
### endpoints 

prom- http://3.110.175.149:9090/graph
grphana - http://3.110.175.149:3000/admin
nodeexp - http://3.110.175.149:9100/metrics
