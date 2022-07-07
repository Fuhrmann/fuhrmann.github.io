---
title: "Criando uma máquina virtual com libvirt"
date: 2022-07-06
author: Ricardo Fuhrmann
layout: post
permalink: /criando-maquina-virtual-libvirt/
tags: software, linux
---

## Introdução
Se você está tentando debugar aquele problema que você está tendo na sua distribuição Linux, mas não consegue descobrir se o problema é na aplicação ou na sua própria distribuição, a saída é tentar simular o mesmo comportamento em uma máquina virtual. Ou então se você desenvolve para diferentes distribuições Linux e quer testar seus packages pode criar máquinas virtuais de diferentes distribuições para testar suas aplicações.

Uma forma fácil de fazer isso é utilizando o `libvirt`, sendo uma ferramenta de código aberto utilizada para o gerenciamento de virtualização de máquinas. Ele fornece o servidor e as bibliotecas no host para poder interagir com hypervisors e sistemas guest.

## Instalação do libvirt no Fedora
Se você tem instalado no seu computador host o Fedora, pode seguir os passos abaixo para instalar o `libvirt`:

```shell
sudo dnf install @virtualization
```

No qual será instalado os seguintes pacotes:

```shell
Group: Virtualization
Description: These packages provide a graphical virtualization environment.
Mandatory Packages:
 virt-install
Default Packages:
 libvirt-daemon-config-network
 libvirt-daemon-kvm
 qemu-kvm
 virt-manager
 virt-viewer
Optional Packages:
 libguestfs-tools
 python3-libguestfs
 virt-top
```


Após a instalação inicie o serviço do `libvirt`:
```shell
sudo systemctl start libvirtd
```
Para ativar o serviço no boot, execute:
```shell
sudo systemctl enable libvirtd
```

E por último verifique se os módulos do kernel foram propriamente carregados:

```shell
[root@localhost ~]# lsmod | grep kvm
kvm_amd               114688  0
kvm                   831488  1 kvm_amd
```

> **Atenção**! Para mais detalhes da instalação do `libvirt` no Fedora acesse [Getting started with virtualization](https://docs.fedoraproject.org/en-US/quick-docs/getting-started-with-virtualization/). Para um guia de como instalar no Ubuntu, acesse [UbuntuKVMWalkthrough](https://wiki.libvirt.org/page/UbuntuKVMWalkthrough).
 
## Criando o disco dinamicamente alocado
Vamos começar criando um disco alocado dinamicamente para o nosso sistema operacional guest, ou seja, nossa máquina virtual, para isso, execute o comando abaixo:
```shell
sudo truncate --size=20480M /var/lib/libvirt/images/fedora36.img
```
O comando acima irá criar um disco de 20GB que será usado pela nossa máquina virtual no caminho especificado.

## Baixando o Fedora 36 Server
Você pode baixar a ISO do Fedora Server [aqui](https://getfedora.org/en/server/download/). Caso deseje usar a mesma versão deste artigo, o link direto para ISO, [clique aqui](https://download.fedoraproject.org/pub/fedora/linux/releases/36/Server/x86_64/iso/Fedora-Server-dvd-x86_64-36-1.5.iso).
Com a ISO baixada vamos agora executar o comando que irá criar nossa guest:
```shell
sudo virt-install \
--name fedora36 \
--ram 4096 \
--vcpus 2 \
--disk path=/var/lib/libvirt/images/fedora36.img,size=20 \
--os-variant fedora36 \
--network bridge=virbr0 \
--graphics none \
--console pty,target_type=serial \
--location /tmp/Fedora-Server-dvd-x86_64-36-1.5.iso \
--extra-args 'console=ttyS0,115200n8'
```

> **Atenção**! Caso você receba um erro de permissão como esse: 'WARNING /path/to/fedora.iso may not be accessible by the hypervisor. You will need to grant the 'qemu' user search permissions for the following directories:', coloque a ISO do Fedora em um local que o `qemu` tenha permissão para leitura, neste caso estamos colocando a ISO na pasta /tmp.

## Continuando a instalação
Após executar o comando acima será exibido em seu terminal o seguinte texto:
```
Text mode provides a limited set of installation options. It does not offer
custom partitioning for full control over the disk layout. Would you like to use
VNC mode instead?

1) Start VNC
2) Use text mode
```

Aqui fica a seu critério caso prefira continuar com a instalação gráfica ou textual. Caso queira continuar com a instalação no modo texto, responda com a opção `2` e tecle `Enter`.

Caso tenha escolhido o modo texto siga com o setup da forma como você achar melhor para configurar sua nova instalação do Fedora, como por exemplo configurar a timezone, senha do root, criação de um usuário durante a instalação e entre outros. A instalação no modo texto é bem intuitiva e fácil de entender.

Após finalizar a sua configuração no modo texto aperte `b` para iniciar a instalação e aguarde.

> **Atenção**! Se em algum momento durante a instalação você perder conexão com o console da VM, é possível reconectar a máquina virtual digitando `virsh console fedora36`:

```shell
[root@localhost ~]# virsh console fedora36
Connected to domain fedora36
Escape character is ^]  # Enter key
```
A instalação será finalizada quando a seguinte mensagem ser exibida:
```shell
Installation complete. Press ENTER to quit:
```

Pressione `Enter`e a VM irá reiniciar.

> **Atenção**! Caso a VM não tenha reiniciado e apenas desligado, você poderá inicializar ela novamente digitando `sudo virsh start fedora36`:

```shell
[root@localhost ~]# sudo virsh start fedora36
Domain 'fedora36' started
```

## Após a instalação
Para conectar novamente ao geust após a reinicialização, execute:

```shell
[root@localhost ~]# sudo virsh console fedora36
Connected to domain 'fedora36'
Escape character is ^] (Ctrl + ])
```
Pressione `Enter` e o guest irá requisitar um login. Informe o nome do usuário que você configurou para uso durante o processo de instalação ou então logue como o próprio root e informe a senha. A partir daí você terá sua máquina virtual do Fedora rodando disponível para realizar alguns testes, instalar packages entre outros!

## Comandos úteis

### Lista todos os guests:
```shell
[root@localhost ~]# sudo virsh list --all
Id   Nome       Estado
-----------------------------
7    fedora36   executando
-    win11      desligado
```

### Desliga um guest:
```shell
[root@localhost ~]# sudo virsh shutdown fedora36
Domain 'fedora36' is being shutdown
```

### Reinicializa um guest:
```shell
[root@localhost ~]# sudo virsh reboot fedora36
Domain 'fedora36' is being rebooted
```

### Exibe informações de um guest:
```shell
[root@localhost ~]# sudo virsh dominfo fedora36
Id:             8
Nome:           fedora36
Tipo de SO:     hvm
Estado:         executando
CPU(s):         2
Tempo de CPU:   27,5s
Memória máxima: 4194304 KiB
Memória utilizada: 4194304 KiB
Persistente:    sim
Auto-iniciar:   desabilitar
Salvamento gerenciado: não
Modelo de segurança: none
DOI de segurança: 0
```
	
Para a lista completa de comandos acesse a ajuda do comando `virsh`:
```shell
[root@localhost ~]# sudo virsh --help
virsh [options]... [<command_string>]
virsh [options]... <command> [args...]

options:
  -c | --connect=URI      hypervisor connection URI
  -d | --debug=NUM        debug level [0-4]
  -e | --escape <char>    set escape sequence for console
  -h | --help             this help
  -k | --keepalive-interval=NUM
                          keepalive interval in seconds, 0 for disable
  -K | --keepalive-count=NUM
                          number of possible missed keepalive messages
  -l | --log=FILE         output logging to file
  -q | --quiet            quiet mode
  -r | --readonly         connect readonly
  -t | --timing           print timing information
  -v                      short version
  -V                      long version
       --version[=TYPE]   version, TYPE is short or long (default short)
commands (non interactive mode):
...
...
...
...
```

## Referências:
- [KVM : Create Virtual Machines](https://www.server-world.info/en/note?os=Fedora_36&p=kvm&f=2)
- [Getting started with virtualization](https://docs.fedoraproject.org/en-US/quick-docs/getting-started-with-virtualization/)
- [Use virt-install and connect by using a local VNC client](https://docs.openstack.org/image-guide/virt-install.html)
- [KVM: Starting / Stopping Guest Operating Systems With virsh Command](https://www.cyberciti.biz/faq/linux-kvm-stop-start-guest-virtual-machine/)
