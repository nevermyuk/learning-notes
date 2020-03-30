# Monitoring

### Common Monitoring Packages

- cAdvisor
- CA Associates
- Check_MK
- Collectd
- ConnectWise
- CloudWatch
- DataDog
- DynaTrace
- Grafana
- Icinga
- ManageEngine
- nagios
- OP5
- PRTG Network Mon
- Science Logic
- Sensu
- SignalFX
- SpiceWorks
- Solar winds
- Sysdig
- trace
- WhatsUp Gold
- Zabbix 
- ZipKin

## Prometheus

[How to use Prometheus Node Exporter](https://devopscube.com/monitor-linux-servers-prometheus-node-exporter/)

[Main Tutorial](https://prometheus.io/docs/guides/node-exporter/)

#### Steps

- Install Ansible
- Git clone Ansible-prometheus
- Install pip and tox
- Setup role
- Create inventory file
- Create main.yaml playbook
- Run playbook
- Install node exporter and configure
- Check web interface

[Step 1](https://www.youtube.com/watch?time_continue=266&v=fThV6by52yc&feature=emb_logo)

[Step 2](https://www.youtube.com/watch?time_continue=335&v=oOocpSNAQA0&feature=emb_logo)

## Graphing

### Grafana

#### Installing [Grafana](https://www.youtube.com/watch?v=XqLg4nWX_7g)

```bash
curl -LO https://s3-us-west-2.amazonaws.com/grafana-releases/release/grafana_5.1.4_amd64.deb ;
sudo apt-get install -y adduser libfontconfig ;
sudo dpkg -i grafana_5.1.4_amd64.deb ;
sudo systemctl start grafana-server ;
sudo systemctl enable grafana-server
```





### ELK - Elastic Stack

### ELK Represents:

- **Elasticsearch**: Log aggregator
- **Logstash**: Agent to send logs into Elasticsearch
- **Kibana**: GUI web interface to search logs



[Step 1](https://www.youtube.com/watch?v=xY1EFNthC04)



### ELK Stack Quickstart

- Clone this Github repo: https://github.com/elastic/ansible-elasticsearch
- Create inventory file
- Create main.yaml playbook
- Run the playbook
- Configure Elasticsearch password for user “elastic” (default):
  - /usr/share/elasticsearch/bin/elasticsearch-keystore add "bootstrap.password"

#### Kibana

[Official Tutorial to Install Kibana](https://www.elastic.co/guide/en/kibana/current/install.html)

[Step 2](https://www.youtube.com/watch?time_continue=11&v=5CaciBLvtJ0&feature=emb_logo)

```bash
Install Kibana ' apt install kibana'
Install Nginx ' apt install nginx'
Configure Nginx as a proxy to Kibana
echo "kibanaadmin:`openssl passwd -apr1`" | sudo tee -a /etc/nginx/htpasswd.users
Configure Elasticsearch connection string in Kibana

```





#### Configure Logstash

[Step 3](https://www.youtube.com/watch?v=U0un-m-8Wk4)

Sending logs to Elasticsearch

- Login to Kibana
- Install Logstash
- Configure connection string to Elasticsearch
- Check Kibana for logs







## Practise

### Pro Tips on Using the ELK Stack

**The ELK stack requires more memory than everything else in this course.**

- The standard config recommends 2GB. This is why we migrate to a larger instance.
- It may also be necessary to stop anything else that you have set up in this course (i.e. Jenkins, Prometheus, and Grafana).
- If the Ansible installation script for Elasticsearch takes too long (more than 5 minutes for a single item) you may have run out of memory.
- Do not be alarmed, just restart the EC2 instance either through the command line, or if necessary in the AWS web console.



### Further Reading on Monitoring

To learn how to set up alerts and notifications with ELK stack, please refer to the following:

- https://www.elastic.co/what-is/elasticsearch-alerting

### Exercise 1

- Migrate your AWS instance to a T2.medium
- [Get started with Prometheus](https://prometheus.io/docs/prometheus/latest/getting_started/) and [Deploy Prometheus monitoring system](https://github.com/cloudalchemy/ansible-prometheus)
- [Download](https://prometheus.io/download/#node_exporter) and [configure a node exporter](https://prometheus.io/docs/guides/node-exporter/)
- [Download and install Grafana](https://grafana.com/grafana/download) and configure it to [connect to your Prometheus backend](https://prometheus.io/docs/visualization/grafana/)

###  Exercise 2

- Set up ELK stack and all of its components (note: upgrade the version of Elasticsearch in Ansible, or do an apt install logstash to match those for Logstash and Kibana) https://github.com/elastic/ansible-elasticsearch

  