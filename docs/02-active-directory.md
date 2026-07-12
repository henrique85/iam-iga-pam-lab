# 02 - Active Directory

## Objetivo

Esta etapa documenta a criação da fundação de identidade do laboratório utilizando **Active Directory Domain Services** no Windows Server 2022.

O Active Directory será utilizado como diretório central para usuários, grupos, computadores, autenticação e autorização.

## Servidor Domain Controller

| Item                | Valor               |
| ------------------- | ------------------- |
| Hostname            | DC01                |
| Sistema Operacional | Windows Server 2022 |
| Função              | Domain Controller   |
| Domínio             | iamlab.local        |
| NetBIOS             | IAMLAB              |
| IP                  | 192.168.100.10      |
| DNS Primário        | 192.168.100.10      |
| Gateway             | Não configurado     |

## Funções instaladas

No servidor DC01, foi instalada a função:

* Active Directory Domain Services;
* DNS Server.

O servidor foi promovido como o primeiro Domain Controller de uma nova floresta.

## Floresta e domínio

| Item              | Valor             |
| ----------------- | ----------------- |
| Forest            | iamlab.local      |
| Domain            | iamlab.local      |
| Domain Mode       | Windows2016Domain |
| Domain Controller | DC01.iamlab.local |
| Global Catalog    | Sim               |

## Estrutura de OUs

Foi criada uma estrutura lógica para organizar os objetos do domínio.

```text
iamlab.local
└── IAMLAB
    ├── Usuarios
    │   ├── TI
    │   ├── Financeiro
    │   ├── RH
    │   ├── Operacoes
    │   └── Diretoria
    │
    ├── Grupos
    │   ├── Globais
    │   ├── Locais_Dominio
    │   └── Privilegiados
    │
    ├── Computadores
    ├── Administrativos
    ├── Servidores
    └── Testes_IGA_PAM
```

## Usuários criados

| Usuário       | Área       |
| ------------- | ---------- |
| joao.silva    | TI         |
| carlos.lima   | TI         |
| maria.souza   | Financeiro |
| paulo.costa   | Financeiro |
| ana.ribeiro   | RH         |
| bruno.almeida | Operações  |
| renata.moraes | Diretoria  |

## Observação de laboratório

As contas foram criadas com configuração simplificada para ambiente de estudo. Em ambiente corporativo real, devem ser aplicadas políticas adequadas de senha, expiração, MFA, bloqueio e auditoria.

## Conceito IAM aplicado

O Active Directory atua como o diretório central de identidades do laboratório.

Nesta etapa, foram praticados os conceitos de:

* identidade;
* autenticação;
* organização lógica de objetos;
* separação por áreas;
* base para autorização via grupos;
* fundação para governança de acessos.

