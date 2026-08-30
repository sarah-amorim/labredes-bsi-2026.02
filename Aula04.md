# Relatório Técnico: Manipulação de Arquivos e Automação de Usuários no Linux

## 1. Identificação do Aluno
* **Nome Completo:** Sarah Amorim dos Santos
* **Matrícula:** 2024014201
* **Turma:** 2026.2
* **Data de Realização:** 29/08/2026

> **Observação Importante:** Esta atividade prática foi realizada em dupla com o colega Pedro Henrique Cavalcante Rocha. Por isso, utilizamos a máquina virtual configurada no computador dele para as execuções. Isso justifica a exibição do usuário e host `vboxuser@pedrocha` nas capturas de tela e comandos abaixo.

## 2. Objetivo
O objetivo desta prática foi aprofundar o uso de comandos básicos para edição e manipulação de arquivos no Linux, e aplicar esse conhecimento na criação de scripts Shell (`.sh`). A meta final foi automatizar o cadastro de usuários em lote, demonstrando como a automação reduz erros humanos e agiliza tarefas repetitivas de administração de sistemas.

## 3. Ambiente
* **Sistema Hospedeiro:** Windows 11
* **Hipervisor:** Oracle VM VirtualBox
* **Sistema Convidado:** Ubuntu Server 26.04 LTS
* **Recursos Alocados:** 512 MB de RAM / 1 vCPU / 32 GB de Disco
* **Usuário Padrão/Sudoer:** `vboxuser` (máquina `pedrocha`)

## 4. Procedimento Executado
1. **Manipulação de Arquivos:** Praticamos o gerenciamento de diretórios e arquivos usando comandos essenciais como `touch`, `nano`, `cp`, `mv` e `rm` (incluindo exclusão forçada e interativa).
2. **Preparação da Lista:** Criamos um arquivo texto chamado `usuarios.txt`, onde inserimos uma lista de 20 contas (de `aluno01` a `aluno20`).
3. **Script de Criação (`passo1_criar.sh`):** Desenvolvemos um script utilizando um laço `for` que lê a lista e executa o comando `useradd -m -s /bin/bash` para criar cada conta com seu respectivo diretório `/home` e shell padrão.
4. **Script de Senhas (`passo2_senhas.sh`):** Criamos um segundo script para definir senhas em massa, usando o utilitário `chpasswd` alimentado pela estrutura `usuario:senha` através de um *pipe* (`|`).
5. **Permissões de Execução:** Alteramos as propriedades dos scripts criados adicionando a flag de execução com o comando `chmod +x`, permitindo que rodassem nativamente no terminal.

## 5. Testes e Evidências

**A. Execução dos Scripts em Lote**
Rodamos os scripts e a criação das 20 contas, junto com a definição das senhas, ocorreu perfeitamente e sem erros de sintaxe.
<img width="475" height="127" alt="Execução script 1" src="https://github.com/user-attachments/assets/5a9b4c98-5a09-45e9-98df-aa812a5bc4cc" />
<img width="428" height="428" alt="Execução script 2" src="https://github.com/user-attachments/assets/ac87c762-cb4b-45c4-8591-878d8b298248" />
<img width="456" height="401" alt="Execução script 3" src="https://github.com/user-attachments/assets/210b394a-a3f8-4893-a2aa-6589f488c692" />
<img width="482" height="141" alt="Execução script 4" src="https://github.com/user-attachments/assets/5a60ac61-8c93-4055-88f1-caae62700d95" />

**B. Validação no Sistema**
Fizemos a checagem no arquivo de usuários do sistema (`getent passwd | tail -n 20`), o que confirmou que todas as 20 contas foram registradas com sucesso, contendo ID, diretório `/home` e shell configurados corretamente.
<img width="404" height="359" alt="Validação getent passwd" src="https://github.com/user-attachments/assets/db30ca80-7e54-459f-be42-95a760a33543" />

**C. Teste de Acesso (Login Shell)**
Realizamos o login com a primeira conta criada (`su - aluno01`) inserindo a senha definida. O sistema nos autenticou e direcionou corretamente para a pasta raiz do usuário (`/home/aluno01`).
<img width="573" height="228" alt="Teste de acesso login" src="https://github.com/user-attachments/assets/512414a6-c999-4647-bf71-d35678f4ea45" />

## 6. Problemas Encontrados e Soluções

* **Bloqueio de Execução (Permission denied):** Ao tentar iniciar o script com `./passo1_criar.sh`, fomos barrados. Percebemos que arquivos criados com o editor `nano` não ganham permissão de execução automaticamente por questões de segurança do Linux. Resolvemos isso aplicando o comando `chmod +x passo1_criar.sh passo2_senhas.sh`.
* **Falta de Privilégios no useradd:** O script disparou erros avisando que não tínhamos autorização para criar contas, pois o comando `useradd` é exclusivo de superusuário (root). A correção foi simples: abrimos o script e adicionamos `sudo` antes do comando dentro do laço `for` (`sudo useradd -m -s /bin/bash $usuario`).

## 7. Conclusão
Esta atividade prática deixou muito claro o poder da automação via Shell Script na rotina de um administrador de sistemas. Substituir o trabalho manual de criar usuário por usuário por um script simples com `for`, `useradd` e `chpasswd` não apenas economiza um tempo valioso, mas também elimina o risco de erros de digitação. É uma base essencial para gerenciar infraestruturas Linux em larga escala de forma eficiente e padronizada.
