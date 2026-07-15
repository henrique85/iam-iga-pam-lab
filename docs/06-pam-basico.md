# 06 - PAM Básico

## Objetivo

Esta etapa documenta a implementação inicial de conceitos de **PAM - Privileged Access Management** no laboratório.

O objetivo foi separar contas comuns de contas administrativas e conceder privilégio local por meio de grupo, evitando atribuição direta de permissões administrativas a usuários individuais.

## Conceito principal

A regra aplicada nesta etapa foi:

> Conta comum trabalha. Conta administrativa administra.

Isso significa que um usuário deve possuir uma conta normal para atividades cotidianas e uma conta separada para atividades administrativas.

## Contas comuns

| Conta       | Finalidade                           |
| ----------- | ------------------------------------ |
| joao.silva  | Uso cotidiano do usuário João Silva  |
| carlos.lima | Uso cotidiano do usuário Carlos Lima |

## Contas administrativas criadas

As contas administrativas foram criadas na OU:

```text
IAMLAB > Administrativos > Admins_TI
```

Contas criadas:

| Conta administrativa | Finalidade                                             |
| -------------------- | ------------------------------------------------------ |
| adm.joao.silva       | Administração técnica associada ao usuário João Silva  |
| adm.carlos.lima      | Administração técnica associada ao usuário Carlos Lima |

## Grupo privilegiado criado

Foi criado o grupo:

```text
GG_ADMIN_SERVIDORES
```

Localização:

```text
IAMLAB > Grupos > Privilegiados
```

Configuração:

| Item        | Valor    |
| ----------- | -------- |
| Group scope | Global   |
| Group type  | Security |

## Membros do grupo privilegiado

| Grupo               | Membro          |
| ------------------- | --------------- |
| GG_ADMIN_SERVIDORES | adm.joao.silva  |
| GG_ADMIN_SERVIDORES | adm.carlos.lima |

## Concessão de privilégio local

O grupo de domínio:

```text
IAMLAB\GG_ADMIN_SERVIDORES
```

foi adicionado ao grupo local:

```text
Administrators
```

na estação:

```text
WIN10-01
```

## Cadeia de privilégio

A cadeia de privilégio validada foi:

```text
adm.joao.silva
↓
GG_ADMIN_SERVIDORES
↓
Administrators local do WIN10-01
↓
Execução elevada via UAC
```

## Teste positivo

Foi realizado login com a conta:

```text
IAMLAB\adm.joao.silva
```

Em seguida, foi aberto o Prompt de Comando como administrador e executado:

```cmd
whoami /groups
```

O resultado confirmou:

```text
BUILTIN\Administradores
Grupo obrigatório, Ativado por padrão, Grupo ativado, Proprietário do grupo
```

Também foi confirmado:

```text
IAMLAB\GG_ADMIN_SERVIDORES
Grupo obrigatório, Ativado por padrão, Grupo ativado
```

E o nível de integridade elevado:

```text
Rótulo Obrigatório\Nível Obrigatório Alto
```

## Teste negativo

Foi realizado login com a conta comum:

```text
IAMLAB\joao.silva
```

Ao tentar executar uma ação administrativa, o sistema solicitou credenciais administrativas ou impediu a elevação.

Resultado validado:

```text
joao.silva não possui privilégio administrativo local.
```

## Diferença entre conta comum e conta administrativa

| Conta          | Tipo                 | Privilégio                    |
| -------------- | -------------------- | ----------------------------- |
| joao.silva     | Conta comum          | Sem administração local       |
| adm.joao.silva | Conta administrativa | Administração local via grupo |

## Conceito PAM aplicado

Nesta etapa, foram praticados os conceitos de:

* separação de contas;
* privilégio administrativo controlado;
* concessão de privilégio por grupo;
* redução de privilégio excessivo;
* validação positiva e negativa;
* uso de UAC;
* distinção entre autenticação e elevação;
* base para controle de acesso privilegiado.

## Observação sobre UAC

Durante o teste, foi observado que uma conta administrativa pode estar logada sem que todos os processos estejam elevados.

Em um Prompt comum, o grupo Administrators pode aparecer como:

```text
Grupo usado apenas para negar
```

Após executar o Prompt como administrador, o token elevado passa a apresentar:

```text
Grupo ativado
```

Isso demonstra que:

```text
Pertencer ao grupo Administrators não significa que todo processo roda elevado automaticamente.
```

## Observação de segurança

Este modelo ainda é básico.

Em um ambiente corporativo mais maduro, controles adicionais seriam necessários, como:

* MFA;
* aprovação para elevação;
* acesso Just-in-Time;
* gravação de sessão;
* auditoria centralizada;
* rotação de senha;
* solução PAM dedicada;
* restrição de logon interativo;
* remoção de privilégios permanentes.

## Conclusão

A etapa validou com sucesso a separação entre usuário comum e usuário administrativo.

O acesso privilegiado foi concedido de forma controlada por grupo, permitindo melhor rastreabilidade, manutenção e governança.


## Gerenciamento de administradores locais via GPO

Após a validação inicial manual, foi criada uma GPO para controlar de forma centralizada os administradores locais da estação `WIN10-01`.

### GPO criada

| Item | Valor |
|---|---|
| Nome da GPO | GPO-WIN10-LOCAL-ADMINS |
| Vinculada em | IAMLAB > Computadores |
| Escopo | Computadores da OU Computadores |
| Finalidade | Adicionar grupo privilegiado do domínio ao grupo local Administrators |

### Caminho da configuração

```text
Computer Configuration
└── Preferences
    └── Control Panel Settings
        └── Local Users and Groups

## Registro do acesso privilegiado no Microsoft Lists

Após a criação da GPO de administradores locais, o acesso privilegiado foi registrado no catálogo manual de IGA no Microsoft Lists.

### Registro criado

| Campo | Valor |
|---|---|
| Recurso | Administradores locais WIN10-01 |
| Caminho | `WIN10-01 > Local Group > Administrators` |
| Plataforma | Windows Server |
| Tipo de Recurso | Grupo Local Administrativo |
| Grupo de Acesso | `GG_ADMIN_SERVIDORES` |
| Grupo de Função | `Admins_TI` |
| Permissão | Admin |
| Dono do Recurso | Coordenador de TI |
| Aprovador | Coordenador de TI |
| Criticidade | Alta |
| Risco | Alto |
| Revisão Periódica | Mensal |
| Status | Ativo |

### Justificativa

Acesso administrativo local concedido via GPO para administração técnica controlada da estação `WIN10-01`.

### Conceito IGA + PAM aplicado

Este registro demonstra a união entre governança de identidade e controle de privilégio.

No Active Directory e na GPO, o acesso existe tecnicamente:

```text
GG_ADMIN_SERVIDORES
↓
Administrators local do WIN10-01
