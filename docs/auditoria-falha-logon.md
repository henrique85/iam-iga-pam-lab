# Auditoria — Investigação de Falha de Logon no Active Directory

## Objetivo

Este documento registra uma simulação de falha de logon em ambiente Active Directory, com o objetivo de compreender como eventos de autenticação são registrados no cliente Windows e no Domain Controller.

O teste foi realizado como parte do laboratório de IAM, IGA e PAM.

## Cenário do laboratório

| Item              | Valor                                  |
| ----------------- | -------------------------------------- |
| Domínio           | `iamlab.local`                         |
| Domain Controller | `DC01`                                 |
| Cliente Windows   | `WIN10-01`                             |
| Usuária testada   | `IAMLAB\maria.souza`                   |
| Tipo de teste     | Tentativa de logon com senha incorreta |

## Ação executada

No cliente `WIN10-01`, foi realizada tentativa de logon com a conta:

`IAMLAB\maria.souza`

A senha foi digitada incorretamente de forma proposital para gerar eventos de auditoria.

## Eventos esperados

Durante o teste, os principais eventos esperados eram:

| Local      | Evento | Significado                        |
| ---------- | -----: | ---------------------------------- |
| `WIN10-01` | `4625` | Falha no logon de uma conta        |
| `DC01`     | `4771` | Falha de pré-autenticação Kerberos |

## Evento no Domain Controller — 4771

No `DC01`, foi localizado o evento:

`4771 — Kerberos pre-authentication failed`

### Dados observados

| Campo          | Valor                |
| -------------- | -------------------- |
| Account Name   | `maria.souza`        |
| Security ID    | `IAMLAB\maria.souza` |
| Service Name   | `krbtgt/IAMLAB`      |
| Client Address | `192.168.100.20`     |
| Failure Code   | `0x18`               |

### Interpretação

O evento indica que o Domain Controller recebeu uma tentativa de autenticação Kerberos para a conta `maria.souza`.

O endereço de origem foi:

`192.168.100.20`

Esse IP corresponde ao cliente `WIN10-01`.

O código de falha:

`0x18`

indica falha de pré-autenticação Kerberos, normalmente associada a senha incorreta.

Em termos simples:

> O cliente pediu ao domínio um ticket Kerberos para a usuária `maria.souza`, mas o Domain Controller recusou a autenticação porque a senha estava incorreta.

## Evento no cliente Windows — 4625

No `WIN10-01`, foi localizado o evento:

`4625 — Falha no logon de uma conta`

### Dados observados

| Campo                  | Valor                                           |
| ---------------------- | ----------------------------------------------- |
| Nome da conta          | `maria.souza`                                   |
| Domínio da conta       | `IAMLAB`                                        |
| Tipo de logon          | `2`                                             |
| Razão da falha         | Nome de usuário desconhecido ou senha incorreta |
| Status                 | `0xC000006D`                                    |
| Substatus              | `0xC000006A`                                    |
| Processo de logon      | `User32`                                        |
| Pacote de autenticação | `Negotiate`                                     |
| Estação de trabalho    | `WIN10-01`                                      |
| Endereço de origem     | `127.0.0.1`                                     |

### Interpretação

O evento `4625` mostra que a falha ocorreu na própria estação `WIN10-01`.

O tipo de logon:

`2`

representa logon interativo local, ou seja, uma tentativa de login feita diretamente na tela do Windows.

Os códigos:

| Código       | Significado             |
| ------------ | ----------------------- |
| `0xC000006D` | Falha genérica de logon |
| `0xC000006A` | Senha incorreta         |

O processo de logon `User32` e o pacote `Negotiate` são compatíveis com uma tentativa de autenticação feita pela interface gráfica do Windows.

## Correlação entre os eventos

A simulação gerou eventos em dois pontos diferentes:

| Local      | Evento | Papel na investigação                                      |
| ---------- | -----: | ---------------------------------------------------------- |
| `WIN10-01` | `4625` | Mostra a falha de logon vista pela estação                 |
| `DC01`     | `4771` | Mostra a falha de autenticação Kerberos vista pelo domínio |

A cadeia de autenticação observada foi:

```text
Usuário digitou senha incorreta no WIN10-01
↓
WIN10-01 tentou autenticar a conta no domínio
↓
DC01 recusou a autenticação Kerberos
↓
DC01 registrou o evento 4771
↓
WIN10-01 registrou o evento 4625
```

## Conclusão da investigação

A análise confirmou que a falha de logon ocorreu devido à senha incorreta informada para a conta:

`IAMLAB\maria.souza`

O evento no cliente `WIN10-01` demonstrou a falha na tentativa de logon interativo.

O evento no `DC01` demonstrou a falha de pré-autenticação Kerberos no domínio.

Juntos, os eventos permitiram responder:

| Pergunta                                   | Resposta           |
| ------------------------------------------ | ------------------ |
| Quem tentou logar?                         | `maria.souza`      |
| Onde tentou logar?                         | `WIN10-01`         |
| Qual domínio foi usado?                    | `IAMLAB`           |
| Qual foi o tipo de logon?                  | Interativo local   |
| Por que falhou?                            | Senha incorreta    |
| O Domain Controller registrou a tentativa? | Sim, evento `4771` |
| A estação registrou a tentativa?           | Sim, evento `4625` |

## Conceito IAM aplicado

Este teste demonstra que autenticação em ambiente Active Directory gera rastros em mais de um ponto.

O cliente registra a tentativa de logon local.

O Domain Controller registra a validação da identidade no domínio.

Essa correlação é essencial para troubleshooting e investigação de segurança.

## Diferença entre os eventos

| Evento | Local mais comum                             | O que indica                       |
| ------ | -------------------------------------------- | ---------------------------------- |
| `4625` | Estação ou servidor onde o logon foi tentado | Falha de logon na máquina          |
| `4771` | Domain Controller                            | Falha de pré-autenticação Kerberos |

## Lição aprendida

Nem todo evento com o mesmo ID representa o mesmo cenário.

Durante a análise, foi identificado outro evento `4625` relacionado à conta local `WIN10-01\Administrador`, originado pelo processo `consent.exe`, associado ao UAC.

Esse evento não correspondia à simulação da usuária `maria.souza`.

A lição principal foi:

> Não basta filtrar pelo Event ID. É necessário interpretar os campos do evento.

Campos essenciais para análise:

* Account Name;
* Account Domain;
* Logon Type;
* Failure Reason;
* Status;
* Substatus;
* Process Name;
* Workstation Name;
* Source Network Address.

## Frases para fixar

O `4625` mostra a falha vista pela máquina onde o logon ocorreu.

O `4771` mostra a falha vista pelo Domain Controller durante a autenticação Kerberos.

O log nasce onde a ação aconteceu.

Um único evento conta uma parte da história. A correlação entre eventos conta a narrativa.

Controle de acesso bom permite, nega e registra.
