# Practical Guide: IDS with Snort and Docker

Este guia descreve de forma prática como configurar e testar um ambiente Docker com Snort (IDS), um servidor web (Nginx) e um container atacante (Netshoot). Inclui explicações em **inglês** e **português**.

---

## 1. Quick Introduction to Snort / Introdução Rápida ao Snort

**English:**  
Snort is an open-source Intrusion Detection System (IDS) that inspects network traffic in real time against a set of signature rules. It captures packets on a network interface, applies rules to detect suspicious patterns, and generates alerts when matches occur.

**Português:**  
O Snort é um sistema de detecção de intrusos (IDS) open-source que monitora tráfego de rede em tempo real utilizando regras de assinaturas. Ele captura pacotes em uma interface de rede, aplica regras para identificar padrões suspeitos e gera alertas quando encontra correspondências.

---

## 2. Docker Lab Overview / Visão Geral do Ambiente Docker

**English:**  
We set up a small Docker “lab” with three containers, all connected to a custom Docker network called `lab-net`:

- **snort**: Runs Snort in IDS mode, listening on `eth0` and applying rule sets.
- **victim**: Runs Nginx (web server) to generate HTTP traffic and act as a target.
- **attacker**: Runs Netshoot (a diagnostic image with tools like ping, nmap, curl, tcpdump) to simulate attacks.

**Português:**  
Criamos um pequeno “laboratório” Docker com três containers, todos conectados a uma rede Docker personalizada chamada `lab-net`:

- **snort**: Executa o Snort em modo IDS, escutando na interface `eth0` e aplicando conjuntos de regras.
- **victim**: Executa o Nginx (servidor web) para gerar tráfego HTTP e servir como alvo.
- **attacker**: Executa o Netshoot (imagem diagnostic-tools que inclui ping, nmap, curl, tcpdump) para simular ataques.

---

## 3. Why Each Docker Image Was Chosen / Por que cada imagem Docker foi escolhida?

**English:**

1. **snort (`frapsoft/snort:latest`)**

   - Pre-built Snort image ready for IDS mode. Avoids manual installation of dependencies and ensures a stable, up-to-date version.

2. **victim (`nginx:alpine`)**

   - Lightweight Nginx web server (Alpine variant) to simulate HTTP traffic. Reduced image size and resource usage, ideal for a simple web target.

3. **attacker (`nicolaka/netshoot:latest`)**
   - Netshoot is an Alpine-based image that bundles common network tools (ping, nmap, curl, tcpdump, etc.). Facilitates simulating malicious or anomalous traffic without installing extra packages.

**Português:**

1. **snort (`frapsoft/snort:latest`)**

   - Imagem pré-construída do Snort pronta para uso em modo IDS. Evita instalar manualmente dependências e garante uma versão estável e atualizada.

2. **victim (`nginx:alpine`)**

   - Servidor web Nginx leve na variante Alpine, para simular tráfego HTTP. Imagem pequena e com baixo consumo de recursos, ideal para um alvo web simples.

3. **attacker (`nicolaka/netshoot:latest`)**
   - O Netshoot é uma imagem baseada em Alpine que inclui diversas ferramentas de rede (ping, nmap, curl, tcpdump, etc.). Facilita a simulação de tráfego malicioso ou anômalo sem instalar pacotes adicionais.

---

## 4. Docker Network Setup / Configuração da Rede Docker

**English:**  
We encountered a connectivity issue initially: Snort couldn't capture packets because containers were on different networks. We resolved this by creating a custom Docker bridge network named `lab-net`. All services connect to this network, allowing Snort to capture traffic between `victim` and `attacker`.

```yaml
networks:
  lab-net:
    driver: bridge

services:
  snort:
    networks:
      - lab-net

  victim:
    networks:
      - lab-net

  attacker:
    networks:
      - lab-net
```

Optionally, assign fixed IPs for clarity:

```yaml
services:
  snort:
    networks:
      lab-net:
        ipv4_address: 172.20.0.10

  victim:
    networks:
      lab-net:
        ipv4_address: 172.20.0.20

  attacker:
    networks:
      lab-net:
        ipv4_address: 172.20.0.30
```

**Português:**  
Encontramos um problema de conectividade: o Snort não capturava pacotes porque os containers estavam em redes diferentes. Resolvemos criando uma rede Docker personalizada do tipo **bridge** chamada `lab-net`. Todos os serviços se conectam a essa rede, permitindo que o Snort capture o tráfego entre `victim` e `attacker`.

Opcionalmente, atribua IPs fixos para facilitar a visualização:

```yaml
services:
  snort:
    networks:
      lab-net:
        ipv4_address: 172.20.0.10

  victim:
    networks:
      lab-net:
        ipv4_address: 172.20.0.20

  attacker:
    networks:
      lab-net:
        ipv4_address: 172.20.0.30
```

---

## 5. Folder Structure / Estrutura de Pastas

```text
ids-labs/
├── docker-compose.yml
└── snort-lab/
    ├── snort.conf
    ├── rules/
    │   └── local.rules
    └── logs/
```

---

## 6. Docker Compose Configuration / Configuração do Docker Compose

**English:**  
Below is a sample `docker-compose.yml` that sets up the network, mounts volumes, and runs each container with appropriate commands:

```yaml
version: "3.8"

networks:
  lab-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16

services:
  snort:
    image: frapsoft/snort:latest
    container_name: snort-ids
    networks:
      lab-net:
        ipv4_address: 172.20.0.10
    volumes:
      - ./snort-lab/snort.conf:/etc/snort/snort.conf:ro
      - ./snort-lab/rules:/etc/snort/rules:ro
      - ./snort-lab/logs:/var/log/snort
    command: >
      snort -c /etc/snort/snort.conf 
      -i eth0 -A console -l /var/log/snort
    restart: unless-stopped

  victim:
    image: nginx:alpine
    container_name: victim-server
    networks:
      lab-net:
        ipv4_address: 172.20.0.20
    ports:
      - "8080:80"
    restart: unless-stopped

  attacker:
    image: nicolaka/netshoot:latest
    container_name: attacker-machine
    networks:
      lab-net:
        ipv4_address: 172.20.0.30
    tty: true
    stdin_open: true
    restart: unless-stopped
```

**Português:**  
Exemplo de `docker-compose.yml` que configura a rede, mapeia volumes e executa cada container com os comandos corretos:

```yaml
version: "3.8"

networks:
  lab-net:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16

services:
  snort:
    image: frapsoft/snort:latest
    container_name: snort-ids
    networks:
      lab-net:
        ipv4_address: 172.20.0.10
    volumes:
      - ./snort-lab/snort.conf:/etc/snort/snort.conf:ro
      - ./snort-lab/rules:/etc/snort/rules:ro
      - ./snort-lab/logs:/var/log/snort
    command: >
      snort -c /etc/snort/snort.conf 
      -i eth0 -A console -l /var/log/snort
    restart: unless-stopped

  victim:
    image: nginx:alpine
    container_name: victim-server
    networks:
      lab-net:
        ipv4_address: 172.20.0.20
    ports:
      - "8080:80"
    restart: unless-stopped

  attacker:
    image: nicolaka/netshoot:latest
    container_name: attacker-machine
    networks:
      lab-net:
        ipv4_address: 172.20.0.30
    tty: true
    stdin_open: true
    restart: unless-stopped
```

---

## 7. Where Are Snort Rules Defined? / Onde Ficam as Regras do Snort?

**English:**  
Custom rules are defined in `snort-lab/rules/local.rules`. Example to detect a port scan:

```rules
# Snort rules - local.rules

# Detect port scan
alert tcp any any -> any any (msg:"Port Scan Detected"; flags:S; detection_filter:track by_src, count 10, seconds 5; sid:1000001; rev:1;)

# Detect SQL Injection attempts
alert tcp any any -> any 80 (msg:"HTTP SQL Injection Attempt"; content:"union"; nocase; content:"select"; nocase; sid:1000002; rev:1;)
```

**Português:**  
As regras personalizadas ficam em `snort-lab/rules/local.rules`. Exemplo para detectar port scan:

```rules
# Regras Snort - local.rules

# Detecta port scan
alert tcp any any -> any any (msg:"Port Scan Detected"; flags:S; detection_filter:track by_src, count 10, seconds 5; sid:1000001; rev:1;)

# Detecta tentativas de SQL Injection
alert tcp any any -> any 80 (msg:"HTTP SQL Injection Attempt"; content:"union"; nocase; content:"select"; nocase; sid:1000002; rev:1;)
```

---

## 8. How to Run the Environment / Como Rodar o Ambiente

**English:**

```bash
# 1. Change to project directory
cd ids-labs

# 2. (Optional) Clean up previous containers
docker-compose down --volumes
docker system prune -f

# 3. Start containers
docker-compose up -d

# 4. Check container status
docker-compose ps
```

Make sure all containers show “Up” on the `lab-net` network.

**Português:**

```bash
# 1. Acessar diretório do projeto
cd ids-labs

# 2. (Opcional) Limpar containers anteriores
docker-compose down --volumes
docker system prune -f

# 3. Iniciar containers
docker-compose up -d

# 4. Verificar status dos containers
docker-compose ps
```

Certifique-se de que todos os containers estejam “Up” na rede `lab-net`.

---

## 9. How to Test the IDS / Como Testar o IDS

**English:**  
To simulate attacks, enter the `attacker-machine` container:

```bash
docker exec -it attacker-machine bash
```

**Note:** You can use the container name (e.g., `victim-server`) instead of an IP. If you prefer the IP, run:

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}} container_name'
```

This returns the container’s IP. If fixed IPs were assigned (e.g., `172.20.0.20`), use that.

Once inside, run tests:

1. **Port Scanning:**

   ```bash
   nmap -sS -p 1-1000 172.20.0.20    # Stealth SYN scan
   nmap -sT 172.20.0.20             # Full TCP connect
   ```

2. **HTTP Attack (SQL Injection):**

   ```bash
   curl "http://172.20.0.20/?id=1' UNION SELECT * FROM users--"
   ```

3. **ICMP Flood:**
   ```bash
   ping -c 15 -i 0.1 172.20.0.20
   ```

View real-time alerts:

```bash
docker-compose logs -f snort | grep "[**]"
```

**Português:**  
Para simular ataques, entre no container `attacker-machine`:

```bash
docker exec -it attacker-machine bash
```

**Observação:** Você pode usar o nome do container (por exemplo, `victim-server`) em vez do IP. Se preferir usar o IP, execute:

```bash
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}} nome_do_container'
```

Isso retorna o IP do container. Se você definiu IP fixo (ex.: `172.20.0.20`), use-o diretamente.

Dentro do `attacker-machine`, execute:

1. **Varredura de Portas:**

   ```bash
   nmap -sS -p 1-1000 172.20.0.20    # SYN stealth scan
   nmap -sT 172.20.0.20             # Conexão TCP completa
   ```

2. **Ataque HTTP (SQL Injection):**

   ```bash
   curl "http://172.20.0.20/?id=1' UNION SELECT * FROM users--"
   ```

3. **Flood ICMP:**
   ```bash
   ping -c 15 -i 0.1 172.20.0.20
   ```

Para ver alertas em tempo real:

```bash
docker-compose logs -f snort | grep "[**]"
```

---

## 10. Compatibility: Windows & Linux / Compatibilidade: Windows & Linux

**English:**  
This container-based environment works on both Windows and Linux because Docker abstracts the underlying OS and uses a Linux-based engine to run containers. On Windows, Docker Desktop runs a lightweight Linux VM behind the scenes, meaning the same Docker Compose file and container images work identically across platforms.

**Português:**  
Este ambiente baseado em containers funciona tanto no Windows quanto no Linux, pois o Docker abstrai o sistema operacional e utiliza um mecanismo Linux para executar containers. No Windows, o Docker Desktop executa uma VM Linux nos bastidores, portanto, o mesmo arquivo Docker Compose e as mesmas imagens funcionam igualmente em ambas as plataformas.

---

## 11. Final Considerations / Considerações Finais

**English:**  
By creating the custom Docker network (`lab-net`) and choosing appropriate images (Snort, Nginx/Alpine, Netshoot), we ensured full visibility of container traffic and real-time attack detection. This lab demonstrates how to quickly deploy an IDS in containers, facilitating security testing and analysis.

**Português:**  
Ao criar a rede Docker personalizada (`lab-net`) e selecionar imagens adequadas (Snort, Nginx/Alpine, Netshoot), garantimos visibilidade completa do tráfego entre containers e detecção de ataques em tempo real. Este laboratório demonstra como implantar rapidamente um IDS em containers, facilitando testes e análises de segurança.
