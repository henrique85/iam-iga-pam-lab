# Eventos Importantes para IAM, IGA e PAM no Active Directory

## Objetivo

Este documento reúne eventos importantes do **Security Log** do Windows relacionados a **IAM**, **IGA** e **PAM** em ambientes com Active Directory.

A finalidade não é decorar todos os Event IDs, mas criar uma referência rápida para investigação, auditoria e troubleshooting.

## Ideia principal

Em ambientes Active Directory, muitas ações relevantes deixam rastros no **Security Log** dos Domain Controllers e das estações/servidores.

A regra prática é:

```text
O log nasce onde a ação aconteceu.
```

Exemplos:

```text
Alteração em grupo do AD → Security Log do Domain Controller
Login em estação → Security Log da estação
Login validado no domínio → Security Log do Domain Controller
Alteração em grupo local da máquina → Security Log da própria máquina
```

---

# 1. Eventos de Logon

## 4624 — Logon bem-sucedido

Indica que uma conta conseguiu autenticar com sucesso.

### Uso em IAM

Ajuda a responder:

* quem acessou;
* quando acessou;
* de onde acessou;
* qual tipo de logon foi usado.

### Pontos importantes

Observar campos como:

```text
Account Name
Account Domain
Logon Type
Workstation Name
Source Network Address
```

### Exemplos de Logon Type

| Logon Type | Significado                    |
| ---------- | ------------------------------ |
| 2          | Logon interativo local         |
| 3          | Logon via rede                 |
| 7          | Desbloqueio de estação         |
| 10         | Remote Desktop / RDP           |
| 11         | Logon com credenciais em cache |

---

## 4625 — Falha de logon

Indica tentativa de autenticação malsucedida.

### Uso em IAM e Segurança

Ajuda a identificar:

* senha incorreta;
* tentativa de ataque;
* conta inexistente;
* brute force;
* problemas de autenticação;
* conta bloqueada ou desabilitada.

### Campos importantes

```text
Account Name
Failure Reason
Status
Sub Status
Source Network Address
Workstation Name
Logon Type
```

### Atenção

Muitas falhas repetidas podem indicar:

```text
ataque de força bruta
serviço usando senha antiga
usuário digitando senha errada
aplicação tentando autenticar com credencial inválida
```

---

# 2. Eventos de Contas de Usuário

## 4720 — Conta de usuário criada

Indica que uma nova conta foi criada no Active Directory.

### Uso em IGA

Relaciona-se ao processo:

```text
Joiner = entrada de nova identidade
```

### Perguntas que o evento ajuda a responder

* quem criou a conta?
* quando a conta foi criada?
* qual conta foi criada?
* a criação estava aprovada?

---

## 4722 — Conta de usuário habilitada

Indica que uma conta foi habilitada.

### Uso em IAM

Importante para investigar contas que voltaram a ficar ativas.

### Risco

Conta antiga habilitada sem controle pode indicar:

```text
reativação indevida
erro operacional
tentativa de abuso
conta esquecida sendo reutilizada
```

---

## 4725 — Conta de usuário desabilitada

Indica que uma conta foi desabilitada.

### Uso em IGA

Relaciona-se ao processo:

```text
Leaver = saída/desligamento
```

### Boa prática

Em desligamento, a conta deve ser desabilitada no momento correto e a ação deve ser auditável.

---

## 4726 — Conta de usuário excluída

Indica que uma conta foi excluída.

### Atenção

Excluir conta nem sempre é a melhor primeira ação. Em muitos ambientes, é preferível:

```text
desabilitar
remover acessos
preservar histórico
mover para OU de desligados
reter evidências
```

Excluir imediatamente pode prejudicar auditoria e rastreabilidade.

---

## 4738 — Conta de usuário alterada

Indica alteração em atributos de uma conta.

### Exemplos de alterações

* nome;
* descrição;
* opções da conta;
* parâmetros de logon;
* propriedades relacionadas à conta.

### Uso em IAM

Ajuda a identificar mudanças administrativas em identidades.

---

## 4740 — Conta bloqueada

Indica que uma conta foi bloqueada após tentativas inválidas de autenticação.

### Uso em troubleshooting

Muito útil para investigar:

```text
usuário digitando senha errada
serviço com senha antiga
credencial salva em estação
ataque de força bruta
aplicação tentando autenticar repetidamente
```

### Campos importantes

```text
Account Name
Caller Computer Name
```

O campo **Caller Computer Name** pode indicar de onde partiu a tentativa que causou o bloqueio.

---

# 3. Eventos de Grupos de Segurança

## 4728 — Membro adicionado a grupo global de segurança

Indica que uma conta foi adicionada a um grupo global de segurança.

### Exemplo

```text
adm.carlos.lima adicionado ao grupo GG_ADMIN_SERVIDORES
```

### Uso em IAM/PAM

Muito importante para auditar concessão de acesso.

Se o grupo for privilegiado, o evento é crítico.

### Campos importantes

```text
Subject
Member
Group
```

Onde:

```text
Subject = quem fez a alteração
Member = quem foi adicionado
Group = grupo alterado
```

---

## 4729 — Membro removido de grupo global de segurança

Indica que uma conta foi removida de um grupo global de segurança.

### Uso em IGA

Importante para evidenciar remoção de acesso.

Relaciona-se com:

```text
Mover = mudança de área/função
Leaver = desligamento
Remoção de acesso
```

### Exemplo

```text
adm.carlos.lima removido do grupo GG_ADMIN_SERVIDORES
```

---

## 4732 — Membro adicionado a grupo local de segurança

Indica que uma conta foi adicionada a um grupo local.

### Exemplo

```text
IAMLAB\GG_ADMIN_SERVIDORES adicionado ao grupo local Administrators do WIN10-01
```

### Uso em PAM

Importante para auditar alterações em grupos locais privilegiados, como:

```text
Administrators
Remote Desktop Users
Backup Operators
```

### Onde procurar

Normalmente no **Security Log da máquina onde o grupo local foi alterado**.

---

## 4733 — Membro removido de grupo local de segurança

Indica que uma conta foi removida de um grupo local.

### Uso em PAM

Ajuda a evidenciar remoção de privilégio local.

---

## 4735 — Grupo local de segurança alterado

Indica alteração em um grupo local de segurança.

### Uso

Pode aparecer quando propriedades do grupo são modificadas.

---

## 4737 — Grupo global de segurança alterado

Indica alteração em um grupo global de segurança.

### Uso

Útil para identificar mudanças nas propriedades de grupos do AD.

---

# 4. Eventos de Grupos Privilegiados

## Grupos que merecem atenção especial

Alterações nos seguintes grupos devem ser tratadas como sensíveis:

```text
Domain Admins
Enterprise Admins
Schema Admins
Administrators
Account Operators
Backup Operators
Server Operators
Print Operators
Remote Desktop Users
GG_ADMIN_SERVIDORES
grupos equivalentes criados pela empresa
```

## Por que isso importa?

Alterar membro de grupo privilegiado pode conceder poder elevado.

Em PAM, esse tipo de alteração precisa responder:

```text
quem concedeu?
quem recebeu?
quando recebeu?
por qual motivo?
havia aprovação?
o acesso é temporário ou permanente?
foi revisado?
```

---

# 5. Eventos de Política e Auditoria

## 4719 — Política de auditoria alterada

Indica alteração na política de auditoria do sistema.

### Uso em segurança

Evento crítico, pois um atacante ou administrador mal-intencionado pode tentar reduzir auditoria para apagar rastros futuros.

### Atenção

Mudança em política de auditoria deve ser rara, planejada e documentada.

---

## 1102 — Log de auditoria apagado

Indica que o log de auditoria foi limpo.

### Uso em investigação

Evento extremamente sensível.

Apagar log de segurança pode indicar:

```text
tentativa de ocultar atividade
erro administrativo
limpeza indevida
ação maliciosa
```

### Boa prática

Esse evento deve gerar alerta em ambientes corporativos.

---

# 6. Eventos de Kerberos e Autenticação no Domínio

## 4768 — Ticket Kerberos TGT solicitado

Indica solicitação de Ticket Granting Ticket.

### Uso

Ajuda a investigar autenticação Kerberos no domínio.

---

## 4769 — Ticket Kerberos de serviço solicitado

Indica solicitação de ticket para acesso a serviço.

### Uso

Pode ajudar em investigações mais avançadas envolvendo Kerberos, acesso a serviços e possíveis ataques.

---

## 4771 — Pré-autenticação Kerberos falhou

Indica falha de pré-autenticação Kerberos.

### Uso

Pode aparecer em casos de senha incorreta, problemas de conta ou tentativas suspeitas.

---

## 4776 — Validação de credenciais NTLM

Indica tentativa de validação de credenciais usando NTLM.

### Uso

Útil em ambientes legados ou quando aplicações ainda usam NTLM.

### Atenção

Uso excessivo de NTLM pode indicar dependência de autenticação antiga.

---

# 7. Onde procurar os eventos

## No Domain Controller

Procurar eventos relacionados a:

```text
criação de usuários
alteração de usuários
alteração de grupos do domínio
autenticação no domínio
Kerberos
NTLM
políticas do domínio
```

Caminho:

```text
Event Viewer
└── Windows Logs
    └── Security
```

## Na estação ou servidor membro

Procurar eventos relacionados a:

```text
logon local
RDP
alteração em grupo local
execução administrativa local
acesso local
```

Caminho:

```text
Event Viewer
└── Windows Logs
    └── Security
```

---

# 8. Eventos mais úteis para IAM, IGA e PAM

## IAM

| Evento | Uso                            |
| ------ | ------------------------------ |
| 4624   | Logon bem-sucedido             |
| 4625   | Falha de logon                 |
| 4768   | Solicitação de TGT Kerberos    |
| 4769   | Solicitação de ticket Kerberos |
| 4776   | Validação NTLM                 |

## IGA

| Evento | Uso                              |
| ------ | -------------------------------- |
| 4720   | Usuário criado                   |
| 4722   | Usuário habilitado               |
| 4725   | Usuário desabilitado             |
| 4726   | Usuário excluído                 |
| 4738   | Usuário alterado                 |
| 4728   | Membro adicionado a grupo global |
| 4729   | Membro removido de grupo global  |

## PAM

| Evento | Uso                                  |
| ------ | ------------------------------------ |
| 4728   | Adição a grupo global privilegiado   |
| 4729   | Remoção de grupo global privilegiado |
| 4732   | Adição a grupo local privilegiado    |
| 4733   | Remoção de grupo local privilegiado  |
| 4719   | Política de auditoria alterada       |
| 1102   | Log de auditoria apagado             |

---

# 9. Checklist rápido de investigação

## Quando um usuário diz que não consegue logar

Verificar:

```text
4625 — falha de logon
4740 — conta bloqueada
4725 — conta desabilitada
4771 — falha Kerberos
4776 — falha NTLM
```

## Quando um usuário recebeu acesso indevido

Verificar:

```text
4728 — adicionado a grupo global
4732 — adicionado a grupo local
4738 — conta alterada
```

## Quando um usuário perdeu acesso

Verificar:

```text
4729 — removido de grupo global
4733 — removido de grupo local
4725 — conta desabilitada
```

## Quando houve suspeita de privilégio indevido

Verificar:

```text
4728 — adicionado a grupo global privilegiado
4732 — adicionado a grupo local privilegiado
4624 — logon bem-sucedido
4625 — falha de logon
1102 — log apagado
4719 — auditoria alterada
```

---

# 10. Frases para fixar

```text
Não é necessário decorar todos os Event IDs.
É necessário saber quais ações deixam rastros e onde procurar.
```

```text
O log nasce onde a ação aconteceu.
```

```text
Se alterou grupo do domínio, investigue o Domain Controller.
```

```text
Se alterou grupo local da máquina, investigue a própria máquina.
```

```text
Acesso privilegiado sem auditoria é risco invisível.
```

```text
Controle de acesso bom permite, nega e registra.
```

---

# 11. Observação sobre ambiente híbrido

Em ambientes híbridos com Active Directory on-premises e Microsoft Entra ID:

```text
Alteração feita no AD local → investigar no Domain Controller
Alteração feita no Entra ID → investigar no Entra Audit Log
Sincronização AD → Entra → investigar também logs de sincronização/provisionamento
```

A sincronização não substitui a auditoria local.

Se a ação aconteceu no AD on-premises, a evidência primária estará no ambiente on-premises.

---

# 12. Eventos utilizados no laboratório

Durante o laboratório, foram gerados eventos de alteração no grupo privilegiado:

```text
GG_ADMIN_SERVIDORES
```

Eventos esperados:

| Evento | Significado                                    |
| ------ | ---------------------------------------------- |
| 4728   | Membro adicionado ao grupo global de segurança |
| 4729   | Membro removido do grupo global de segurança   |

Exemplo validado:

```text
Evento: 4729
Ação: membro removido de grupo global de segurança
Executado por: IAMLAB\Administrator
Membro removido: IAMLAB\adm.carlos.lima
Grupo alterado: IAMLAB\GG_ADMIN_SERVIDORES
Computador: DC01.iamlab.local
```

Esse teste confirmou que alterações em grupos privilegiados podem ser rastreadas no Security Log do Domain Controller.
