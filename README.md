\# Docker \& Ansible Infrastructure with Nginx Reverse Proxy



\## Project Overview



This project demonstrates a containerized web infrastructure using Docker Compose, Nginx reverse proxy, round-robin load balancing, HTTPS, and Ansible automation.



\## Architecture



\- \*\*Backend 1:\*\* Nginx container serving a sample response.

\- \*\*Backend 2:\*\* Nginx container serving a separate sample response.

\- \*\*Reverse Proxy:\*\* Nginx distributes incoming requests between both backends.

\- \*\*HTTPS:\*\* Local self-signed TLS certificate for development testing.

\- \*\*Ansible Control:\*\* Runs Ansible commands and HTTP health checks against both backends.



\## Technologies



\- Docker and Docker Compose

\- Nginx

\- Ansible

\- TLS/HTTPS

\- YAML



\## Start the Project



Run these commands from the project root:



```bash

docker compose up -d

docker compose ps

```



\## Test Load Balancing



```bash

for /L %i in (1,1,10) do @curl -k -s -H "Connection: close" https://localhost:8443

```



The responses should include both backend messages.



\## Test HTTPS



```bash

curl -k -I https://localhost:8443

```



The `-k` option is for local testing with a self-signed certificate.



\## Test HTTP-to-HTTPS Redirect



```bash

curl -I http://localhost:8080

```



Expected status: `301 Moved Permanently`.



\## Run the Ansible Playbook



```bash

docker exec -it ansible-control ansible-playbook -i /ansible/inventory/hosts.ini /ansible/check-backends.yml

```



The playbook checks HTTP responses from both backend services.



\## Health Endpoint



Open or test:



```bash

curl -k https://localhost:8443/health

```



Expected response:



```text

Reverse proxy is healthy

```



\## Important Notes



\- The self-signed certificate is for local development and testing only.

\- HTTPS uses host port `8443`; HTTP uses host port `8080`.

\- Backend ports are not published directly to the host.

\- The current Ansible inventory uses local connections inside the Ansible control container. It does not remotely configure the backend containers.



\## Stop the Project



bash

docker compose down





