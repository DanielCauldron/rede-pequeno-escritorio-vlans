# Rede para um pequeno escritório (segura, segmentada, confiável)

Laboratório no **Cisco Packet Tracer** que simula a rede de um escritório pequeno: VLANs separadas, DHCP, roteamento entre redes, acesso restrito para visitantes, NAT e Wi-Fi.

**Objetivo:** praticar configuração e **troubleshooting** de redes (foco em suporte N1 → infraestrutura).

![Topologia da rede](02-topologia/topologia.png)

## Entendendo o projeto de forma simples

Pensa na rede como um **prédio**:

| No prédio | Na rede | Para quê |
|---|---|---|
| Andar | **VLAN** | Separa os grupos (equipe, visitantes, servidores...) |
| Elevador | **Trunk** | Leva todas as VLANs entre os switches por um único cabo |
| Porta da sala | **Porta de acesso** | Liga um dispositivo a uma VLAN específica |
| Porteiro | **Gateway / SVI** | Decide quem pode ir de um andar para outro (ACL) e quem sai para a rua (internet) |

## Equipamentos

| Equipamento | Modelo | Função |
|---|---|---|
| CORE-SW | Switch L3 3560-24PS | VLANs, roteamento entre VLANs (SVIs), DHCP e ACL |
| FW-ROUTER | Roteador 2911 | Borda da rede interna e NAT |
| ISP-ROUTER | Roteador 2911 | Simula o provedor de internet |
| SW-EQUIPE, SW-VISITANTES, SW-SERVIDORES, SW-CCTV | Switch 2960-24TT | Acesso dos dispositivos finais |
| AP-EQUIPE, AP-VISITANTES | Access Point | Wi-Fi separado por VLAN |

## Fluxo da rede

```
INTERNET-SERVER (8.8.8.8)
        |
   ISP-ROUTER  (8.8.8.1 | 200.200.200.2)
        |
   FW-ROUTER   (200.200.200.1 | 10.0.0.2)   <- NAT
        |
   CORE-SW (L3) (10.0.0.1)    <- VLANs, SVIs, DHCP, ACL
    |        |         |        |
SW-EQUIPE SW-VISITANTES SW-SERVIDORES SW-CCTV
PC + AP   PC + AP       Servidor      Câmera
```

Arquivo do laboratório: [`02-topologia/projeto.pkt`](02-topologia/projeto.pkt)

## Endereçamento

| VLAN | Nome | Rede | Gateway | IP |
|---|---|---|---|---|
| 10 | Gerenciamento | 192.168.10.0/24 | .1 | fixo |
| 20 | Equipe | 192.168.20.0/24 | .1 | DHCP (.11+) |
| 30 | Servidores | 192.168.30.0/24 | .1 | fixo |
| 40 | Wi-Fi Visitantes | 192.168.40.0/24 | .1 | DHCP (.11+) |
| 50 | CCTV/IoT | 192.168.50.0/24 | .1 | fixo |

Links: CORE-SW ↔ FW-ROUTER `10.0.0.0/30` · FW-ROUTER ↔ ISP `200.200.200.0/30` · Internet `8.8.8.0/24`.
Endereços `.1` a `.10` de cada rede ficam reservados para IP fixo (evita conflito com o DHCP).

## O que foi implementado

- Segmentação em 5 VLANs com trunks 802.1Q
- Roteamento entre VLANs no switch Layer 3 (SVIs + `ip routing`)
- DHCP para Equipe e Visitantes, IP fixo para servidores, câmera e gerenciamento
- ACL: visitantes **não** acessam a rede interna (nem por Wi-Fi), mas **têm internet**
- Equipe acessa os servidores
- NAT overload (PAT) e rotas estáticas/padrão
- Dois Wi-Fi separados (EQUIPE-WIFI e VISITANTES-WIFI) com WPA2-PSK

**Fora do escopo:** VPN e IPS aparecem na imagem de referência, mas o Packet Tracer não simula esses recursos.

## Resultados

Todos os testes de conectividade passaram (DHCP, gateway, equipe → servidores, internet, visitantes bloqueados da rede interna). Tabela completa em [`04-testes/testes-validacao.md`](04-testes/testes-validacao.md).

## Habilidades praticadas

VLAN e trunk 802.1Q · roteamento Layer 3 · DHCP · ACL estendida · NAT/PAT · rotas estáticas · Wi-Fi com WPA2 · leitura de `show` e troubleshooting por camadas.

## Estrutura do repositório

| Pasta | Conteúdo |
|---|---|
| `00-padrao/` | Padrão de nomes, template de documento e formato de commit |
| `01-planejamento/` | Plano de endereçamento e decisões |
| `02-topologia/` | `projeto.pkt` e imagem da topologia |
| `03-configuracoes/` | Comandos de cada equipamento |
| `04-testes/` | Testes de validação |
| `05-troubleshooting/` | Erros reais, método de diagnóstico e cenários de treino |
| `06-imagens/` | Prints |

## Método de troubleshooting

Cabo → VLAN → IP → gateway → rota → ACL. Detalhes e erros reais em [`05-troubleshooting/cenarios-de-falha.md`](05-troubleshooting/cenarios-de-falha.md).

## Próximos passos

- Praticar cenários de falha (VLAN errada, DHCP, trunk, rota, ACL)
- Evoluir para DHCP relay, port-security e VPN em ambiente real

## Autor

Daniel Caldeirão, analista de suporte N1 em transição para dados/infraestrutura.
