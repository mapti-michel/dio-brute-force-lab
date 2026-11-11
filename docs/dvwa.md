# Laboratório de Brute Force DVWA com Kali Linux e Medusa

## 1. Objetivo

Este laboratório tem como objetivo demonstrar, em um ambiente controlado, como realizar testes automatizados de autenticação contra a aplicação web DVWA (*Damn Vulnerable Web Application*), utilizando o Kali Linux e a ferramenta Medusa.

A atividade permite compreender como ferramentas automatizadas podem testar combinações de usuários e senhas em formulários de autenticação e demonstrar a importância de mecanismos de proteção contra tentativas indevidas de acesso.

## 2. Identificação do formulário de autenticação

Inicialmente, foi acessada a página de login do DVWA pelo navegador:

```text
http://192.168.x.x/dvwa/login.php
```

Foi realizado um teste manual utilizando credenciais aleatórias, resultando na mensagem `Login failed`.

Em seguida, utilizando as ferramentas de desenvolvedor do navegador (F12), foi acessada a aba **Network (Rede)** para inspecionar a requisição HTTP enviada durante a tentativa de autenticação.

Essa análise permitiu identificar informações importantes, como:

- Endereço da página de autenticação.
- Método HTTP utilizado.
- Parâmetros enviados pelo formulário.
- Mensagem retornada quando ocorre uma falha de autenticação.

## 3. Execução do teste com Medusa

Com as informações obtidas durante a inspeção do formulário, foi preparada a execução do Medusa utilizando o módulo HTTP.

As listas de usuários e senhas utilizadas foram as mesmas preparadas para o laboratório FTP.

**Comando utilizado:**

```bash
$ medusa -h 192.168.x.x -U users.txt -P pass.txt -M http \
  -m PAGE:'/dvwa/login.php' \
  -m FORM:'username=^USER^&password=^PASS^&login=Login' \
  -m 'FAIL=Loginb failed' -t 6
```

**Parâmetros utilizados:**

- `-h`: endereço IP da máquina alvo.
- `-U`: arquivo contendo possíveis usuários.
- `-P`: arquivo contendo possíveis senhas.
- `-M http`: módulo HTTP do Medusa.
- `-m`: parâmetros específicos utilizados pelo módulo HTTP.
- `-t`: número de tarefas concorrentes.

## 4. Análise dos resultados

Durante a execução, o Medusa realiza tentativas de autenticação utilizando as combinações fornecidas nos arquivos de usuários e senhas.

O resultado permite identificar se alguma combinação foi aceita pela aplicação.

A mensagem de sucesso deve ser interpretada considerando o comportamento da aplicação e validada por meio de uma autenticação no ambiente controlado.

## 5. Evidências

As capturas de tela registram a identificação do formulário de autenticação e a execução do teste com o Medusa.

- Identificação da página de login.
- Inspeção da requisição HTTP.
- Execução do teste automatizado.
- Resultado obtido.

## 6. Conclusão

O laboratório demonstrou como ferramentas de segurança podem ser utilizadas para avaliar a resistência de formulários web a tentativas automatizadas de autenticação.

A atividade também permitiu compreender a importância de implementar mecanismos como limitação de tentativas, bloqueio temporário, monitoramento de acessos e autenticação multifator.

---

**Aviso:** Os testes foram realizados exclusivamente em ambiente de laboratório, com autorização e finalidade educacional.