# Identificação: 
- Nome completo: Sarah Amorim dos Santos
- Curso: Sistemas de Informação
- Turma: 2026.02
- Data: 26/08/2026
- Título da prática: 

---

## 2. Objetivo da Prática
Esta atividade teve como propósito principal a aplicação prática de conceitos de virtualização, com foco no uso de um Hypervisor Tipo 2 para o isolamento e gerenciamento de recursos. A aula consistiu na criação de uma máquina virtual no Oracle VM VirtualBox, na instalação do sistema operacional Ubuntu Server 26.04 LTS e na execução de testes via linha de comando para validar a conectividade de rede, o particionamento do disco e a comunicação com os repositórios de pacotes.

---

## 3. Especificações do Ambiente
* **Máquina Hospedeira (Host):** Sistema Operacional Windows 10/11
* **Diretório de Trabalho:** `C:\2026\BSI\VM\[SeuPrimeiroNome]\`
* **Hypervisor Utilizado:** Oracle VM VirtualBox
* **Imagem ISO:** `ubuntu-26.04-live-server-amd64.iso`
* **Parâmetros da Máquina Virtual (`[nome_da_sua_vm]`):**
  * **Memória RAM:** 2048 MB (2 GB)
  * **Processador:** 1 núcleo (vCPU)
  * **Armazenamento:** 32 GB (Disco virtual tipo VDI, com alocação dinâmica)
  * **Configuração de Rede:** Adaptador configurado em NAT (Interface `enp0s3`)

---

## 4. Passos Executados
1. **Preparação do Ambiente Físico:** Organização dos diretórios no sistema host para armazenar o disco virtual e a imagem ISO, garantindo um espaço de trabalho isolado.
2. **Criação e Configuração da VM:** Provisionamento da máquina virtual no VirtualBox, ajustando os recursos de hardware (memória, processador e disco VDI) para garantir um ambiente estável para o sistema.
3. **Instalação do SO:** Inicialização da VM a partir da imagem ISO e execução do processo de instalação do Ubuntu Server 26.04 LTS.
4. **Testes Pós-Instalação:** Acesso ao terminal do servidor virtualizado (CLI) para verificação do ambiente, incluindo testes de interface de rede, análise de armazenamento e checagem de atualizações.

---

## 5. Validação do Ambiente (Comandos e Evidências)

### A. Verificação das Interfaces de Rede (`ip addr`)
Para garantir que o servidor estava conectado à rede, executamos o comando de verificação de IP:

```bash
ip addr
```

**Resultado:** O comando confirmou que a interface `enp0s3` subiu corretamente, recebendo o endereço IPv4 `10.0.2.15/24` via DHCP. Esse comportamento é o padrão esperado para redes configuradas em modo NAT no VirtualBox.

### B. Análise de Armazenamento (`df -h`)
Para verificar o layout das partições e o consumo de disco, utilizamos o seguinte comando:
```bash
df -h
```

**Resultado:** Identificamos que a partição primária `/dev/sda2` foi montada no diretório raiz (`/`). O espaço total alocado foi de 33 GB, dos quais apenas 3.1 GB estavam sendo utilizados no momento, deixando bastante espaço livre (cerca de 28 GB) para atividades futuras.

### C. Sincronização dos Repositórios (`sudo apt-get update`)
O último teste consistiu em validar a saída para a internet e a integridade do gerenciador de pacotes:
```bash
sudo apt-get update
```

**Resultado:** A comunicação com os servidores oficiais da Canonical (archive.ubuntu.com) ocorreu com sucesso, atualizando as listas de pacotes do sistema sem apresentar falhas de conexão.

---

## 6. Desafios Enfrentados e Soluções Adotadas

### Desafio 1: Falha na compilação/instalação do sistema (erro *curtin*)
* **Ocorrência:** Durante a extração dos arquivos do sistema operacional, a instalação apresentou uma falha crítica.
* **Causa:** Verificamos que a configuração inicial alocou apenas 512 MB de memória RAM para a VM, quantidade insuficiente para suportar o processo de instalação do Ubuntu.
* **Solução:** A máquina virtual foi desligada, e a memória base foi redimensionada para 2 GB (2048 MB) nas configurações do VirtualBox. Após essa alteração, o processo de instalação foi refeito e concluído com sucesso.


### Desafio 2: Instalação no modo não assistido (Unattended Installation)
* **Ocorrência:** O assistente de instalação pulou etapas tradicionais, como a configuração manual das partições e a escolha do layout do teclado.
* **Causa:** O VirtualBox possui um recurso de instalação automatizada que foi ativado assim que reconheceu a ISO do Ubuntu.
* **Solução:** Optamos por manter o sistema gerado automaticamente, utilizando a conta padrão `vboxuser` criada pelo hipervisor. O comportamento foi apenas documentado, já que não impediu a realização e validação da prática.

---

## 7. Conclusão
A prática atingiu todos os objetivos propostos, oferecendo uma visão clara de como um hipervisor do tipo 2 opera na abstração e distribuição de recursos físicos (CPU, RAM e disco) para sistemas virtualizados. A experiência com a resolução do problema de falta de RAM ressaltou a importância do planejamento de hardware virtual. Ao final, a validação via CLI provou que a rede, os diretórios e os gerenciadores de pacotes do Ubuntu Server 26.04 LTS estão totalmente operacionais, deixando o ambiente pronto para futuras configurações de serviços de rede na disciplina.
