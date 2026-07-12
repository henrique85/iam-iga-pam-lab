# 04 - Microsoft Lists como IGA Manual

## Objetivo

Esta etapa documenta o uso do **Microsoft Lists** como catálogo manual de acessos para o laboratório de IAM, IGA e PAM.

Como o ambiente não utiliza inicialmente uma ferramenta dedicada de IGA, como midPoint, SailPoint ou Microsoft Entra ID Governance, o Microsoft Lists foi utilizado para registrar, organizar e acompanhar os acessos concedidos no laboratório.

## Função do catálogo de acessos

O catálogo de acessos tem como objetivo responder perguntas essenciais de governança:

* Qual recurso existe?
* Quem é o dono do recurso?
* Qual grupo concede acesso?
* Qual grupo representa a função do usuário?
* Qual permissão foi concedida?
* Qual é a criticidade do recurso?
* Qual é o risco do acesso?
* Quem aprova esse acesso?
* Quando o acesso deve ser revisado?
* O acesso ainda está ativo?

## Lista criada

| Item           | Valor                                    |
| -------------- | ---------------------------------------- |
| Nome da lista  | Catálogo de Acessos IAM Lab              |
| Plataforma     | Microsoft Lists                          |
| Finalidade     | Catálogo manual de governança de acessos |
| Escopo inicial | Pastas compartilhadas do laboratório     |

## Colunas utilizadas

| Coluna            | Finalidade                            |
| ----------------- | ------------------------------------- |
| Recurso           | Nome amigável do recurso              |
| Caminho           | Caminho técnico do recurso            |
| Plataforma        | Ambiente onde o recurso existe        |
| Tipo de Recurso   | Classificação do recurso              |
| Grupo de Acesso   | Grupo que recebe permissão no recurso |
| Grupo de Função   | Grupo que representa área ou função   |
| Permissão         | Nível de acesso concedido             |
| Dono do Recurso   | Responsável pelo recurso              |
| Aprovador         | Responsável por aprovar o acesso      |
| Criticidade       | Importância do recurso para o negócio |
| Risco             | Nível de risco associado ao acesso    |
| Revisão Periódica | Frequência de revisão do acesso       |
| Próxima Revisão   | Data planejada para nova análise      |
| Status            | Situação atual do acesso              |
| Justificativa     | Motivo para existência do acesso      |

## Registros iniciais cadastrados

| Recurso          | Caminho                   | Grupo de Acesso          | Grupo de Função          | Permissão | Criticidade | Risco   |
| ---------------- | ------------------------- | ------------------------ | ------------------------ | --------- | ----------- | ------- |
| Pasta TI         | `\\DC01\Dados\TI`         | `DL_PASTA_TI_RW`         | `GG_TI_USUARIOS`         | Modify    | Média       | Médio   |
| Pasta Financeiro | `\\DC01\Dados\Financeiro` | `DL_PASTA_FINANCEIRO_RW` | `GG_FINANCEIRO_USUARIOS` | Modify    | Alta        | Alto    |
| Pasta RH         | `\\DC01\Dados\RH`         | `DL_PASTA_RH_RW`         | `GG_RH_USUARIOS`         | Modify    | Alta        | Alto    |
| Pasta Operações  | `\\DC01\Dados\Operacoes`  | `DL_PASTA_OPERACOES_RW`  | `GG_OPERACOES_USUARIOS`  | Modify    | Média       | Médio   |
| Pasta Diretoria  | `\\DC01\Dados\Diretoria`  | `DL_PASTA_DIRETORIA_RW`  | `GG_DIRETORIA_USUARIOS`  | Modify    | Crítica     | Crítico |

## Conceito IGA aplicado

Nesta etapa, o acesso deixou de ser apenas uma configuração técnica no Active Directory e passou a ter contexto de governança.

O catálogo permite associar cada acesso a:

* um recurso;
* um grupo técnico;
* uma função de negócio;
* um dono;
* um aprovador;
* uma criticidade;
* um risco;
* uma periodicidade de revisão.

## Diferença entre IAM e IGA nesta etapa

O **IAM** aparece na configuração técnica:

```text
Usuário → Grupo Global → Grupo Local de Domínio → Permissão NTFS
```

O **IGA** aparece na governança:

```text
Acesso → Dono → Aprovador → Justificativa → Risco → Revisão
```

## Observação

O Microsoft Lists não substitui uma solução corporativa completa de IGA. Porém, para laboratório, pequenas empresas ou fase inicial de maturidade, ele permite criar uma base de controle, rastreabilidade e revisão de acessos.

