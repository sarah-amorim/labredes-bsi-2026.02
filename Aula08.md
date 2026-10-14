- **Nome completo:** Sarah Amorim dos Santos
- **Matrícula:** 2024014201
- **Turma:** BSI 2026.2
- **Título da prática:** Configuração do Gateway (VM1): IPTables, NAT e Testes

## 2. Objetivo

O objetivo desta prática é terminar a configuração do gateway para que a VM2 consiga acessar a internet passando pela VM1. Para isso, a aula trabalha estes pontos:

- **Nomes das máquinas (hostnamectl):** dar nomes claros às VMs (vm1-gateway e vm2-cliente), para saber em qual máquina cada comando está sendo executado.
- **Encaminhamento de IP (ip_forward):** por padrão, o Linux não repassa pacotes de uma placa de rede para outra. Ao ligar o `net.ipv4.ip_forward`, o servidor passa a funcionar como roteador. Para o ajuste continuar valendo depois de reiniciar, ele é salvo em um arquivo em `/etc/sysctl.d/`.
- **NAT com mascaramento (MASQUERADE):** a regra na tabela nat, cadeia POSTROUTING, troca o IP de origem dos pacotes que saem pela enp0s3 pelo IP do próprio gateway. O mascaramento usa automaticamente o IP da interface de saída, então a regra não precisa ser alterada se esse IP mudar.
- **Regras de FORWARD:** liberam o que pode passar pelo gateway. Uma regra deixa a rede interna (enp0s8) enviar tráfego para fora (enp0s3). A outra deixa as respostas voltarem, aceitando apenas conexões já estabelecidas ou relacionadas, o que evita que a internet inicie conexões para dentro da rede.
- **Persistência das regras:** as regras do iptables somem ao reiniciar a máquina. Com o pacote `iptables-persistent` e o comando `netfilter-persistent save`, elas são gravadas em `/etc/iptables/rules.v4` e carregadas no boot.
- **Testes na VM2:** o ping mostra se há conectividade (com o gateway interno, o gateway externo e a internet). O nslookup testa a resolução de nomes (DNS). O traceroute mostra por quais saltos o pacote passa, comprovando que o tráfego sai pela VM1.

## 7. Conclusão

Com o ip_forward ligado e as regras de NAT e FORWARD aplicadas, a VM1 passa a ser o gateway da rede interna. As máquinas ficam numa rede privada e mesmo assim acessam a internet por um único ponto de saída, que é como funciona o roteador de casa e a rede de muitas empresas.

Isso também ajuda na segurança. De fora, só o gateway aparece, e as respostas só entram se alguém da rede interna fez o pedido antes. Como as regras controlam o que pode passar, o administrador decide o que entra e o que sai.

Outro ponto importante é a persistência. Sem salvar o ip_forward e as regras do iptables, tudo se perde ao reiniciar a máquina, e o gateway deixa de funcionar sem ninguém perceber a causa. Por fim, quando algo falha, vale conferir uma coisa de cada vez: o ip_forward, as regras do iptables, a rota padrão e o DNS.
