# Docker \& Ansible Infrastructure with Nginx Reverse Proxy



A hands-on DevOps project demonstrating containerized infrastructure, SSH-based Ansible automation, HTTPS reverse proxying, load balancing, backend failover, and firewall rules.



## Project Overview



This project simulates a small multi-server web infrastructure using Docker Compose. An Nginx reverse proxy distributes incoming HTTPS requests across two backend web servers. Ansible connects to the backends over SSH to verify their health and apply basic SSH hardening.



## Architecture



\- \*\*Ansible Control Node:\*\* Runs Ansible playbooks and manages backend servers over SSH.

\- \*\*Backend 1:\*\* Nginx web server returning a sample response.

\- \*\*Backend 2:\*\* Nginx web server returning a different sample response.

\- \*\*Reverse Proxy:\*\* Nginx handles HTTP-to-HTTPS redirection and distributes requests across the backends.

\- \*\*Firewall:\*\* iptables rules restrict inbound proxy-container traffic.

\- \*\*TLS:\*\* A self-signed certificate enables HTTPS for local development.



### Request Flow



`Client → HTTP (8080) → HTTPS (8443) → Nginx Reverse Proxy → Backend 1 / Backend 2`



Ansible separately connects to both backend containers over SSH for administration and health checks.



## Technologies Used



\- Docker and Docker Compose

\- Linux

\- Ansible and YAML

\- Nginx reverse proxy

\- SSH key-based authentication

\- HTTPS/TLS

\- iptables firewall

\- Git and GitHub



## Prerequisites



Install Docker Desktop with Docker Compose and Git. Run the following commands from the project root directory.



## Start the Project



```bash

docker compose up -d --build

docker compose ps

```



Check the containers:



```bash

docker logs reverse-proxy

docker exec reverse-proxy nginx -t

```



## Test HTTPS



```bash

curl -k -I https://localhost:8443

```



Expected result: `HTTP/1.1 200 OK`.



The `-k` option allows curl to test the locally generated self-signed certificate without normal certificate verification.



## Test HTTP-to-HTTPS Redirect



```bash

curl -I http://localhost:8080

```



Expected result: `301 Moved Permanently`, redirecting to HTTPS on port `8443`.



## Test Load Balancing (Windows CMD)



Run this command in Windows CMD:



```cmd

for /L %i in (1,1,10) do @curl -k -s -H "Connection: close" https://localhost:8443

```



The responses should include messages from both backend servers. The exact distribution may vary.



## Test Backend Failover



Stop Backend 1:



```bash

docker compose stop backend1

```



Send a request through the HTTPS reverse proxy:



```bash

curl -k https://localhost:8443

```



With the Nginx upstream configuration and failure handling working correctly, Backend 2 should continue serving requests.



Restart Backend 1:



```bash

docker compose start backend1

```



## Run Ansible Health Checks



The playbook checks that both backends return HTTP 200.



```bash

docker exec ansible-control sh -c "ANSIBLE\_CONFIG=/tmp/ansible.cfg ansible-playbook -i /ansible/inventory/hosts.ini /ansible/check-backends.yml"

```



The temporary Ansible configuration disables host-key checking for this local lab. This is a convenience for testing, not a recommended production security setting.



## SSH Hardening



The Ansible backend role configures:



\- Root SSH login disabled.

\- Password-based SSH authentication disabled.

\- SSH configuration syntax validation.

\- SSH service reload through the playbook handler.

\- Backend HTTP health checks.



The current lab uses SSH key-based authentication from the Ansible control container.



## Health Endpoint



Test the reverse proxy health endpoint:



```bash

curl -k https://localhost:8443/health

```



Expected response:



```text

Reverse proxy is healthy

```



## Firewall Verification



Inspect the reverse proxy's inbound IPv4 rules:



```bash

docker exec reverse-proxy iptables -L INPUT -n -v

```



The current rules allow loopback traffic, established/related connections, and inbound TCP ports 80 and 443 inside the proxy container. Other inbound IPv4 traffic is dropped by the INPUT policy.



The proxy container does not currently allow inbound SSH on port 22. Host ports `8080` and `8443` map to container ports `80` and `443`, respectively.



## Security Notes



\- The self-signed TLS certificate is for local testing only.

\- Do not commit private SSH keys, TLS private keys, credentials, or local backup files.

\- Host-key checking is disabled for this lab's Ansible test configuration; production environments should validate SSH host keys.

\- The firewall configuration is a learning example and should be reviewed before production use.

\- Backend ports are not published directly to the host in the current Compose setup.



## Stop the Project



```bash

docker compose down

```



## Learning Outcomes



\- Deploy a multi-container application with Docker Compose.

\- Configure an Nginx reverse proxy and HTTPS.

\- Demonstrate load balancing and backend failover.

\- Automate backend configuration and health checks using Ansible over SSH.

\- Apply basic SSH hardening and inspect iptables firewall rules.

\- Manage project changes with Git and GitHub.



## Author



Rohit Kharat



GitHub: \[docker-ansible-nginx](https://github.com/rohitKharat161/docker-ansible-nginx)



