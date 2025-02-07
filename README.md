# Configuração Completa do SSH com GitHub no Google Cloud Shell

Este guia detalhado fornece um passo a passo para configurar e usar o SSH no Google Cloud Shell, garantindo uma conexão segura com o GitHub. Inclui todos os comandos necessários e melhores práticas para evitar problemas comuns.

---

## **1️⃣ Verificando o Ambiente e Atualizando o Sistema**

Antes de começar, verifique se o Google Cloud Shell está ativo e atualizado:

```sh
sudo apt update && sudo apt upgrade -y
```

Isso garantirá que todas as dependências necessárias estejam disponíveis.

Verifique se o Git está instalado:

```sh
git --version
```

Se o Git não estiver instalado, instale-o com:

```sh
sudo apt install git -y
```

---

## **2️⃣ Gerar uma Nova Chave SSH**

Abra o Google Cloud Shell e execute o comando abaixo para gerar uma nova chave SSH:

```sh
ssh-keygen -t rsa -b 4096 -C "seu-email@example.com"
```

Explicação dos parâmetros:
- **`-t rsa`**: Define o tipo de chave (RSA).
- **`-b 4096`**: Define o tamanho da chave (4096 bits para mais segurança).
- **`-C "seu-email@example.com"`**: Adiciona um comentário à chave, geralmente seu e-mail GitHub.

Quando solicitado, pressione **ENTER** para aceitar o local padrão (`/home/seu-usuario/.ssh/id_rsa`).

Se desejar, você pode definir uma senha para proteger a chave ou pressionar **ENTER** para deixá-la sem senha.

---

## **3️⃣ Iniciar o SSH-Agent e Adicionar a Chave**

O `ssh-agent` ajuda a gerenciar chaves SSH em segundo plano. Ative-o com:

```sh
eval "$(ssh-agent -s)"
```

Agora, adicione a chave gerada ao agente:

```sh
ssh-add ~/.ssh/id_rsa
```

Para verificar se a chave foi adicionada corretamente, execute:

```sh
ssh-add -l
```

Se a saída exibir um identificador da chave, significa que ela foi carregada corretamente.

---

## **4️⃣ Copiar a Chave Pública e Adicionar ao GitHub**

Agora, copie a chave pública para adicioná-la ao GitHub:

```sh
cat ~/.ssh/id_rsa.pub
```

Isso imprimirá algo semelhante a:

```
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQ... seu-email@example.com
```

**Copie todo esse conteúdo**.

Agora, siga os passos para adicioná-la ao GitHub:
1. Acesse [GitHub - SSH Keys](https://github.com/settings/keys).
2. Clique em **New SSH Key**.
3. No campo **Title**, insira um nome descritivo (ex: `Google Cloud Shell`).
4. No campo **Key**, cole a chave copiada.
5. Clique em **Add SSH Key**.

---

## **5️⃣ Testar a Conexão SSH com o GitHub**

Agora, vamos testar a conexão para garantir que o GitHub reconhece sua chave SSH:

```sh
ssh -T git@github.com
```

Se tudo estiver correto, a saída será:

```
Hi <seu-usuario>! You've successfully authenticated, but GitHub does not provide shell access.
```

Se houver um erro **Permission denied (publickey)**, volte e verifique se a chave correta foi adicionada ao GitHub.

---

## **6️⃣ Configurar o Git para Usar o SSH**

Caso ainda não tenha configurado seu usuário e e-mail no Git, faça isso agora:

```sh
git config --global user.name "Seu Nome"
git config --global user.email "seu-email@example.com"
```

Verifique as configurações:

```sh
git config --list
```

---

## **7️⃣ Ajustar Permissões das Chaves SSH**

O SSH pode recusar a autenticação se as permissões estiverem incorretas. Ajuste-as com:

```sh
chmod 600 ~/.ssh/id_rsa
chmod 644 ~/.ssh/id_rsa.pub
chmod 700 ~/.ssh
```

Isso garante que apenas você tenha acesso às chaves.

---

## **8️⃣ Configurar o Arquivo SSH para Garantir Conexão Estável**

Podemos configurar o SSH para sempre usar a chave correta ao conectar-se ao GitHub.

Edite (ou crie) o arquivo de configuração SSH:

```sh
nano ~/.ssh/config
```

Adicione o seguinte conteúdo:

```
Host github.com
  User git
  IdentityFile ~/.ssh/id_rsa
  AddKeysToAgent yes
```

Salve e saia (`CTRL+X`, `Y`, `ENTER`).

Reinicie o `ssh-agent` e adicione a chave novamente:

```sh
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Teste novamente a conexão:

```sh
ssh -T git@github.com
```

---

## **9️⃣ Clonar um Repositório via SSH**

Agora, você pode clonar um repositório GitHub sem precisar inserir usuário e senha:

```sh
git clone git@github.com:seu-usuario/seu-repositorio.git
```

Se já tiver um repositório local e quiser alterar a URL remota de HTTPS para SSH:

```sh
git remote set-url origin git@github.com:seu-usuario/seu-repositorio.git
```

Para verificar se a URL remota está correta:

```sh
git remote -v
```

---

## **🔟 Solução de Problemas**

### **Erro `Permission denied (publickey)` ao testar conexão**

- Verifique se a chave pública correta está no GitHub.
- Certifique-se de que a chave privada está carregada no `ssh-agent`.
- Tente rodar:

  ```sh
  ssh-add ~/.ssh/id_rsa
  ```

- Verifique se a permissão do arquivo da chave está correta (`chmod 600 ~/.ssh/id_rsa`).

### **Erro `Could not open a connection to your authentication agent`**

- Inicie o `ssh-agent` novamente:

  ```sh
  eval "$(ssh-agent -s)"
  ```

- Depois, adicione a chave:

  ```sh
  ssh-add ~/.ssh/id_rsa
  ```

---

## **✅ Conclusão**

Parabéns! Agora você configurou e autenticou seu ambiente de desenvolvimento no Google Cloud Shell com GitHub via SSH. 🚀

Caso tenha alguma dúvida, sinta-se à vontade para perguntar! 😊

