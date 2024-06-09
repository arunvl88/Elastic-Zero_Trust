# Elastic Stack Installation and Configuration on Ubuntu VM

This guide provides step-by-step instructions to install and configure Elasticsearch and Kibana on an Ubuntu VM.

## Prerequisites

- An Ubuntu VM with root access
- Internet connectivity

## Steps

### 1. Add Elastic Stack GPG Key

Download and install the Elastic Stack GPG key:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg

```

### 2. Install `apt-transport-https`

Ensure that the `apt-transport-https` package is installed:

```bash
sudo apt-get install apt-transport-https

```

### 3. Add Elastic Stack Repository

Add the Elastic Stack repository to your `sources.list.d`:

```bash
echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list

```

### 4. Update and Install Elasticsearch

Update your package lists and install Elasticsearch:

```bash
sudo apt-get update && sudo apt-get install elasticsearch

```

### 5. Install Kibana

Install Kibana:

```bash
sudo apt-get install kibana

```

### 6. Enable Elasticsearch and Kibana Services

Reload the systemd daemon and enable Elasticsearch and Kibana services to start at boot:

```bash
sudo /bin/systemctl daemon-reload
sudo /bin/systemctl enable elasticsearch.service
sudo /bin/systemctl enable kibana.service

```

### 7. Start Elasticsearch and Kibana

Start the Elasticsearch and Kibana services:

```bash
sudo systemctl start elasticsearch.service
sudo systemctl start kibana.service

```

## Verification

### Elasticsearch

To verify that Elasticsearch is running, use the following command:

```bash
curl -X GET "localhost:9200/"

```

You should receive a JSON response containing information about your Elasticsearch cluster.

### Kibana

To verify that Kibana is running, open a web browser and navigate to:

```arduino
arduinoCopy code
http://localhost:5601

```

You should see the Kibana web interface.

## References

- [Elasticsearch Installation Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)
- [Kibana Installation Guide](https://www.elastic.co/guide/en/kibana/current/deb.html)
