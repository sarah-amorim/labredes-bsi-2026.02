# Identificação: 
- **Nome completo:** Sarah Amorim dos Santos
- **Matrícula:** 2024014101
- **Turma:** 2026.2
- **Data da prática:** 26/08/2026

> **Observação:** Como esta atividade foi realizada em dupla com o aluno Pedro Henrique Cavalcante Rocha, utilizamos a máquina virtual dele como ambiente principal do laboratório. Por esse motivo, as capturas de tela e os caminhos no terminal mostram o usuário padrão como `vboxuser@pedrocha`.

# Objetivo: 
O objetivo principal desta aula foi aprofundar os conhecimentos sobre a hierarquia de diretórios padrão do Linux (FHS). Durante a prática, focamos na navegação em pastas de sistema, na criação ágil de árvores de diretórios com o comando `mkdir -p` e na aplicação de permissões avançadas para estabelecer um isolamento departamental seguro dentro de um ambiente corporativo simulado.

# Ambiente: 
- **Sistema Hospedeiro:** Windows 11
- **Hipervisor (Virtualização):** Oracle VM VirtualBox
- **Sistema Convidado:** Ubuntu Server
- **Usuário Padrão/Sudoer:** `vboxuser` (rodando sob o hostname `pedrocha`)

# Procedimento: 
A prática foi executada via linha de comando seguindo as etapas abaixo:
1. **Inspeção do FHS:** Navegamos pelas pastas de configuração global em `/etc` e investigamos o diretório de registros e logs do sistema operacional em `/var/log/auth.log`.
2. **Criação de Estruturas Recursivas:** Para montar a base de arquivos da empresa de uma só vez, utilizamos o parâmetro `-p` na criação de pastas aninhadas: `/srv/ti-dept/projetos`, `/srv/vendas-dept/relatorios` e `/srv/diretoria-dept`.
3. **Mapeamento de Grupos Departamentais:** Construímos os grupos organizacionais `ti-group`, `vendas-group` e `diretoria-group`. Na sequência, designamos os usuários fictícios (`fulano`, `cicrano` e `beltrano`) aos seus respectivos setores.
4. **Aplicação de Permissões Corporativas:** Mudamos a propriedade das pastas com o comando `chown`. Depois, utilizamos o sistema octal de permissões para blindar o diretório da diretoria com `chmod 770` (`drwxrwx---`) e o arquivo interno `orcamento_ti.txt` com `660` (`-rw-rw----`), restringindo o acesso total a terceiros.
5. **Testes de Sessão Limpa:** Para validar as regras de segurança, realizamos a troca de usuários no terminal utilizando o `su -` para gerar sessões reais e limpas.

# Testes: 

### Validação A (Sucesso - Acesso Autorizado)
Efetuamos o login com o perfil do `beltrano`, que pertence ao grupo da diretoria. Ao tentar acessar a pasta e ler o arquivo `orcamento_ti.txt`, o sistema permitiu as operações perfeitamente, já que as permissões octais liberam leitura/escrita/execução para os membros do grupo.
<img width="557" height="121" alt="Acesso beltrano 1" src="https://github.com/user-attachments/assets/abfd5b19-de54-4e08-8e7d-5ed45cb19d7c" />
<img width="493" height="121" alt="Acesso beltrano 2" src="https://github.com/user-attachments/assets/72008068-72e1-4b14-85a1-96c3b982e647" />

### Validação B (Bloqueio - Permission Denied)
Entramos na conta do `fulano` (usuário que não faz parte do diretório da diretoria). Ao executarmos o comando de navegação (`cd /srv/diretoria-dept`), recebemos o bloqueio exato configurado, resultando na mensagem `-bash: cd: /srv/diretoria-dept: Permission denied`.
<img width="448" height="100" alt="Bloqueio fulano" src="https://github.com/user-attachments/assets/4840d397-4336-4e06-bb25-425f2d56eb6b" />

### Confirmação das Propriedades
Executamos uma listagem detalhada que comprova a configuração correta de dono/grupo e das máscaras de bloqueio.
<img width="617" height="112" alt="Checagem de permissões" src="https://github.com/user-attachments/assets/6adc3591-e397-4b21-a977-7326ca3bc990" />

# Problemas e Soluções: 

**Confusão de Sessão (`su` vs `su -`)**
- **Dificuldade Encontrada:** Durante a realização dos testes para trocar de usuário, o uso apenas do comando `su usuario` acabou reaproveitando as variáveis de ambiente e o caminho da pasta raiz do perfil anterior, o que gerou confusão na navegação e falsos alertas de permissão.
- **Solução Aplicada:** Aprendemos e aplicamos a necessidade de sempre usar o hífen (`su - usuario`). O hífen força o carregamento de uma "Login Shell" limpa, reiniciando o contexto de variáveis e direcionando o usuário recém-logado imediatamente para o seu diretório `/home` isolado.

# Conclusão: 
Esta prática consolidou o nosso entendimento sobre o padrão FHS e os mecanismos de segurança local no Linux. Trabalhar com a estrutura hierárquica e aliar comandos de montagem rápida (`mkdir -p`) à precisão cirúrgica das permissões octais demonstra como proteger pastas e arquivos sensíveis. Esses conceitos formam a base indispensável da proteção de dados, garantindo que cada usuário interaja apenas dentro dos limites autorizados no servidor.
