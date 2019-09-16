# How To Install Promethues

## Install Prometheus and Grafana
### Prometheus
#### Step 1: Install Prometheus
  
``` 
sudo useradd --no-create-home --shell /usr/sbin/nologin prometheus
sudo apt-get update -y
sudo apt-get install -y prometheus prometheus-node-exporter prometheus-pushgateway prometheus-alertmanager
sudo systemctl start prometheus
sudo systemctl enable prometheus
```
  
#### Step 2: Install Grafana

``` 
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
curl https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana


sudo systemctl daemon-reload
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

> The configuration file localtion is /etc/grafana/grafana.ini 
> You can restart the server by following this command systemctl restart grafana-server 
> The default credential is admin/admin


#### Step 3: Install Node Exporter
```
sudo apt install prometheus-node-exporter
```

#### Step 4: add the target to collect the metrics
> Edit /etc/prometheus/prometheus.yml
```
scrape_configs:
  - job_name: node
    # If prometheus-node-exporter is installed, grab stats about the local
    # machine by default.
    static_configs:
      - targets: ['localhost:9100', 'new.node.com:9100']
```
```
sudo systemctl start prometheus-node-exporter.service
sudo systemctl enable prometheus-node-exporter.service

```


### If you want to install the latest version of exporter, following this

Download the file.
```
cd /usr/src/
 wget https://github.com/prometheus/node_exporter/releases/download/v0.18.1/node_exporter-0.18.1.linux-amd64.tar.gz
tar zxvf node_exporter-0.18.1.linux-amd64.tar.gz
```
Add below line to /etc/rc.local and also execute 

> /usr/src/node_exporter-0.18.1.linux-amd64/node_exporter &

Add this line to /etc/systemd/system/kubelet.service.d/10-kubeadm.conf  
> Environment="KUBELET_CGROUP_ARGS=--runtime-cgroups=/lib/systemd/system/kubelet.service --kubelet-cgroups=/lib/systemd/system/kubelet.service"

```
sudo systemctl restart kubelet
sudo systemctl restart docker
```
