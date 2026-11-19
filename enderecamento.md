# Planejamento de Endereçamento e VLANs

| Campo | Valor |
|---|---|
| Projeto | rede-pequeno-escritorio-vlans |
| Fase | 1, Planejamento |
| Ferramenta | Cisco Packet Tracer |
| Data | 2026-10-20 |
| Status | ✅ Concluído (atualizado em 2026-10-29 com o que foi implementado) |

## Contexto
Projeto de estudo que reproduz a rede de um pequeno escritório: segura, segmentada e confiável. A rede é dividida em 5 VLANs para isolar o tráfego por função (equipe, servidores, visitantes, câmeras e gerenciamento), com roteamento entre elas feito por um Core Switch Layer 3 e controle de acesso por ACL no próprio Core. O FW-ROUTER faz a saída para a internet (NAT).

## Objetivo desta fase
Definir, antes de configurar qualquer equipamento, o endereçamento IP de cada VLAN e se cada uma usará IP fixo ou DHCP. Planejar primeiro evita retrabalho e conflito de endereços.

## Tabela de endereçamento

| VLAN | Nome | Rede | Gateway | Atribuição de IP |
|---|---|---|---|---|
| 10 | Gerenciamento | 192.168.10.0/24 | 192.168.10.1 | Fixo |
| 20 | Equipe | 192.168.20.0/24 | 192.168.20.1 | DHCP (a partir do .11) |
| 30 | Servidores | 192.168.30.0/24 | 192.168.30.1 | Fixo |
| 40 | Wi-Fi Visitantes | 192.168.40.0/24 | 192.168.40.1 | DHCP (a partir do .11) |
| 50 | CCTV / IoT | 192.168.50.0/24 | 192.168.50.1 | Fixo |

## Links entre equipamentos

| Link | Rede | Lado A | Lado B |
|---|---|---|---|
| CORE-SW ↔ FW-ROUTER | 10.0.0.0/30 | CORE-SW 10.0.0.1 | FW-ROUTER 10.0.0.2 |
| FW-ROUTER ↔ ISP-ROUTER | 200.200.200.0/30 | FW-ROUTER 200.200.200.1 | ISP-ROUTER 200.200.200.2 |
| ISP-ROUTER ↔ Internet | 8.8.8.0/24 | ISP-ROUTER 8.8.8.1 | INTERNET-SERVER 8.8.8.8 |

## IPs fixos definidos

| Dispositivo | IP | VLAN |
|---|---|---|
| SW-EQUIPE | 192.168.10.2 | 10 |
| SW-VISITANTES | 192.168.10.3 | 10 |
| SW-SERVIDORES | 192.168.10.4 | 10 |
| SW-CCTV | 192.168.10.5 | 10 |
| SERVIDOR-ARQUIVOS | 192.168.30.10 | 30 |
| CAMERA-1 | 192.168.50.10 | 50 |

## Padrões adotados
- O número da VLAN define o terceiro octeto da rede (VLAN 20 → `192.168.20.0/24`), o que facilita identificar a VLAN só olhando o IP.
- O gateway de cada VLAN é sempre o endereço `.1`.
- Máscara `/24` (255.255.255.0) nas VLANs, suficiente para até 254 dispositivos por VLAN.
- Do `.1` ao `.10` de cada rede: reservado para gateway e IPs fixos. O DHCP só distribui a partir do `.11`. Assim o DHCP nunca entrega um endereço que já está em uso.
- Links entre roteadores usam `/30`, que tem só 2 endereços úteis (um para cada ponta).

## Decisões: IP fixo ou DHCP

| VLAN | Escolha | Justificativa |
|---|---|---|
| 10 Gerenciamento | Fixo | Os switches precisam ser acessados sempre pelo mesmo endereço para administração. |
| 20 Equipe | DHCP | Muitos PCs; configurar um a um é trabalhoso e gera erro. |
| 30 Servidores | Fixo | Os PCs precisam encontrar o servidor sempre no mesmo IP. Se mudasse, os serviços parariam. |
| 40 Wi-Fi Visitantes | DHCP | Dispositivos entram e saem o tempo todo; não há como configurar IP manualmente. |
| 50 CCTV / IoT | Fixo | O sistema de gravação precisa localizar cada câmera sempre no mesmo endereço. |

## Critério de decisão
- **DHCP** para dispositivos que mudam ou entram e saem da rede.
- **IP fixo** para dispositivos que precisam ser encontrados sempre no mesmo endereço.

## Boas práticas aplicadas
- Segmentação por função: um problema ou ataque em uma VLAN não se espalha para as outras.
- Visitantes isolados da rede interna por ACL na interface da VLAN 40 (SVI) no Core Switch, mantendo acesso à internet.
- Planejamento documentado antes da configuração.

## Aprendizado
Aprendi que é preciso definir o endereçamento de cada VLAN antes de configurar qualquer equipamento. Também aprendi a escolher quais VLANs usam IP fixo (dispositivos que precisam ser sempre encontrados, como servidores, câmeras e gerenciamento) e quais usam DHCP (dispositivos que mudam). Planejando assim, evito conflito de IP, porque os endereços fixos ficam separados da faixa que o DHCP distribui (reservei o `.1` ao `.10` com `excluded-address`).

## Fora do escopo da simulação
VPN e IPS aparecem na imagem de referência, mas o Packet Tracer não simula esses recursos.

## Próxima fase
Fase 2: montar a topologia no Packet Tracer e criar as VLANs nos switches.
