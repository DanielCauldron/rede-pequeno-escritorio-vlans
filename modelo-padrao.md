# MODELO PADRÃO DE DOCUMENTAÇÃO

> Copie este modelo para criar qualquer documento novo do projeto.
> Salvar em: `00-padrao/modelo-padrao.md`

---

## 1. Regras de nomes

| Item | Regra | Exemplo |
|---|---|---|
| Arquivos e pastas | minúsculas, sem espaço, sem acento, hífen no lugar do espaço | `sw-equipe.txt` |
| Pastas numeradas | `NN-nome` | `03-configuracoes` |
| Equipamentos | MAIÚSCULAS com hífen | `CORE-SW`, `SW-EQUIPE`, `PC-EQUIPE-1` |
| Datas | `AAAA-MM-DD` | `2026-10-20` |
| Prints | `NN-descricao.png` | `01-vlans-criadas.png` |

---

## 2. Modelo de documento (copie a partir daqui)

# [Título curto do que foi feito]

| Campo | Valor |
|---|---|
| Projeto | rede-pequeno-escritorio-vlans |
| Fase | [Ex.: Fase 2, VLANs] |
| Equipamento | [Ex.: CORE-SW] |
| Data | AAAA-MM-DD |
| Status | ⬜ Em andamento / ✅ Concluído / ❌ Com problema |

## Objetivo
[Uma frase: o que este passo resolve.]

## Pré-requisitos
- [O que precisa estar pronto antes]

## Passos e comandos

```
[Comandos usados, na ordem]
```

## Resultado esperado
[O que deve aparecer ou acontecer se deu certo.]

## Teste de validação

| Teste | Comando | Resultado esperado | Resultado obtido | OK? |
|---|---|---|---|---|
| [Ex.: ping PC → gateway] | `ping 192.168.20.1` | 4 respostas | | ⬜ |

## Problemas encontrados

| Sintoma | Causa | Solução |
|---|---|---|
| [O que você viu] | [Por que aconteceu] | [O que corrigiu] |

## Aprendizado
[Uma frase: o que ficou de lição, em palavras suas.]

## Prints
- `06-imagens/NN-descricao.png`

---

## 3. Modelo de commit no Git

Formato: `tipo: descrição curta`

| Tipo | Quando usar | Exemplo |
|---|---|---|
| `docs` | documentação nova ou alterada | `docs: adiciona tabela de enderecamento` |
| `config` | configuração de equipamento | `config: cria VLANs no CORE-SW` |
| `test` | testes de validação | `test: valida ping entre VLANs` |
| `fix` | correção | `fix: corrige trunk entre CORE-SW e SW-EQUIPE` |
| `lab` | cenário de falha | `lab: simula DHCP desligado` |
