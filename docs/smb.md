# Laboratório de Brute Force SMB com Kali Linux e Medusa

## 1. Objetivo

Este laboratório tem como objetivo demonstrar, em um ambiente controlado, como realizar reconhecimento, enumeração de informações e testes de autenticação contra o serviço SMB (*Server Message Block*), utilizando o Kali Linux e ferramentas como Nmap, enum4linux, smbclient e Medusa.

A atividade permite compreender como informações sobre usuários, serviços e compartilhamentos podem ser identificadas em uma rede, além de demonstrar os riscos associados a configurações inadequadas de acesso e senhas fracas.

## 2. Reconhecimento do alvo com Nmap

Inicialmente, foi realizada uma varredura para identificar as portas utilizadas pelo serviço SMB e suas versões.

```bash
nmap -Pn -p 139,445 -sV --script smb-protocols 192.168.x.x
```

**Parâmetros utilizados:**

- `-Pn`: considera o host ativo, sem realizar descoberta por ping.
- `-p 139,445`: verifica as portas NetBIOS e SMB.
- `-sV`: identifica versões dos serviços.
- `--script smb-protocols`: identifica os protocolos SMB suportados pelo servidor.

O resultado identificou o suporte ao protocolo SMBv1, apresentado pelo Nmap como NT LM 0.12.

## 3. Enumeração com enum4linux

Em seguida, foi utilizada a ferramenta enum4linux para coletar informações disponibilizadas pelo serviço SMB.

```bash
enum4linux -a 192.168.x.x | tee enum4_output.txt
```

**Parâmetros utilizados:**

- `-a`: executa um conjunto amplo de verificações de enumeração.
- `tee`: exibe o resultado no terminal e salva uma cópia em arquivo.

Durante a execução, foi possível identificar informações como o grupo de trabalho (`WORKGROUP`) e o nome do servidor (`METASPLOITABLE`).

O resultado completo da execução foi armazenado no arquivo `enum4_output.txt` para consulta e documentação.

## 4. Diagnóstico de compatibilidade SMB

Inicialmente, a ferramenta smbclient apresentou o erro `NT_STATUS_CONNECTION_DISCONNECTED`.

Após identificar que o servidor Metasploitable 2 utilizava SMBv1 (NT LM 0.12), foi realizado um teste controlado especificando o protocolo NT1.

```bash
smbclient -L //192.168.x.x -N \
  --option='client min protocol=NT1' \
  --option='client max protocol=NT1'
```

A negociação foi estabelecida com sucesso, permitindo identificar os compartilhamentos disponíveis por meio de uma sessão anônima.

Os compartilhamentos identificados incluíram `print$`, `tmp`, `opt`, `IPC$` e `ADMIN$`.

A listagem dos compartilhamentos não significa que todos tenham permissões de leitura ou escrita.

### 4.1. Análise do comportamento das ferramentas

Durante os testes, foram observados resultados diferentes entre as ferramentas utilizadas.

A ferramenta enum4linux não conseguiu estabelecer uma sessão SMB anônima durante sua verificação. Entretanto, o smbclient conseguiu listar os compartilhamentos disponíveis utilizando acesso anônimo e negociação explícita do protocolo SMB1.

Essa diferença demonstra que os resultados de enumeração podem variar conforme a ferramenta, o método de conexão e a negociação do protocolo utilizada.

## 5. Considerações de segurança

Os testes evidenciaram alguns riscos associados à configuração do serviço SMB:

- **SMBv1:** protocolo legado, obsoleto e associado a vulnerabilidades conhecidas. Seu uso deve ser evitado.
- **Acesso anônimo:** permitir a listagem de compartilhamentos sem autenticação pode expor informações da infraestrutura.
- **Compartilhamentos:** permissões inadequadas de leitura e escrita podem facilitar o acesso não autorizado a arquivos.
- **Senhas fracas:** credenciais previsíveis aumentam o risco de ataques de força bruta e comprometimento de contas.

Como medidas de proteção, recomenda-se desabilitar SMBv1, restringir o acesso aos compartilhamentos, exigir autenticação, aplicar políticas de senhas fortes e monitorar tentativas de acesso.

## 6. Conclusão

O laboratório permitiu praticar atividades de reconhecimento e enumeração do serviço SMB, identificar protocolos suportados pelo servidor, analisar o comportamento de diferentes ferramentas e investigar problemas de compatibilidade entre clientes e servidores.

A experiência também demonstrou a importância de compreender os resultados obtidos durante os testes, em vez de apenas executar comandos, relacionando as evidências encontradas aos riscos de segurança da infraestrutura.

**Observação:** todos os testes foram realizados em ambiente de laboratório controlado, com máquinas virtuais destinadas à prática de segurança da informação.