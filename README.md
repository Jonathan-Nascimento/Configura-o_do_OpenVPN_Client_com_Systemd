
# Configuração do OpenVPN Client com Systemd

Este guia descreve como configurar o OpenVPN Client no Linux utilizando Systemd.




## 1. Instalando o OpenVPN e o Gerenciador de Rede

Instale os pacotes necessários:

```bash
sudo apt install openvpn network-manager -y
```

---

## 2. Adicionando o arquivo de configuração `.ovpn`

Copie o arquivo de configuração `.ovpn` para o diretório apropriado:

```bash
sudo cp SEU_ARQUIVO.ovpn /etc/openvpn/client/
```

---

## 3. Criando o arquivo de autenticação

O OpenVPN permite utilizar um arquivo que contenha as credenciais de autenticação.

### 3.1. Crie o arquivo `auth.txt`:

```bash
sudo vim /etc/openvpn/client/auth.txt
```

Adicione o conteúdo no formato abaixo:

```
SEU_USUARIO
SUA_SENHA
```

> **Exemplo:**
>
> ```
> usuario
> senha_secreta
> ```

### 3.2. Proteger o arquivo de autenticação:

Para proteger suas credenciais, ajuste as permissões:

```bash
sudo chmod 600 /etc/openvpn/client/auth.txt
```

---

## 4. Configurando o arquivo `.ovpn`

Edite o arquivo de configuração `.ovpn` para utilizar o arquivo de autenticação:

```bash
sudo vim /etc/openvpn/client/SEU_ARQUIVO.ovpn
```

Adicione ou edite a seguinte linha:

```ovpn
auth-user-pass /etc/openvpn/client/auth.txt
```

---

## 5. Criando o serviço Systemd

Para gerenciar a conexão do OpenVPN como um serviço, crie um arquivo de serviço:

```bash
sudo vim /etc/systemd/system/openvpn-client@NOME_DA_CONEXAO.service
```

Adicione o seguinte conteúdo:

```systemd
[Unit]
Description=OpenVPN connection to %i
After=network.target

[Service]
ExecStart=openvpn --config /etc/openvpn/client/NOME_DA_CONEXAO.ovpn
WorkingDirectory=/etc/openvpn/client
Restart=always
User=root
CapabilityBoundingSet=CAP_NET_ADMIN CAP_NET_BIND_SERVICE
ProtectSystem=full
ProtectHome=read-only
LimitNPROC=10

[Install]
WantedBy=multi-user.target
```

> Substitua `NOME_DA_CONEXAO` pelo nome desejado para identificar a conexão e garanta que o arquivo `.ovpn` exista no caminho configurado.

---

## 6. Iniciando e gerenciando o serviço

### 6.1. Inicie o serviço:

```bash
sudo systemctl start openvpn-client@NOME_DA_CONEXAO
```

### 6.2. Verifique o status:

```bash
sudo systemctl status openvpn-client@NOME_DA_CONEXAO
```

### 6.3. Habilite o serviço no boot (opcional):

```bash
sudo systemctl enable openvpn-client@NOME_DA_CONEXAO
```

---

## Observações

- Certifique-se de que os arquivos no diretório `/etc/openvpn/client/` têm as permissões adequadas.
- Caso encontre problemas, revise os logs usando o comando:

```bash
journalctl -u openvpn-client@NOME_DA_CONEXAO
```

---

## Segurança

- Proteja arquivos sensíveis como o `auth.txt` e o arquivo de configuração `.ovpn` utilizando permissões restritas.
- Sempre use conexões seguras para transferir arquivos de configuração entre dispositivos.

---

## Contribuições

Contribuições são bem-vindas! Sinta-se à vontade para abrir issues ou enviar pull requests.


