# iam-iga-pam-lab
Laboratório prático de IAM, IGA e PAM com Active Directory, Microsoft Lists, Windows, Linux e ferramentas open-source.

# IAM, IGA e PAM Lab

Laboratório prático para estudo de **Identity and Access Management**, **Identity Governance and Administration** e **Privileged Access Management**, simulando um ambiente corporativo com Active Directory, Windows, Linux e ferramentas de segurança.

## Objetivo

Construir um ambiente de laboratório para compreender, praticar e documentar conceitos essenciais de identidade, autenticação, autorização, governança de acessos, controle de privilégios e auditoria.

Este projeto tem como foco o aprendizado prático, a documentação técnica e a criação de evidências para portfólio profissional na área de Segurança da Informação com ênfase em IAM.

## Tecnologias utilizadas

* Windows Server 2022
* Active Directory Domain Services
* DNS
* Windows 10 Pro
* Microsoft Lists
* VirtualBox
* Ubuntu Server 24.04 LTS
* Ubuntu Desktop 24.04 LTS

## Ferramentas previstas para fases futuras

* Microsoft Entra ID
* LAPS
* Keycloak
* midPoint
* OpenLDAP
* FreeIPA
* HashiCorp Vault
* Teleport Community
* Apache Guacamole

## Escopo inicial

Nesta primeira fase, o laboratório cobre:

* Criação de domínio Active Directory
* Criação de usuários e grupos
* Modelo AGDLP
* Permissões NTFS
* Compartilhamento SMB
* Catálogo de acessos em Microsoft Lists
* Documentação técnica no GitHub

## Ambiente inicial

| Componente      | Função                                           |
| --------------- | ------------------------------------------------ |
| DC01            | Domain Controller, DNS e Active Directory        |
| WIN10-01        | Estação cliente ingressada no domínio            |
| Microsoft Lists | Catálogo manual de governança de acessos         |
| GitHub          | Documentação técnica e evidências do laboratório |

## Domínio utilizado

```text
iamlab.local
```

## Rede do laboratório

```text
Rede Interna VirtualBox: LAB-IAM
DC01: 192.168.100.10
WIN10-01: 192.168.100.20
```

## Conceitos estudados

* Identidade
* Autenticação
* Autorização
* Princípio do menor privilégio
* Grupos globais
* Grupos locais de domínio
* Permissões NTFS
* Compartilhamento SMB
* Modelo AGDLP
* Catálogo de acessos
* Revisão periódica de acessos
* Base conceitual de IGA
* Base conceitual de PAM

## Observação de segurança

Este laboratório utiliza dados fictícios, usuários fictícios, domínio fictício e endereços IP privados de ambiente isolado. Nenhuma informação sensível de ambiente corporativo real deve ser publicada neste repositório.
