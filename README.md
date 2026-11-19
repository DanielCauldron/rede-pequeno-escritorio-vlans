# Aprendizados práticos — Projeto de Redes Cisco

## Sobre minha experiência neste laboratório

Desenvolvi este laboratório no Cisco Packet Tracer com o objetivo de compreender como funciona a infraestrutura de rede de um pequeno escritório.

Durante a construção, trabalhei na organização das redes, configuração dos equipamentos, comunicação entre diferentes segmentos e controle de acesso.

O foco não foi apenas fazer a topologia funcionar, mas entender o motivo de cada configuração e como identificar possíveis falhas.

## 1. Separando os dispositivos por função

**O que fiz:**

Criei cinco VLANs para separar o gerenciamento, a equipe, os servidores, os visitantes e os dispositivos de CFTV/IoT.

Configurei as portas dos switches para conectar cada dispositivo à VLAN correspondente e utilizei trunks 802.1Q para transportar diferentes VLANs entre os equipamentos.

**O que testei:**

Verifiquei a associação das portas às VLANs e a comunicação entre os switches.

**O que compreendi:**

Aprendi que a separação lógica permite organizar melhor uma rede corporativa. Mesmo utilizando a mesma infraestrutura física, os dispositivos podem pertencer a redes diferentes.

Também entendi que uma configuração incorreta de VLAN ou trunk pode impedir a comunicação, mesmo quando os cabos estão conectados corretamente.

## 2. Configurando a distribuição de endereços IP

**O que fiz:**

Planejei uma faixa de endereços IP para cada VLAN.

Configurei DHCP para distribuir automaticamente endereços aos dispositivos da equipe e aos visitantes. Para servidores, câmeras e equipamentos de gerenciamento, utilizei endereços fixos.

**O que testei:**

Verifiquei se os computadores recebiam automaticamente:

- Endereço IP.
- Máscara de sub-rede.
- Gateway padrão.

Também conferi se os endereços recebidos pertenciam à rede correta.

**O que compreendi:**

Entendi que o DHCP facilita a administração de uma rede, evitando a configuração manual de cada computador.

Também percebi a importância de planejar as faixas de endereçamento e reservar endereços para equipamentos que precisam manter um IP fixo.

## 3. Fazendo redes diferentes se comunicarem

**O que fiz:**

Configurei o switch Layer 3 com interfaces virtuais (SVIs) para funcionar como gateway das VLANs.

Ativei o roteamento utilizando `ip routing`.

**O que testei:**

Verifiquei a comunicação entre dispositivos de redes diferentes e o acesso da equipe aos servidores.

**O que compreendi:**

Aprendi que dispositivos de VLANs diferentes precisam de um equipamento de camada 3 para realizar a comunicação.

Também compreendi a importância do gateway padrão: ele indica para onde o dispositivo deve encaminhar pacotes destinados a outras redes.

Esse conhecimento ajuda a investigar situações em que um computador consegue acessar dispositivos da própria rede, mas não consegue alcançar outros segmentos.

## 4. Restringindo o acesso dos visitantes

**O que fiz:**

Configurei uma ACL estendida para impedir que a VLAN de visitantes acessasse as redes internas.

Mantive a possibilidade de acesso à internet para essa rede.

**O que testei:**

Realizei testes de comunicação entre a rede de visitantes e os recursos internos, além de verificar o acesso à internet simulada.

**O que compreendi:**

Entendi que permitir acesso à internet não significa permitir acesso aos servidores, computadores e demais recursos internos.

A ACL permite estabelecer regras de comunicação de acordo com a origem e o destino do tráfego.

Essa configuração demonstra como a segmentação e o controle de acesso podem contribuir para a segurança da infraestrutura.

## 5. Entendendo a saída para a internet

**O que fiz:**

Configurei um roteador de borda e outro roteador representando o provedor de internet.

Implementei NAT Overload (PAT) e rotas estáticas para permitir a comunicação entre a rede interna e a rede externa simulada.

**O que testei:**

Verifiquei a comunicação entre os dispositivos internos e o servidor que representa a internet no laboratório.

**O que compreendi:**

Entendi que os endereços privados utilizados dentro da empresa precisam ser traduzidos para permitir a comunicação externa por meio do NAT.

Também compreendi que uma rota padrão é necessária para indicar o caminho utilizado quando o destino não está em uma rede conhecida pelo equipamento.

## 6. Investigando falhas de conectividade

**O que pratiquei:**

Utilizei comandos de verificação para analisar o estado das interfaces, VLANs, trunks, rotas e listas de acesso.

Entre eles:

- `show vlan brief`
- `show interfaces trunk`
- `show ip interface brief`
- `show ip route`
- `show access-lists`
- `show running-config`
- `ping`
- `tracert`

**O que compreendi:**

Aprendi que não é adequado começar alterando configurações sem antes identificar onde está a falha.

Passei a entender o troubleshooting como uma sequência de investigação:

1. Verificar a conexão física.
2. Conferir a VLAN da porta.
3. Verificar IP e máscara.
4. Testar o gateway.
5. Conferir o roteamento.
6. Investigar possíveis bloqueios de ACL.

Essa sequência ajuda a reduzir tentativas desnecessárias e torna o diagnóstico mais organizado.

## 7. Validação do laboratório

Após concluir as configurações, realizei testes para verificar o funcionamento da rede.

| Cenário | Resultado esperado | Validação |
|---|---|---|
| Computador da equipe recebe IP | DHCP funcionando | Aprovado |
| Visitante recebe IP | DHCP funcionando | Aprovado |
| Dispositivo acessa seu gateway | Comunicação local | Aprovado |
| Equipe acessa servidores | Comunicação entre VLANs | Aprovado |
| Visitantes acessam redes internas | Acesso bloqueado | Aprovado |
| Visitantes acessam internet | Acesso permitido | Aprovado |
| Rede interna acessa servidor externo | NAT e rotas funcionando | Aprovado |

Os testes detalhados estão documentados em `04-testes/testes-validacao.md`.

## 8. O que levo deste projeto para o suporte técnico

Este laboratório ajudou a relacionar conceitos de redes com situações que podem aparecer na rotina de suporte técnico.

Alguns exemplos:

- Computador sem endereço IP: investigar DHCP e configuração de rede.
- Computador conectado, mas sem comunicação: verificar VLAN, gateway e conectividade.
- Acesso a um servidor indisponível: investigar roteamento e regras de acesso.
- Falha de internet: verificar gateway, rota padrão e NAT.
- Dispositivo conectado à rede incorreta: conferir a configuração da porta no switch.

O principal aprendizado foi compreender que problemas aparentemente semelhantes podem ter causas diferentes. Por isso, é importante investigar cada camada antes de realizar alterações.

## 9. Próximas evoluções

Este laboratório representa uma etapa inicial de aprofundamento em infraestrutura de redes.

Como continuidade dos estudos, pretendo praticar:

- Port Security para controle de dispositivos conectados às portas.
- DHCP Relay para distribuição de endereços entre diferentes redes.
- Spanning Tree Protocol (STP) para prevenção de loops.
- Monitoramento de equipamentos de rede.
- Diagnóstico de falhas em cenários mais próximos de ambientes corporativos reais.

## Conclusão

O desenvolvimento deste projeto me permitiu sair da teoria e compreender melhor como uma rede corporativa é estruturada.

Consegui configurar os equipamentos, estabelecer a comunicação entre diferentes redes, aplicar restrições de acesso e validar o funcionamento por meio de testes.

Também desenvolvi uma forma mais organizada de investigar problemas de conectividade, relacionando os conceitos de redes com situações encontradas na área de suporte técnico.

Meu objetivo com este laboratório é fortalecer minha base técnica e evoluir profissionalmente em direção à infraestrutura de TI.

---

**Autor:** Daniel Caldeirão  
**Laboratório:** Cisco Packet Tracer  
**Área:** Redes de Computadores | Suporte Técnico | Infraestrutura de TI
