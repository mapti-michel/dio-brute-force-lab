# Laboratório de Brute Force FTP com Kali Linux e Medusa

## 1. Objetivo

Este laboratório tem como objetivo demonstrar, em um ambiente controlado, como realizar testes de força bruta contra um serviço FTP utilizando o Kali Linux e a ferramenta Medusa.

A atividade permite compreender como ferramentas automatizadas podem testar combinações de usuários e senhas, além de demonstrar a importância de implementar mecanismos de proteção na autenticação.

## 2. Reconhecimento do alvo

Inicialmente, foi realizada uma varredura com o Nmap para identificar portas abertas e serviços disponíveis na máquina alvo.

```bash
nmap -sV -p 21,22,80,139,445 192.168.x.x
```

**Parâmetros utilizados:**

- `-sV`: identifica as versões dos serviços disponíveis.
- `-p`: especifica as portas que serão verificadas.

A porta 21 corresponde ao serviço FTP.

## 3. Criação das listas de usuários e senhas

Para realizar os testes, foram criados dois arquivos contendo possíveis usuários e senhas.

**Lista de usuários:**

```bash
echo -e 'users\nmsfadmin\nadmin\nroot' > users.txt
```

**Lista de senhas:**

```bash
echo -e '123456\npassword\nqwerty\nmsfadmin' > pass.txt
```

Os arquivos serão utilizados como entrada para a ferramenta Medusa.

## 4. Execução do teste com Medusa

Com as listas preparadas, foi executado o teste de autenticação contra o serviço FTP.

```bash
medusa -h 192.168.x.x -U users.txt -P pass.txt -M ftp -t 6
```

**Parâmetros utilizados:**

| Parâmetro | Descrição |
|---|---|
| `-h` | Endereço IP da máquina alvo. |
| `-U` | Arquivo contendo a lista de usuários. |
| `-P` | Arquivo contendo a lista de senhas. |
| `-M` | Módulo utilizado (FTP). |
| `-t` | Número de tarefas concorrentes. |

## 5. Análise dos resultados

Durante a execução, a ferramenta Medusa testa as combinações de usuários e senhas fornecidas.

Quando uma combinação válida é identificada, o resultado apresenta uma indicação de sucesso (`SUCCESS`).

Essa informação permite identificar credenciais que possibilitam a autenticação no serviço FTP do ambiente de laboratório.

Após a identificação, a autenticação pode ser validada por meio de uma conexão FTP autorizada.

## 6. Evidências

As imagens abaixo registram as etapas realizadas durante o laboratório:

- **Reconhecimento:** `images/bf_ftp1.jpg`
- **Execução do teste:** `images/bf_ftp2.jpg`

## 7. Conclusão

A atividade demonstrou como ferramentas de auditoria, como Nmap e Medusa, podem ser utilizadas para avaliar a segurança de serviços FTP.

O exercício também permitiu compreender os riscos associados ao uso de senhas fracas e à ausência de mecanismos de proteção contra tentativas automatizadas de autenticação.

Entre as medidas de mitigação, destacam-se:

- Utilização de senhas fortes e exclusivas.
- Desativação do acesso anônimo quando não necessário.
- Restrição de tentativas de autenticação.
- Monitoramento de logs e tentativas suspeitas.
- Preferência por protocolos seguros, como SFTP, em substituição ao FTP tradicional.

---

**Aviso:** Este procedimento foi realizado exclusivamente em ambiente de laboratório, com máquinas virtuais destinadas a fins educacionais. A execução de testes de segurança deve ocorrer somente em sistemas próprios ou com autorização expressa de seus responsáveis.
