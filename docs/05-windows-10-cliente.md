# 05 - Windows 10 Cliente

## Objetivo

Esta etapa documenta a criação, configuração e ingresso da estação Windows 10 no domínio `iamlab.local`, além dos testes práticos de autenticação e autorização com usuários do Active Directory.

## Máquina cliente

| Item                | Valor                   |
| ------------------- | ----------------------- |
| Hostname            | WIN10-01                |
| Sistema Operacional | Windows 10 Pro          |
| Tipo de rede        | Rede Interna VirtualBox |
| Rede                | LAB-IAM                 |
| IP                  | 192.168.100.20          |
| Máscara             | 255.255.255.0           |
| Gateway             | Não configurado         |
| DNS Primário        | 192.168.100.10          |
| Domínio             | iamlab.local            |

## Configuração de rede

A estação foi configurada para utilizar o DC01 como servidor DNS.

```text
DNS Primário: 192.168.100.10
```

Essa configuração é essencial para que a estação consiga localizar o domínio e o Domain Controller.

## Validação de comunicação

Foram realizados testes de comunicação com o DC01:

```cmd
ping 192.168.100.10
ping DC01
nslookup iamlab.local
```

Após correção do DNS na estação, o domínio `iamlab.local` passou a ser resolvido corretamente pelo DC01.

## Ingresso no domínio

A estação `WIN10-01` foi ingressada no domínio:

```text
iamlab.local
```

Após o ingresso, o computador foi movido no Active Directory para a OU:

```text
IAMLAB > Computadores
```

## Conceito IAM aplicado

Ao ingressar no domínio, o computador passou a possuir uma identidade própria no Active Directory.

Essa identidade é representada por uma conta de computador:

```text
WIN10-01$
```

A partir disso, a estação pode receber políticas, auditoria, configurações de segurança e controle administrativo por meio do domínio.

## Teste de autenticação

Foi realizado login na estação com o usuário de domínio:

```text
IAMLAB\joao.silva
```

O comando abaixo validou a identidade autenticada:

```cmd
whoami
```

Resultado esperado:

```text
iamlab\joao.silva
```

## Teste de autorização - Usuário João Silva

Usuário testado:

```text
IAMLAB\joao.silva
```

Grupo de função:

```text
GG_TI_USUARIOS
```

Cadeia de acesso:

```text
joao.silva
↓
GG_TI_USUARIOS
↓
DL_PASTA_TI_RW
↓
Modify em C:\Dados\TI
```

### Resultado

| Recurso                   | Resultado        |
| ------------------------- | ---------------- |
| `\\DC01\Dados\TI`         | Acesso permitido |
| `\\DC01\Dados\Financeiro` | Acesso negado    |

O usuário conseguiu criar arquivo de teste na pasta TI:

```text
teste-joao-ti.txt
```

## Teste de autorização - Usuária Maria Souza

Usuária testada:

```text
IAMLAB\maria.souza
```

Grupo de função:

```text
GG_FINANCEIRO_USUARIOS
```

Cadeia de acesso:

```text
maria.souza
↓
GG_FINANCEIRO_USUARIOS
↓
DL_PASTA_FINANCEIRO_RW
↓
Modify em C:\Dados\Financeiro
```

### Resultado

| Recurso                   | Resultado        |
| ------------------------- | ---------------- |
| `\\DC01\Dados\Financeiro` | Acesso permitido |
| `\\DC01\Dados\TI`         | Acesso negado    |

A usuária conseguiu criar arquivo de teste na pasta Financeiro:

```text
teste-maria-financeiro.txt
```

## Conclusão dos testes

Os testes confirmaram que o modelo de acesso baseado em grupos está funcionando corretamente.

Resultados validados:

* Usuário de TI acessa apenas recurso de TI;
* Usuário do Financeiro acessa apenas recurso do Financeiro;
* Acesso indevido entre áreas foi negado;
* O modelo AGDLP funcionou conforme esperado;
* As permissões NTFS foram aplicadas corretamente.

## Conceitos praticados

Nesta etapa, foram praticados:

* autenticação centralizada;
* identidade de usuário no domínio;
* identidade de computador no domínio;
* autorização baseada em grupo;
* negação de acesso indevido;
* validação prática de permissões;
* princípio do menor privilégio;
* evidência técnica de controle de acesso.

## Observação

Controle de acesso não deve ser considerado válido apenas porque foi configurado.
Ele precisa ser testado e evidenciado.

Nesta etapa, o acesso foi validado tanto pelo caminho positivo quanto pelo caminho negativo.
