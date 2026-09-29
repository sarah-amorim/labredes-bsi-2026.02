# Relatório Técnico: Configuração de Rede Estática com Netplan e Modo Placa em Ponte (Bridge Adapter) no VirtualBox

## 1. Identificação do Aluno
* **Nome Completo:** Sarah Amorim dos Santos
* **Matrícula:** 2024014201
* **Turma:** 2026.2
* **Data de Realização:** /09/2026

Identificação: Nome completo, matrícula, turma (BSI 2026.02), data e título da prática.
Objetivo: Explicação sobre a transição do modo NAT para Placa em Ponte e a importância da atribuição de IP estático via Netplan conforme a documentação do Ubuntu Server.
Ambiente: Especificação das configurações do Host Windows e do Guest Ubuntu Server 26.04 LTS no VirtualBox.
Procedimento: Descrição passo a passo da busca de IP livre, alteração no VirtualBox, edição e exibição do arquivo /etc/netplan/00-installer-config.yaml e aplicação das regras com netplan apply.
Testes e Evidências: Capturas de tela e saídas dos testes de ping bidirecional, exibição do arquivo YAML via cat e comandos traceroute.
Problemas e Soluções: Registro de eventuais erros de sintaxe no YAML (erros de indentação) ou bloqueios de firewall e como foram resolvidos.
Conclusão: Reflexão técnica sobre as vantagens e cuidados do uso de IPs estáticos e modo Bridge em servidores corporativos.
