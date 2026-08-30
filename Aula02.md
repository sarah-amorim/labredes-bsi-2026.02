# Identificação:
- **Título da prática:** Prática de Gerenciamento de Usuários, Grupos e Permissões em Servidor Linux
- **Nome completo:** Sarah Amorim dos Santos
- **Matrícula:** 2024014201
- **Curso:** Sistemas de Informação
- **Turma:** 2026.2
- **Data:** 26/08/2026

> **Observação Importante:** Esta atividade prática foi desenvolvida em conjunto (em dupla) com o aluno Pedro Henrique Cavalcante Rocha. Devido a isso, o ambiente de laboratório compartilhado foi configurado na máquina virtual dele. Portanto, nas capturas de tela e registros do terminal abaixo, o usuário e host exibidos constam como `vboxuser@pedrocha`.

# Objetivo: 
Esta atividade prática teve como propósito configurar e validar mecanismos de controle de acesso no sistema operacional Ubuntu Server. O foco principal foi estruturar a gestão de usuários e grupos departamentais, montar diretórios compartilhados no sistema de arquivos (`/srv/`) e aplicar o sistema de permissões octais (como `770` para diretórios e `660` para arquivos). Com isso, pretendeu-se garantir o isolamento adequado dos dados, permitindo que apenas os membros autorizados de cada equipe acessassem os conteúdos de seus respectivos setores.

# Ambiente: 
- **Sistema Operacional Hospedeiro:** Windows 11
- **Software de Virtualização (Hipervisor):** Oracle VM VirtualBox
- **Sistema Operacional Convidado (Guest):** Ubuntu Server (Distribuição Linux Server)
- **Usuário com Privilégios (Sudoer):** `vboxuser` (acessado via `vboxuser@pedrocha`)

# Procedimento: 
A execução das configurações de permissionamento foi dividida nas seguintes etapas pelo terminal da máquina virtual:
1. **Criação das Contas de Usuário:** Utilizamos o comando `sudo adduser` para criar as credenciais de `cicrano`, `beltrano` e `novato`.
2. **Definição dos Grupos de Trabalho:** 
   - Criamos o grupo de desenvolvedores (`sudo groupadd devs`) e adicionamos os usuários `fulano`, `cicrano` e `beltrano` através do comando `sudo usermod -aG devs`.
   - Em seguida, criamos o grupo administrativo (`sudo groupadd financeiro`), no qual inserimos exclusivamente `cicrano` e `beltrano`.
3. **Mapeamento de Pastas e Propriedades:**
   - Construímos as pastas raiz das equipes executando `sudo mkdir -p /srv/projeto` e `/srv/financeiro`.
   - Modificamos os donos e grupos desses diretórios para `vboxuser`, atrelando o diretório projeto ao grupo `devs` e o diretório financeiro ao grupo `financeiro` (`sudo chown` e `sudo chgrp`).
4. **Aplicação das Máscaras de Acesso:**
   - Ajustamos as pastas `/srv/projeto` e `/srv/financeiro` com a permissão octal `770` (`drwxrwx---`), permitindo acesso total ao dono e ao grupo, e bloqueando terceiros (`sudo chmod 770`).
   - Dentro da pasta de projetos, definimos a permissão de um arquivo `config_redes.txt` para `660` (`-rw-rw----`), mudando seu grupo também para `devs`.

# Testes e Evidências: 

### Testes das Etapas A e B (Grupo Devs e Acesso ao Projeto)
- **Verificação do Grupo:** Checamos a integridade do arquivo de grupos do sistema (`/etc/group`), confirmando que `fulano`, `cicrano` e `beltrano` foram de fato listados em `devs`.
<img width="374" height="59" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/75c20ea9-8217-401a-abe4-5264d7b1a66b" />

- **Teste de Permissão de Edição:** Efetuamos login com a conta `fulano` e realizamos uma edição no documento `config_redes.txt`, operação que foi concluída sem incidentes (permissão de Leitura/Escrita validada).
<img width="538" height="123" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/8e013061-ec09-4d01-bbe3-c7b3059fcced" />

### Exercício Prático de Fixação (Isolamento do Setor Financeiro)
- **Validação Restritiva de Grupo:** Através do comando `grep "financeiro" /etc/group`, comprovamos a ausência de pessoas não autorizadas. Apenas `cicrano` e `beltrano` apareceram no resultado (`financeiro:x:1006:cicrano,beltrano`).
<img width="433" height="54" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/8f5ca43c-1f3c-40ad-adca-f53bee5ccd94" />

- **Auditoria do Diretório:** Executamos `ls -ld /srv/financeiro` e confirmamos que a estrutura da máscara de acesso impedia acessos indevidos: `drwxrwx--- 2 vboxuser financeiro`.
<img width="548" height="56" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/e55da226-d8a3-481a-a5de-ac3dcbbe5ad0" />

- **Confirmação de Sucesso por Usuário Legítimo (`cicrano`):** Com o perfil autorizado, criamos um relatório na pasta (`echo "Relatorio Financeiro" > /srv/financeiro/relatorio.txt`). O documento foi salvo e lido via `cat` perfeitamente.
<img width="673" height="110" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/f8a411f9-f518-4d4e-ac24-2b264e39f096" />

- **Teste de Efetividade do Bloqueio (`fulano` e `novato`):** Quando tentamos escrever no mesmo diretório utilizando perfis de fora do grupo financeiro, o sistema defendeu o acesso devolvendo a mensagem `-bash: ... Permission denied` nas duas tentativas.
<img width="673" height="108" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/b335a178-4e1a-4204-b240-9c42d5d7f42c" />
<img width="653" height="114" alt="Captura de tela do terminal" src="https://github.com/user-attachments/assets/bcb857df-862d-4302-9e4f-64698e502030" />

# Problemas e Soluções: 

- **Falha de Comando na Validação (`command not found`):** Ao tentar buscar informações sobre os grupos, o comando `grep` apresentou erro. Descobrimos que a falha se deu por uso de crases e falta de espaçamento em vez das aspas duplas exigidas. Corrigimos o comando executando `grep "devs" /etc/group`.
- **Negação de Permissão Inesperada (Permission denied):** Ocorreu um problema onde o usuário `fulano` não conseguia alterar arquivos dentro da pasta do projeto, mesmo estando no grupo `devs`. Percebemos que o arquivo em si pertencia ao grupo do criador (`vboxuser`). Solucionamos a falha alterando o grupo raiz do documento com `sudo chgrp devs /srv/projeto/config_redes.txt` e aplicando o octal `chmod 660`.
- **Membro Incorreto em Grupo Confidencial:** Durante a montagem dos times, vinculamos por engano o usuário `fulano` à equipe do financeiro usando o comando `usermod`. Como tratava-se de um setor com acesso restrito, usamos o comando de desvinculação imediata `sudo gpasswd -d fulano financeiro` para revogar o privilégio.

# Conclusão: 
O desenvolvimento desta prática permitiu evidenciar a extrema importância da administração estruturada de usuários, grupos e permissões nativas do Linux (arquitetura de acesso POSIX). Ao implementarmos corretamente as máscaras de permissão (`chmod`), garantimos que dados sigilosos e de operação crítica se mantivessem isolados e protegidos contra o acesso indevido por usuários sem privilégio. A compreensão desses fundamentos é indispensável para qualquer administrador que pretenda garantir a integridade, a confidencialidade e a operação fluida de servidores Linux corporativos.
