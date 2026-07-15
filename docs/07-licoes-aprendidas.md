# 07 - Lições Aprendidas

## Processo Mover - Mudança de Área

Esta etapa documenta a simulação de um processo **Mover** dentro do ciclo de vida de identidade.

No contexto de IAM e IGA, o processo **Mover** ocorre quando uma identidade muda de área, função, cargo ou responsabilidade dentro da organização. Essa mudança exige revisão dos acessos existentes, remoção de permissões antigas e concessão dos novos acessos necessários.

## Cenário

O usuário `joao.silva` pertencia inicialmente à área de TI.

Sua cadeia de acesso antes da mudança era:

```text
joao.silva
↓
GG_TI_USUARIOS
↓
DL_PASTA_TI_RW
↓
Permissão Modify em C:\Dados\TI
```

Isso permitia que o usuário acessasse a pasta compartilhada de TI:

```text
\\DC01\Dados\TI
```

Em seguida, foi simulada a transferência do usuário `joao.silva` da área de TI para a área Financeira.

## Objetivo do teste

Validar se a mudança de área resultaria em:

* remoção do acesso anterior à pasta de TI;
* concessão do novo acesso à pasta do Financeiro;
* atualização da localização do usuário na estrutura de OUs;
* validação prática do acesso no cliente Windows 10.

## Ações executadas no Active Directory

As seguintes alterações foram realizadas no usuário `joao.silva`:

| Ação                                                     | Resultado Esperado                        |
| -------------------------------------------------------- | ----------------------------------------- |
| Remover `joao.silva` do grupo `GG_TI_USUARIOS`           | Remover acesso à pasta TI                 |
| Adicionar `joao.silva` ao grupo `GG_FINANCEIRO_USUARIOS` | Conceder acesso à pasta Financeiro        |
| Mover `joao.silva` da OU `TI` para a OU `Financeiro`     | Atualizar a organização lógica do usuário |

Após a alteração, o usuário passou a ter a seguinte cadeia de acesso:

```text
joao.silva
↓
GG_FINANCEIRO_USUARIOS
↓
DL_PASTA_FINANCEIRO_RW
↓
Permissão Modify em C:\Dados\Financeiro
```

## Validação no Windows 10

No cliente `WIN10-01`, foi realizado novo logon com o usuário:

```text
IAMLAB\joao.silva
```

O novo logon foi necessário porque as associações de grupo do usuário são carregadas no momento da autenticação.

Foram realizados os seguintes testes:

| Recurso Testado           | Resultado        |
| ------------------------- | ---------------- |
| `\\DC01\Dados\TI`         | Acesso negado    |
| `\\DC01\Dados\Financeiro` | Acesso permitido |

Também foi criado um arquivo de teste na pasta Financeiro:

```text
teste-joao-mover-financeiro.txt
```

## Resultado

O teste foi validado com sucesso.

O usuário `joao.silva` perdeu o acesso à pasta de TI e passou a acessar apenas a pasta compatível com sua nova área.

Resultado final:

```text
Antes:
joao.silva → TI → acesso à pasta TI

Depois:
joao.silva → Financeiro → acesso à pasta Financeiro
```

## Conceito IGA aplicado

Este exercício representa um processo clássico de **Identity Governance and Administration**.

Em uma organização real, uma mudança de área não deve ser tratada apenas como uma solicitação de novo acesso. Ela deve ser tratada como uma revisão completa da identidade.

O ponto central é:

```text
Mover = conceder novo acesso + remover acesso antigo
```

Se apenas o novo acesso for concedido, o usuário acumula permissões antigas. Esse acúmulo é um risco comum em ambientes corporativos e pode gerar excesso de privilégio, falhas de auditoria e exposição indevida de informações.

## Diferença entre grupo e OU

Durante o processo, foi observada uma distinção importante:

| Elemento | Função                                          |
| -------- | ----------------------------------------------- |
| Grupo    | Define acesso e autorização                     |
| OU       | Define organização lógica e escopo de políticas |

A remoção e inclusão em grupos corrigiu o acesso do usuário.

A movimentação entre OUs corrigiu a organização lógica do objeto no Active Directory.

Ambas as ações são importantes para manter o ambiente coerente, auditável e preparado para aplicação de políticas por área.

## Lição aprendida

O processo Mover precisa ser tratado com cuidado porque é uma das principais fontes de privilégio acumulado dentro das organizações.

Um usuário que muda de área pode continuar com acessos antigos caso não exista revisão adequada. Esse tipo de falha costuma passar despercebido por meses ou anos.

A boa prática é sempre validar:

* quais acessos o usuário possuía antes;
* quais acessos devem ser removidos;
* quais novos acessos são necessários;
* quem aprovou a mudança;
* se a alteração foi executada corretamente;
* se o acesso foi testado após a mudança.

## Conclusão

A simulação confirmou que o modelo de acesso baseado em grupos funcionou corretamente no cenário de mudança de área.

O usuário recebeu o novo acesso necessário, perdeu o acesso anterior e foi movido para a OU correspondente à nova área.

Este exercício reforçou conceitos essenciais de IAM e IGA:

* ciclo de vida de identidade;
* processo Mover;
* menor privilégio;
* remoção de acesso antigo;
* autorização baseada em grupos;
* organização lógica no Active Directory;
* validação prática de acesso;
* governança de mudanças.
