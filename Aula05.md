# Relatório Técnico: Manipulação de Arquivos e Automação de Usuários no Linux

## 1. Identificação do Aluno
* **Nome Completo:** Sarah Amorim dos Santos
* **Matrícula:** 2024014201
* **Turma:** 2026.2
* **Data de Realização:** 30/09/2026

## 2. Objetivo
 
O objetivo desta prática é aprender a entrar de forma remota e segura em um servidor Ubuntu Server que roda dentro do VirtualBox. Para isso, usamos o SSH, que funciona na porta 22, e o redirecionamento de portas do modo NAT.
 
No modo NAT, a máquina virtual fica numa rede privada escondida (IP `10.0.2.15`). O Windows não consegue chegar nesse IP diretamente. Com a regra de redirecionamento, tudo que chega na porta `5222` do Windows é enviado para a porta `22` da máquina virtual.
 
Também faz parte da prática olhar como a rede está configurada, tanto no Linux quanto no Windows. Isso é feito com comandos como `ifconfig`, `route -n`, `traceroute`, `w`, `ipconfig` e `netstat -an`.
 
## 3. Ambiente
 
- **Computador físico (Host):** Windows, com PowerShell e VirtualBox instalado
- **Máquina virtual (Guest):** Ubuntu Server 26.04 LTS, chamada `ubuntu_server`
- **Usuário da máquina virtual:** `administrador`
- **Rede da máquina virtual:** Adaptador 1 em modo NAT, interface `enp0s3`, IP `10.0.2.15/24`
- **Regra de redirecionamento:** porta `5222` do Host para a porta `22` do Guest
## 4. Procedimento
 
**Parte A: Diagnóstico no Linux (máquina virtual)**
 
1. Entrar na máquina virtual com o usuário `administrador`.
2. Ver o estado da rede com o comando `netplan status`. A interface `enp0s3` aparece com DHCP e o IP `10.0.2.15/24`.
3. Conferir se o servidor SSH está instalado:
```bash
   dpkg -l | grep openssh-server
```
4. Instalar as ferramentas de rede:
```bash
   sudo apt update
   sudo apt install -y net-tools traceroute
```
5. Ver as interfaces com `ifconfig`. Nele aparecem o IP `10.0.2.15`, a máscara `255.255.255.0` e o endereço MAC da placa. Também aparece a interface `lo` com o IP `127.0.0.1`.
6. Ver a rota padrão com `route -n`. A linha que começa com `0.0.0.0` mostra o gateway `10.0.2.2`. É por ele que o tráfego sai para a internet.
7. Rastrear o caminho até o servidor do Google com `traceroute 8.8.8.8`. O primeiro salto é o `10.0.2.2`, e os próximos são os roteadores da rede do IFAL.
8. Ver quem está conectado com o comando `w`. Antes do SSH, só aparece o console local (`tty1`).
**Parte B: Diagnóstico no Windows (antes da regra)**
 
1. Abrir o PowerShell e rodar `ipconfig /all` para ver o IP real do computador e o MAC, que é diferente do MAC da máquina virtual.
2. Rodar `netstat -an | findstr 5222`. Como a regra ainda não existe, não aparece nenhuma linha.
**Parte C: Regra de redirecionamento no VirtualBox**
 
1. Selecionar a máquina `ubuntu_server` e abrir Configurações (`Ctrl + S`), depois Rede.
2. No Adaptador 1 (NAT), clicar em Avançado e depois em Redirecionamento de Portas.
3. Clicar no `+` e criar a regra com estes valores:
   - **Nome:** SSH
   - **Protocolo:** TCP
   - **IP do Hospedeiro:** 127.0.0.1
   - **Porta do Hospedeiro:** 5222
   - **IP do Convidado:** 10.0.2.15
   - **Porta do Convidado:** 22
4. Clicar em OK para salvar.
**Parte D: Conexão SSH e verificação**
 
1. No PowerShell, rodar `netstat -an | findstr 5222` de novo. Agora aparece a linha `127.0.0.1:5222 ... LISTENING`.
2. Conectar com:
```powershell
   ssh -p 5222 administrador@127.0.0.1
```
   No primeiro acesso, digitar `yes` para aceitar a chave e depois colocar a senha.
3. Com a conexão aberta, abrir um segundo PowerShell e rodar `netstat -an | findstr 5222`. Aparecem linhas com `ESTABLISHED`.
4. Dentro da sessão SSH, rodar `w`. Aparecem duas linhas: `tty1` (console da máquina virtual) e `pts/0` (sessão remota por SSH), com origem `10.0.2.2`.
5. Sair da sessão com `exit`.
 
## 5. Testes e Evidências
 
Esta seção descreve o que cada teste mostra quando a prática é feita.
 
- **`ifconfig`:** deve aparecer o IP `10.0.2.15`, a máscara `255.255.255.0` e o MAC da placa. Isso mostra que a máquina virtual está em rede NAT.
- **`route -n`:** deve aparecer a linha `0.0.0.0` com o gateway `10.0.2.2`. Isso mostra que o tráfego para fora passa pelo roteador virtual.
- **`traceroute 8.8.8.8`:** o primeiro salto deve ser `10.0.2.2`, seguido dos roteadores externos. Isso mostra que o caminho até a internet está funcionando.
- **`netstat` antes da regra:** não deve aparecer nenhuma linha. Isso mostra que a porta 5222 ainda não está em uso.
- **`netstat` depois da regra:** deve aparecer `127.0.0.1:5222 LISTENING`. Isso mostra que o VirtualBox está escutando a porta.
- **`netstat` durante o SSH:** devem aparecer linhas com `ESTABLISHED`. Isso mostra que há uma conexão ativa entre o Windows e a regra.
- **`w` dentro do SSH:** devem aparecer os terminais `tty1` e `pts/0`. Isso mostra que a sessão remota foi aberta.
As capturas de tela desta prática não foram anexadas a este relatório (veja o item 6).
 
## 6. Problemas e Soluções
 
A máquina virtual do VirtualBox não estava funcionando no momento da atividade. Por isso não foi possível tirar as capturas de tela nem copiar as saídas reais dos comandos. O relatório apresenta o procedimento completo e o resultado esperado de cada teste, e as evidências poderão ser refeitas assim que o problema da máquina virtual for resolvido.
 
Erros comuns que podem acontecer nessa prática:
 
- **Conexão recusada no SSH:** pode ser que o `openssh-server` não esteja instalado ou que a regra de redirecionamento esteja com a porta errada. A solução é conferir o pacote com `dpkg` e revisar a tabela de regras.
- **Erro ao digitar os parâmetros:** o comando precisa usar `-p 5222` com o `p` minúsculo. A solução é digitar o comando com atenção.
- **Bloqueio de firewall:** o firewall do Windows pode barrar a conexão. A solução é liberar o VirtualBox ou a porta usada.
## 7. Conclusão
 
Nesta prática ficou claro que, no modo NAT, a máquina virtual não pode ser alcançada diretamente pelo computador físico. O redirecionamento de portas resolve isso de um jeito simples: a porta 5222 do Windows leva até a porta 22 da máquina virtual.
 
Os comandos de diagnóstico ajudam a entender a rede. O `ifconfig` mostra o IP e o MAC, o `route -n` mostra o gateway, o `traceroute` mostra o caminho dos pacotes, o `netstat` mostra as portas abertas e as conexões, e o `w` mostra quem está logado e por qual terminal. Saber usar essas ferramentas é importante para quem vai administrar servidores, porque permite achar e corrigir problemas de conexão e também perceber acessos que não deveriam existir.

