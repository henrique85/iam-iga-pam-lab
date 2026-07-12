# 01 - Visão Geral do Laboratório

## Objetivo do laboratório

Este laboratório tem como objetivo estudar, praticar e documentar conceitos de **IAM**, **IGA** e **PAM** em um ambiente controlado, simulando práticas comuns em organizações corporativas.

A proposta é construir a fundação técnica com Active Directory, usuários, grupos, permissões, compartilhamentos e auditoria, evoluindo depois para governança de acessos, controle de privilégios e ferramentas especializadas.

## Escopo inicial

A primeira fase do laboratório cobre:

* Criação de um domínio Active Directory;
* Configuração de um Domain Controller;
* Criação de usuários por área;
* Criação de grupos globais;
* Criação de grupos locais de domínio;
* Aplicação do modelo AGDLP;
* Criação de pastas compartilhadas;
* Configuração de permissões NTFS;
* Criação de catálogo de acessos no Microsoft Lists;
* Documentação técnica no GitHub.

## Ambiente virtual

| Máquina      | Sistema Operacional      | Função                                    |
| ------------ | ------------------------ | ----------------------------------------- |
| DC01         | Windows Server 2022      | Domain Controller, DNS e Active Directory |
| WIN10-01     | Windows 10 Pro           | Cliente Windows ingressado no domínio     |
| UBUNTU-SRV01 | Ubuntu Server 24.04 LTS  | Servidor Linux para ferramentas futuras   |
| UBUNTU-CLI01 | Ubuntu Desktop 24.04 LTS | Cliente Linux para testes futuros         |

## Rede do laboratório

| Item               | Valor          |
| ------------------ | -------------- |
| VirtualBox Network | LAB-IAM        |
| Tipo de rede       | Rede Interna   |
| Domínio            | iamlab.local   |
| NetBIOS            | IAMLAB         |
| DC01               | 192.168.100.10 |
| WIN10-01           | 192.168.100.20 |

## Conceitos principais

### IAM

IAM significa **Identity and Access Management**.

O foco é controlar:

* quem é a identidade;
* como ela se autentica;
* quais acessos possui;
* quais recursos pode utilizar.

### IGA

IGA significa **Identity Governance and Administration**.

O foco é governar:

* quem aprovou o acesso;
* por qual motivo o acesso existe;
* quando ele deve ser revisado;
* se o acesso ainda é necessário;
* se há risco ou privilégio excessivo.

### PAM

PAM significa **Privileged Access Management**.

O foco é controlar acessos privilegiados, como:

* administradores de domínio;
* administradores locais;
* contas de serviço;
* contas com acesso sensível;
* sessões administrativas.

## Princípio orientador

O laboratório segue o princípio do menor privilégio:

> Cada identidade deve possuir apenas o acesso necessário para executar sua função, pelo tempo necessário e com possibilidade de auditoria.

## Observação

Todos os nomes, usuários, endereços IP e domínios utilizados neste laboratório são fictícios e destinados exclusivamente ao estudo.
