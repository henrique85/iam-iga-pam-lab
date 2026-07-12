# 03 - Grupos e Permissões

## Objetivo

Esta etapa documenta a criação dos grupos de segurança e a aplicação do modelo **AGDLP** para controle de acesso a pastas compartilhadas no laboratório.

O objetivo é evitar permissões diretas em usuários e organizar o acesso de forma mais clara, auditável e escalável.

## Modelo utilizado

Foi utilizado o modelo **AGDLP**:

```text
Account → Global Group → Domain Local Group → Permission
```

Em português:

```text
Conta → Grupo Global → Grupo Local de Domínio → Permissão
```

## Conceito do modelo

O modelo separa responsabilidades:

| Camada             | Função                         |
| ------------------ | ------------------------------ |
| Account            | Usuário individual             |
| Global Group       | Representa área ou função      |
| Domain Local Group | Representa acesso a um recurso |
| Permission         | Permissão aplicada no recurso  |

Exemplo:

```text
joao.silva
↓
GG_TI_USUARIOS
↓
DL_PASTA_TI_RW
↓
Permissão Modify em C:\Dados\TI
```

## Grupos globais criados

Os grupos globais representam usuários por área ou função.

| Grupo Global           | Finalidade                          |
| ---------------------- | ----------------------------------- |
| GG_TI_USUARIOS         | Usuários da área de TI              |
| GG_TI_SUPORTE          | Usuários da função de suporte de TI |
| GG_FINANCEIRO_USUARIOS | Usuários da área Financeira         |
| GG_RH_USUARIOS         | Usuários da área de RH              |
| GG_OPERACOES_USUARIOS  | Usuários da área de Operações       |
| GG_DIRETORIA_USUARIOS  | Usuários da Diretoria               |

## Grupos locais de domínio criados

Os grupos locais de domínio representam permissões em recursos específicos.

| Grupo Local de Domínio | Recurso associado | Permissão |
| ---------------------- | ----------------- | --------- |
| DL_PASTA_TI_RW         | Pasta TI          | Modify    |
| DL_PASTA_FINANCEIRO_RW | Pasta Financeiro  | Modify    |
| DL_PASTA_RH_RW         | Pasta RH          | Modify    |
| DL_PASTA_OPERACOES_RW  | Pasta Operações   | Modify    |
| DL_PASTA_DIRETORIA_RW  | Pasta Diretoria   | Modify    |

## Associações AGDLP

Foram feitas as seguintes associações:

| Grupo Local de Domínio | Membro                 |
| ---------------------- | ---------------------- |
| DL_PASTA_TI_RW         | GG_TI_USUARIOS         |
| DL_PASTA_FINANCEIRO_RW | GG_FINANCEIRO_USUARIOS |
| DL_PASTA_RH_RW         | GG_RH_USUARIOS         |
| DL_PASTA_OPERACOES_RW  | GG_OPERACOES_USUARIOS  |
| DL_PASTA_DIRETORIA_RW  | GG_DIRETORIA_USUARIOS  |

## Usuários e grupos

| Usuário       | Grupo Global           |
| ------------- | ---------------------- |
| joao.silva    | GG_TI_USUARIOS         |
| carlos.lima   | GG_TI_USUARIOS         |
| maria.souza   | GG_FINANCEIRO_USUARIOS |
| paulo.costa   | GG_FINANCEIRO_USUARIOS |
| ana.ribeiro   | GG_RH_USUARIOS         |
| bruno.almeida | GG_OPERACOES_USUARIOS  |
| renata.moraes | GG_DIRETORIA_USUARIOS  |

## Compartilhamento SMB

Foi criado o compartilhamento:

```text
\\DC01\Dados
```

A pasta local correspondente é:

```text
C:\Dados
```

O protocolo utilizado para acesso pela rede é o **SMB**.

## Pastas criadas

```text
C:\Dados\TI
C:\Dados\Financeiro
C:\Dados\RH
C:\Dados\Operacoes
C:\Dados\Diretoria
```

## Permissões NTFS

As permissões reais foram aplicadas nas pastas via NTFS.

| Pasta               | Grupo com permissão    | Permissão |
| ------------------- | ---------------------- | --------- |
| C:\Dados\TI         | DL_PASTA_TI_RW         | Modify    |
| C:\Dados\Financeiro | DL_PASTA_FINANCEIRO_RW | Modify    |
| C:\Dados\RH         | DL_PASTA_RH_RW         | Modify    |
| C:\Dados\Operacoes  | DL_PASTA_OPERACOES_RW  | Modify    |
| C:\Dados\Diretoria  | DL_PASTA_DIRETORIA_RW  | Modify    |

Foram mantidos os grupos administrativos necessários, como:

* SYSTEM;
* Administrators.

## Diferença entre SMB e NTFS

| Item | Função                                                    |
| ---- | --------------------------------------------------------- |
| SMB  | Protocolo usado para acessar o compartilhamento pela rede |
| NTFS | Sistema de arquivos que controla permissões no disco      |

Resumo:

```text
SMB é o caminho pela rede.
NTFS é a fechadura no recurso.
AGDLP é o método para entregar a chave correta.
```

## Conceito IAM aplicado

Nesta etapa, foram praticados os conceitos de:

* autorização;
* controle de acesso baseado em grupo;
* menor privilégio;
* separação entre identidade, função e recurso;
* rastreabilidade de acesso;
* base para revisão periódica;
* base para IGA;
* base para PAM.

## Observação de segurança

Não foram aplicadas permissões diretamente em usuários.
Essa decisão melhora a governança, facilita auditoria e reduz o risco de acessos esquecidos ou inconsistentes.

