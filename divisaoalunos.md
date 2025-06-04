# Divisão de Tarefas do Grupo (Com Checkboxes)

Use este checklist no Notion para cada integrante acompanhar suas atividades. Cada tarefa pode ser marcada quando concluída.

---

## Aluno 1: Atila Alcantara

- [ ] **Parte Prática – IDS com Snort e Docker**
  - [ ] Documentar por que escolhemos a imagem `frapsoft/snort:latest` para o Snort.
  - [ ] Explicar a escolha de `nginx:alpine` para o container “victim” e `nicolaka/netshoot:latest` para o container “attacker”.
  - [ ] Detalhar como criamos a rede Docker `lab-net` (bridge) e, opcionalmente, atribuíram IPs fixos (172.20.0.10, 172.20.0.20, 172.20.0.30).
  - [ ] Apresentar o conteúdo do `docker-compose.yml` comentando cada seção (rede, volumes, comando Snort, nome dos containers).
  - [ ] Mostrar onde ficam o `snort.conf` e o `local.rules` (e comentar exemplos de regras como “port scan” e “SQL injection”).
  - [ ] Ensinar como entrar no container “attacker-machine” (`docker exec -it attacker-machine bash`) e explicar como obter o IP real com `docker inspect`.
  - [ ] Executar testes de port scan, ataque HTTP/SQL Injection e ICMP flood, capturando alertas do Snort (`docker-compose logs -f snort | grep "[**]"`).
  - [ ] Incluir no slide breve explicação sobre compatibilidade Windows/Linux (Docker Desktop no Windows roda VM Linux por trás).

---

## Aluno 2: [Nome do Integrante]

- [ ] **Descrição da Empresa Fictícia e Endereçamento IP**
  - [ ] Definir nome da empresa, ramo de atuação, número de funcionários e setores (TI, Administrativo, Financeiro, Comercial).
  - [ ] Escrever um parágrafo sobre serviços/produtos oferecidos e justificar a importância da segurança.
  - [ ] Descrever a segmentação física (andares, prédios) e lógica (VLANs) da empresa.
  - [ ] Elaborar uma tabela de faixas de IP para cada VLAN/segmento (e.g., 192.168.10.0/24 para Servidores, 192.168.20.0/24 para Funcionários etc.).
  - [ ] Explicar o motivo da escolha dessas faixas e gateways (isolamento, crescimento futuro).

---

## Aluno 3: [Nome do Integrante]

- [ ] **Topologia de Rede e Posição do Firewall**
  - [ ] Desenhar o diagrama lógico da rede: Internet → Firewall perimetral → Switch Core → VLANs (Servidores, Funcionários, Administração, Visitantes).
  - [ ] Definir a posição ideal do firewall perimetral (entre Internet e rede interna) e, se aplicável, um firewall interno (entre DMZ/Servidores e VLAN-Funcionários).
  - [ ] Explicar regras conceituais de filtragem (ex.: permitir HTTPS na DMZ, bloquear SSH de fora, isolar VLAN-Visitantes).
  - [ ] Preparar slide ilustrando:
    - Fluxo de tráfego na rede
    - Regras de firewall (conceito, sem sintaxe exata)

---

## Aluno 4: [Nome do Integrante]

- [ ] **Vulnerabilidades e Política de Segurança**
  - [ ] Listar pelo menos 4 vulnerabilidades críticas para a empresa (phishing, brute-force SSH, port scan, SQL injection, malware via e-mail).
  - [ ] Descrever o impacto de cada vulnerabilidade se não for mitigada.
  - [ ] Definir contramedidas correspondentes (uso de Snort para port scan, MFA para brute-force, filtros de e-mail para malware).
  - [ ] Elaborar política de segurança simplificada (ABNT NBR ISO/IEC 17799 ou similar), incluindo:
    - Controle de Acesso (MFA, senhas fortes, perfis de usuário)
    - Uso aceitável de recursos (proibição de instalar software sem autorização)
    - Classificação de dados (público, interno, confidencial)
    - Gestão de Incidentes (fluxo de resposta quando o IDS alerta)
    - Backup e Continuidade (frequência, local de armazenamento)
  - [ ] Criar slide para cada tópico acima (vulnerabilidades, contramedidas e política).

---

## Aluno 5: [Nome do Integrante]

- [ ] **Configuração de Mecanismos Adicionais (escolher 1 a 4):**

  - **Servidor Web com SSL**

    - [ ] Instalar e configurar Apache/Nginx com certificado SSL (autoassinado ou Let’s Encrypt).
    - [ ] Mostrar como forçar redirecionamento de HTTP para HTTPS.
    - [ ] Demonstrar no navegador o cadeado de conexão segura.

  - **Servidor DNS com DNSSEC**

    - [ ] Instalar e configurar `bind9` (ou `dnsmasq`) com DNSSEC habilitado.
    - [ ] Gerar chaves e assinar zona DNS.
    - [ ] Testar validação DNSSEC via `dig +dnssec`.

  - **Proxy HTTP/HTTPS (Squid, TinyProxy, etc.)**

    - [ ] Instalar e configurar o proxy no Linux (por exemplo, Squid).
    - [ ] Definir ACLs para bloquear sites maliciosos ou categorias.
    - [ ] Testar navegação via proxy (`curl --proxy`).

  - **VPN (OpenVPN ou WireGuard)**

    - [ ] Instalar e configurar servidor VPN (OpenVPN ou WireGuard).
    - [ ] Gerar certificados/chaves e criar perfil de cliente.
    - [ ] Demonstrar o cliente conectando-se e acessando recursos internos seguros.

  - **Slides sugeridos (cada mecanismo ~2 minutos):**
    - **Web/SSL:** Instalação → Configuração de certificado → Teste no navegador
    - **DNS/DNSSEC:** Instalação → Geração de chaves → Teste de assinatura
    - **Proxy:** Instalação → Configuração de ACL → Teste de bloqueio de URL
    - **VPN:** Instalação → Arquivos de configuração → Conexão de cliente

---

### Observações Finais

- Ao longo do desenvolvimento do trabalho escrito, combine as partes dos cinco alunos em um documento único, estruturado em ABNT simplificada (capa, sumário, introdução, desenvolvimento, conclusão e referências).
- Cada integrante deve preparar **no mínimo 3 slides** e pode usar prints, diagramas e trechos de código conforme necessário.
- Para a apresentação oral (max. 15 minutos), cada aluno deve falar sobre sua parte (cerca de 3 minutos por integrante), garantindo que todos participem.
- Mantenham este checklist no Notion para que cada um marque as tarefas concluídas e monitore o progresso do grupo.

---
