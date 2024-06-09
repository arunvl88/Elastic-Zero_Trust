## Introduction

This documentation provides a comprehensive guide to installing and configuring the Elastic Stack, which includes Elasticsearch and Kibana, on an Ubuntu VM. The Elastic Stack is a powerful suite of tools for searching, analyzing, and visualizing data in real-time, making it ideal for various applications, including monitoring, security analytics, and operational intelligence.

In this guide, we will cover the following:

1. **Installation of Elasticsearch and Kibana**: Step-by-step instructions to set up and configure Elasticsearch and Kibana on an Ubuntu VM.
2. **Integration with Cloudflare Log Push**: Instructions on how to integrate Cloudflare Log Push to send logs directly to your Elasticsearch instance, enabling you to monitor and analyze traffic and security data from Cloudflare.
3. **Sending Cloudflare Zero Trust Logs to Elastic Agent Endpoint**: Detailed steps to configure Cloudflare Zero Trust logs and send them to the Elastic Agent endpoint for centralized log management and advanced analytics.

   
## Elastic Stack Installation and Configuration on Ubuntu VM

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
http://localhost:5601

```

You should see the Kibana web interface.

- If not verify the following ports are open
    
    **Kibana:** **5601**
    
    **Elasticsearch:** **9200** (HTTP communication) and **9300** (internal node communication)
    
    ```arduino
    netstat -tnlp
    (Not all processes could be identified, non-owned process info
    will not be shown, you would have to be root to see it all.)
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
    
    tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
    
    tcp        0      0 127.0.0.1:5601          0.0.0.0:*               LISTEN      -
    
    tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -
    
    tcp6       0      0 :::9200                 :::*                    LISTEN      -
    
    tcp6       0      0 :::22                   :::*                    LISTEN      -
    
    tcp6       0      0 127.0.0.1:9300          :::*                    LISTEN      -
    
    tcp6       0      0 ::1:9300                :::*                    LISTEN      -  Ports
    ```
    
- Allow remote users to access Kibana
    
    Here's how you can do it:
    
    1. Open the Kibana configuration file (**`kibana.yml`**) using a text editor. Typically, it's located in **`/etc/kibana/`**.
    2. Look for the **`server.host`** option in the **`kibana.yml`** file.
    3. Set the **`server.host`** option to either the external IP address of the VM (**`10.0.0.208`**) or **`0.0.0.0`** to bind to all available network interfaces.
        
        
        ```arduino
        server.host: "0.0.0.0"
        ```
        
    4. Save the changes to the **`kibana.yml`** file.
    5. Restart the Kibana service to apply the changes:
        
        ```
        sudo systemctl restart kibana
        
        ```
        
    
    https://www.elastic.co/guide/en/kibana/current/settings.html

### Initial login to the Elastic dashboard

  - When you initially login to Elastic dashboard, you will see the following:
 
<img width="791" alt="image" src="https://github.com/arunvl88/Elastic-Zero_Trust/assets/7003647/82a80192-e801-49a3-8d50-e162815ac0e6">

One method to obtain enrollment token is from the CLI:

`/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token --scope kibana`

Once entering the enrollment token, you will be asked to enter the username and password.
Deafult username and password was not working for me.

<img width="747" alt="image" src="https://github.com/arunvl88/Elastic-Zero_Trust/assets/7003647/736d618c-8752-4530-a49d-d875dade013f">

However, I had to reset the password as I don’t remeber if the password was set during the setup:

navigate to: `cd /usr/share/elasticsearch/bin/`

`/usr/share/elasticsearch/bin# ./elasticsearch-reset-password -u elastic`

## References

- [Elasticsearch Installation Guide](https://www.elastic.co/guide/en/elasticsearch/reference/current/deb.html)
- [Kibana Installation Guide](https://www.elastic.co/guide/en/kibana/current/deb.html)

### **Enabling the Cloudflare Log Push integration in Elastic**

The Cloudflare Logpush integration can be used in three different modes to collect data:

- HTTP Endpoint mode - Cloudflare pushes logs directly to an HTTP endpoint hosted by your Elastic Agent.
- AWS S3 polling mode - Cloudflare writes data to S3 and Elastic Agent polls the S3 bucket by listing its contents and reading new files.
- AWS S3 SQS mode - Cloudflare writes data to S3, S3 pushes a new object notification to SQS, Elastic Agent receives the notification from SQS, and then reads the S3 object. Multiple Agents can be used in this mode.

1. In Kibana, go to Management > Integrations
2. In the integrations search bar type **Cloudflare Logpush**.
3. Click the **Cloudflare Logpush** integration from the search results.
4. Click the **Add Cloudflare Logpush** button to add Cloudflare Logpush integration.
5. Enable the Integration with the HTTP Endpoint, AWS S3 input or GCS input. I chose 'HTTP endpoint'

<img width="1348" alt="image" src="https://github.com/arunvl88/Elastic-Zero_Trust/assets/7003647/887f32c9-4269-4c05-9b19-a07fe6a49399">

6. Click 'save and continue'
7. Click 'Add Elastic Agent to your hosts' and chose 'Run standalone'

<img width="1709" alt="image" src="https://github.com/arunvl88/Elastic-Zero_Trust/assets/7003647/39f148a8-fe2a-4238-bcaf-e50d2a8b3535">

8. Copy the elastic-agent.yml file (Make sure to Modify `ES_USERNAME` and `ES_PASSWORD` in the outputs section of elastic-agent.yml to use your Elasticsearch credentials.)
9. This page will also give you commands to install elastic agent on your host > copy them and run them on your host machine:

<img width="1703" alt="image" src="https://github.com/arunvl88/Elastic-Zero_Trust/assets/7003647/955ed008-bc9a-4fb2-bcd6-8c5a2339744f">

```
curl -L -O https://artifacts.elastic.co/downloads/beats/elastic-agent/elastic-agent-8.13.4-linux-x86_64.tar.gz
tar xzvf elastic-agent-8.13.4-linux-x86_64.tar.gz
cd elastic-agent-8.13.4-linux-x86_64
sudo ./elastic-agent install
```


10. Next step: Configure Cloudflare to send logs to the Elastic Agent.

### **To collect data from the Cloudflare HTTP Endpoint, follow the below steps:**

- Reference link to [**Enable HTTP destination**](https://developers.cloudflare.com/logs/get-started/enable-destinations/http/) for Cloudflare Logpush.
- Add same custom header along with its value on both the side for additional security.
- For example, while creating a job along with a header and value for a particular dataset:

```arduino
curl --location \
--request POST \
'https://api.cloudflare.com/client/v4/accounts/<account_ID>/logpush/jobs' \
--header 'X-Auth-Key: <Auth_key>'  \
--header 'X-Auth-Email: <Email_address>'  \
--header 'Content-Type: application/json'  \
--data-raw '{
    "name": "<example.com>",
    "destination_conf": "https://<example.com>/cloudflare_logpush/workers_trace?header_Content-Type=application/json",
    "logpull_options": "fields=RayID,EdgeStartTimestamp&timestamps=rfc3339"
}'
```

**Note**:

- The destination_conf parameter inside the request data should set the Content-Type header to **`application/json`**. This is the content type that the HTTP endpoint expects for incoming events.
- Default port for the HTTP Endpoint is *9560*.
- When using the same port for more than one dataset, be sure to specify different dataset paths.
- Example: `https://logs.example.com?header_Authorization=Basic%20REDACTED&tags=host:theburritobot.com,dataset:http_requests`
