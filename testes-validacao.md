# Testes de validação

| Campo | Valor |
|---|---|
| Projeto | rede-pequeno-escritorio-vlans |
| Data | 2026-10-29 |
| Status | ✅ Todos os objetivos validados |

> Observação: o primeiro ping costuma dar `timed out` ou `.` porque o ARP ainda está aprendendo o MAC. Repetir o ping resolve.

## Testes de conectividade

| # | Teste | Origem → Destino | Esperado | Obtido | OK? |
|---|---|---|---|---|---|
| 1 | DHCP equipe | PC-EQUIPE-1 | IP 192.168.20.11 | Recebeu .11 | ✅ |
| 2 | DHCP visitantes | PC-VISITANTE-1 | IP 192.168.40.11 | Recebeu .11 | ✅ |
| 3 | Gateway | PC-EQUIPE-1 → 192.168.20.1 | Responde | Responde | ✅ |
| 4 | Equipe → Servidores | PC-EQUIPE-1 → 192.168.30.10 | Responde | Responde | ✅ |
| 5 | Equipe → Internet | PC-EQUIPE-1 → 8.8.8.8 | Responde (NAT) | Responde | ✅ |
| 6 | Visitantes → Internet | PC-VISITANTE-1 → 8.8.8.8 | Responde | Responde | ✅ |
| 7 | Visitantes → Equipe | PC-VISITANTE-1 → 192.168.20.x | **Bloqueado** (ACL) | Bloqueado | ✅ |
| 8 | Visitantes → Servidores | PC-VISITANTE-1 → 192.168.30.10 | **Bloqueado** (ACL) | Bloqueado | ✅ |
| 9 | Visitantes → Gerenciamento | PC-VISITANTE-1 → 192.168.10.x | **Bloqueado** (ACL) | Bloqueado | ✅ |
| 10 | Wi-Fi visitantes | Smartphone0 | IP da VLAN 40, sem acesso interno, com internet | Conforme esperado | ✅ |
| 11 | Wi-Fi equipe | CELULAR-EQUIPE | IP da VLAN 20 | Recebeu IP | ✅ |
| 12 | Gerenciamento | Switches de acesso → 192.168.10.1 | Responde | Responde | ✅ |

## Comandos de verificação

| Objetivo | Comando | Onde |
|---|---|---|
| VLANs e portas | `show vlan brief` | Switches |
| Trunks | `show interfaces trunk` | Switches |
| IPs das interfaces | `show ip interface brief` | CORE-SW, roteadores |
| Rotas | `show ip route` | CORE-SW, roteadores |
| Leases DHCP | `show ip dhcp binding` | CORE-SW |
| ACL (contadores de match) | `show access-lists` | CORE-SW |
| NAT | `show ip nat translations` | FW-ROUTER |

## Dica de leitura: TTL

O TTL do ping mostra quantos roteadores existem no caminho. Ex.: ping de PC-EQUIPE-1 para 8.8.8.8 volta com TTL 125 = 3 roteadores no caminho (CORE-SW, FW-ROUTER, ISP-ROUTER).

## Fora do escopo da simulação

A imagem de referência inclui **VPN** e **IPS**. O Packet Tracer não simula esses recursos, então ficam documentados como evolução em ambiente real (firewall com VPN site-to-site/remota e IPS).
