# Relatório Técnico: Configuração de Rede Estática com Netplan e Modo Placa em Ponte (Bridge Adapter) no VirtualBox

## 1. Identificação do Aluno
* **Nome Completo:** Sarah Amorim dos Santos
* **Matrícula:** 2024014201
* **Turma:** 2026.2
* **Data de Realização:** 30/09/2026

## 2. Objetivo
 
O objetivo é mudar a placa de rede da máquina virtual do modo NAT para o modo Placa em Ponte (Bridge). Nesse modo, a máquina virtual passa a ser vista na rede do laboratório como se fosse um computador de verdade, dentro da rede `172.20.20.0/22`.
 
Depois, o objetivo é configurar um endereço IP fixo (estático) usando o Netplan, seguindo o padrão da documentação do Ubuntu Server. Um IP fixo é importante em servidores porque o endereço não muda, e assim outras máquinas sempre sabem onde encontrá-lo.
 
Por fim, a prática testa a comunicação nos dois sentidos entre o Windows e a máquina virtual e verifica o caminho até a internet com o `traceroute`.
 
## 3. Ambiente
 
- **Computador físico (Host):** Windows, com PowerShell e VirtualBox
- **Máquina virtual (Guest):** Ubuntu Server 26.04 LTS, chamada `ubuntu_server`
- **Usuário:** `administrador`
- **Interface de rede:** `enp0s3`
- **Rede do laboratório:** `172.20.20.0/22` (máscara `255.255.252.0`)
- **Gateway:** `172.20.20.1`
- **DNS:** `172.20.20.1`, `1.1.1.1` e `8.8.8.8`
## 4. Procedimento
 
**Passo 1: Trocar o modo de rede no VirtualBox**
 
1. Abrir Configurações (`Ctrl + S`) da máquina `ubuntu_server` e ir em Rede.
2. No Adaptador 1, marcar Habilitar Placa de Rede.
3. Em Conectado a, trocar de NAT para Placa em Ponte.
4. Em Nome, escolher a placa de rede física do computador que está ligada na rede.
5. Na aba Avançado, conferir se o cabo está conectado, e clicar em OK.
**Passo 2: Procurar um IP livre**
 
1. No PowerShell do Windows, testar o endereço candidato:
```powershell
   ping 172.20.23.1
```
2. Se aparecer "Esgotado o tempo limite do pedido" ou "Host de destino inacessível", ninguém usa esse IP e ele está livre. Se houver resposta, outra máquina já usa o endereço, e é preciso testar o próximo (`172.20.23.2`, `172.20.23.3` e assim por diante).
3. Neste relatório, o IP livre usado é `172.20.23.1`.
**Passo 3: Editar o arquivo do Netplan**
 
1. Entrar na máquina virtual e abrir o arquivo:
```bash
   sudo nano /etc/netplan/00-installer-config.yaml
```
2. Deixar o conteúdo assim (usar só espaços, nunca a tecla Tab):
```yaml
   network:
     version: 2
     renderer: networkd
     ethernets:
       enp0s3:
         dhcp4: false
         addresses:
           - 172.20.23.1/22
         routes:
           - to: default
             via: 172.20.20.1
         nameservers:
           addresses:
             - 172.20.20.1
             - 1.1.1.1
             - 8.8.8.8
```
3. Salvar com `Ctrl + O`, `Enter`, e sair com `Ctrl + X`.
**Passo 4: Conferir e aplicar**
 
1. Mostrar o arquivo para conferir a escrita:
```bash
   cat /etc/netplan/00-installer-config.yaml
```
2. Aplicar a configuração:
```bash
   sudo netplan apply
```
3. Ver se o IP foi colocado na interface:
```bash
   ip addr show enp0s3
   sudo netplan status
```
 
## 5. Testes e Evidências
 
Esta seção descreve o que cada teste mostra quando a prática é feita.
 
- **IP livre:** `ping 172.20.23.1` no Windows, antes da configuração. Não deve haver resposta, o que mostra que o IP estava livre.
- **Modo de rede:** janela de Configurações do VirtualBox. O Adaptador 1 deve estar em Placa em Ponte.
- **Arquivo do Netplan:** `cat /etc/netplan/00-installer-config.yaml`. Deve aparecer o arquivo completo, com `renderer: networkd`, o IP fixo e a rota.
- **IP aplicado:** `ip addr show enp0s3` ou `netplan status`. A interface deve mostrar `172.20.23.1/22`.
- **Ping do Windows para a VM:** `ping 172.20.23.1`. Devem chegar respostas sem perda de pacotes.
- **Ping da VM para o Windows:** `ping -c 4` com o IP do Windows, visto no `ipconfig`. Devem chegar 4 respostas.
- **Rota para o Google:** `traceroute google.com`. O primeiro salto deve ser `172.20.20.1`, seguido dos saltos externos.
- **Rota para a Cloudflare:** `traceroute one.one.one.one`. O nome deve ser resolvido para `1.1.1.1`, com o caminho até o destino.
As capturas de tela desta prática não foram anexadas a este relatório (veja o item 6).
 
## 6. Problemas e Soluções
 
A máquina virtual do VirtualBox não estava funcionando no momento da atividade. Por isso não foi possível tirar as capturas de tela nem copiar as saídas reais dos comandos. O relatório apresenta o procedimento completo e o resultado esperado de cada teste, e as evidências poderão ser refeitas assim que o problema da máquina virtual for resolvido.
 
Problemas comuns que podem acontecer nessa prática:
 
- **Erro de indentação no YAML:** o Netplan não aceita Tab e exige 2 espaços por nível. Se der erro no `netplan apply`, a solução é abrir o arquivo de novo e acertar os espaços.
- **IP já em uso:** se o `ping` responder, o IP não está livre. A solução é escolher outro endereço.
- **Ping do Windows para a VM não responde:** o firewall do Windows pode estar bloqueando. A solução é liberar o ping (ICMP) no firewall ou conferir se a placa escolhida no VirtualBox é a correta.
## 7. Conclusão
 
O modo Placa em Ponte deixa a máquina virtual visível na rede do laboratório, sem precisar de redirecionamento de portas. Isso é útil quando outras máquinas precisam acessar o servidor direto.
 
O IP estático dá a vantagem de o endereço nunca mudar, o que é essencial para servidores. Em compensação, exige cuidado: é preciso escolher um IP que ninguém esteja usando, para não causar conflito, e conferir bem o arquivo YAML, já que um erro de espaço pode deixar o servidor sem rede. Em servidores de empresas, também é importante manter um registro dos IPs usados, para manter tudo organizado.

