- **Nome completo:** Sarah Amorim dos Santos
- **Matrícula:** 2024014201
- **Turma:** BSI 2026.2
- **Título da prática:** Roteamento e Gateway NAT com IPTables e Rede Interna no VirtualBox

## 2. Objetivo

O objetivo desta prática é entender como um servidor Linux pode servir de gateway, isto é, a "porta de saída" de uma rede privada para outras redes, como a internet.

A topologia usa duas máquinas virtuais:

- **VM1 (gateway):** tem duas placas de rede. Uma fica em modo Bridge (enp0s3), ligada à rede do IFAL. A outra fica em Rede Interna (enp0s8), ligada à rede privada 10.0.0.0/24.
- **VM2 (cliente):** tem uma placa só, em Rede Interna (enp0s3), e usa a VM1 como gateway padrão.

Como o gateway participa das duas redes, ele consegue ligar uma à outra. Para a VM2, tudo o que não é da rede 10.0.0.0/24 deve ser enviado para o 10.0.0.1, que é a VM1, e ela se encarrega de levar o pacote adiante.

A prática também apresenta a ideia do NAT, que é trocar o IP de origem dos pacotes. A VM2 usa um endereço privado, que não funciona na internet. Por isso o gateway precisa trocar esse endereço pelo dele antes de mandar o pacote para fora. Na volta, ele usa a tabela de conexões (conntrack) para entregar a resposta à máquina certa. A configuração completa do NAT e do mascaramento fica para a próxima aula. Nesta, o foco é montar a rede, configurar os IPs pelo Netplan e testar a comunicação básica.

## 7. Conclusão

Esta aula mostrou que, antes de configurar o NAT, é preciso ter a base da rede funcionando: as placas no modo certo no VirtualBox, o mesmo nome de rede interna nas duas VMs, IPs na mesma faixa e o gateway padrão apontando para o lugar correto.

Também ficou claro que o gateway só resolve metade do problema. Neste ponto, a VM2 consegue conversar com a VM1, mas ainda não deve chegar à internet. Falta ligar o encaminhamento de IP e criar as regras de NAT no gateway, o que faz sentido, já que um pacote de origem 10.0.0.2 não tem como voltar sozinho da internet. Por isso é normal alguns testes ainda falharem nesta etapa.

Entender essa divisão (primeiro a rede local, depois a saída para fora) ajuda a achar erros mais rápido, porque dá para testar cada parte separadamente.
