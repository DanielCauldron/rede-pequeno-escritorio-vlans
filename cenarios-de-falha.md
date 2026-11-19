# Troubleshooting: erros reais do projeto

| Campo | Valor |
|---|---|
| Projeto | rede-pequeno-escritorio-vlans |
| Data | 2026-10-29 |
| Status | ✅ Todos resolvidos |

## Método de diagnóstico (por camadas)

Cabo → VLAN → IP → gateway → rota → ACL. Sempre começo pela camada de baixo e só subo quando ela está confirmada com um `show`.

1. Ler a mensagem de erro e achar o `^`, que mostra onde o switch parou de entender.
2. Olhar o prompt para saber em que modo estou.
3. Corrigir **um** comando por vez.
4. Conferir com `show`, sem confiar só em "deu certo".
5. Salvar com `write memory`.

## Fase 2: criação das VLANs (CORE-SW)

| # | Sintoma | Causa | Solução | Como conferi |
|---|---|---|---|---|
| 1 | `% Invalid input` em `enable configure terminal` | Dois comandos na mesma linha | Um comando por linha | Prompt `(config)#` |
| 2 | `% Incomplete command` ao digitar `enable` | `enable` usado dentro do `(config)#` | `end`, depois `configure terminal` | Prompt `Switch#` |
| 3 | `% Invalid input` em `valn 30` | Erro de digitação (`valn`) | Corrigir para `vlan 30` | `show vlan brief` |
| 4 | `% Invalid input` em `name` fora da VLAN | `name` só vale em `(config-vlan)#` | Entrar na VLAN antes | Prompt `(config-vlan)#` |
| 5 | Prompt virou `CORE-SW>` | `exit` a mais | `enable` e `configure terminal` | Prompt `(config)#` |
| 6 | VLAN 20 chamada `EQUIP` | Nome digitado incompleto | `name EQUIPE` de novo | `show vlan brief` |
| 7 | `% Invalid input` ao digitar só `EQUIPE` | Faltou a palavra `name` | `name EQUIPE` | Erro não repetiu |

## Fases 3 a 6: trunks, roteamento, DHCP, ACL, NAT e Wi-Fi

| # | Sintoma | Causa | Solução | Como conferi |
|---|---|---|---|---|
| 8 | Erro de typo em `switchport` (`swictchoprt`) e na máscara do IP (ponto sobrando) | Digitação | Retornar o comando correto | `show running-config interface ...` |
| 9 | Mensagens `SPANTREE` / trunk inconsistente | Trunk configurado só de um lado | Configurar o outro lado também | `show interfaces trunk` nos dois lados |
| 10 | Trunk não sobe no 3560 | 3560 exige `switchport trunk encapsulation dot1q` antes do `mode trunk` (o 2960 não) | Adicionar o encapsulation | `show interfaces trunk` |
| 11 | PC não pega IP | Porta de acesso na VLAN errada (ex.: Fa0/1 na VLAN 40) | Corrigir `switchport access vlan` | `show vlan brief` + `ipconfig` |
| 12 | Ping vai e **não volta** até a internet | FW-ROUTER sem rota padrão (`Gateway of last resort is not set`) | `ip route 0.0.0.0 0.0.0.0 200.200.200.2` | `show ip route` + ping 8.8.8.8 |
| 13 | DHCP e roteamento sumiram depois do reboot | Config não salva (`ip routing` e pools perdidos) | Reaplicar e `write memory` | `show running-config` após reload |
| 14 | `show` aparece vazio no Packet Tracer | Mensagem `%SYS-5-CONFIG_I` cobre a saída | Rodar o `show` de novo | Saída completa |
| 15 | Cabo sem link | Tipo de cabo errado | Straight-through: switch↔router, PC↔switch. Cross-over: router↔router, router↔server | LED verde |

## Cenários para treinar depois

| Cenário | Como quebrar | Sintoma esperado | Como diagnosticar |
|---|---|---|---|
| A | Trocar a VLAN da porta do PC | PC sem IP / IP da VLAN errada | `show vlan brief` |
| B | Apagar o pool DHCP | PC recebe `169.254.x.x` | `show ip dhcp pool` |
| C | Tirar `mode trunk` de um lado | Sem comunicação entre switches | `show interfaces trunk` |
| D | Remover a rota padrão | Ping vai e não volta | `show ip route` |
| E | Mudar a ACL (`permit` no lugar de `deny`) | Visitante alcança a rede interna | `show access-lists` |

## Aprendizado

Quase todo problema de rede tem causa simples (cabo, VLAN, IP, rota). Seguir a ordem das camadas evita chutar. E configuração só vale depois do `write memory`: sem salvar, o reboot apaga o trabalho.
