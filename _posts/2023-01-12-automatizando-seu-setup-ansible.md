---
title: "Automatizando seu setup com Ansible"
date: 2023-01-12
author: Ricardo Fuhrmann
layout: post
permalink: /automatizando-seu-setup-com-ansible/
tags: software, linux, ansible
---

### Introdução

Seja manter o seu computador pronto para uma formatação de última hora (quem nunca?) ou o seu servidor livre de riscos de apagões e corrupção de dados, o Ansible traz uma forma fácil e divertida de lidar com essas questões de forma automatizada. 

Um sistema de automação de gerenciamento de configurações, provisionamento, implantação de aplicações, Ansible torna fácil, com apenas um comando reconfigurar o seu servidor ou seu computador pessoal do jeito que você quer utilizando comandos definidos em arquivos `yaml`. Ansible tira o medo de você perder aquela configuração obscura que você fez para arrumar aquele problema que você nem lembra mais. Basta saber como manter os nossos playbooks (veja mais abaixo) atualizados!  

### Instalando o Ansible

Para instalar o Ansible basta executar:

```bash
python3 -m pip install --user ansible
```

> Para um guia mais completo acesse a [documentação](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installation-guide)

Você pode confirmar a sua instalação executando:

```bash
ansible --version
```

## Criando seu inventory.yaml

O arquivo `inventory.yaml` será o arquivo que iremos definir os hosts que queremos configurar. Nele é possível definir o IP, usuário, tipo de conexão e outras configurações.

Neste artigo vamos simular a configuração automatizada com o Ansible em uma máquina virtual do Fedora Server que criamos no artigo [Criando uma máquina virtual do Fedora Server 36](https://blog.ricardof.dev/criando-maquina-virtual-libvirt/), então não deixe de conferir!

Para começar, crie um arquivo chamado `inventory.yaml` e dentro dele defina:

```conf
vms:
  hosts:
    fedora:
      ansible_host: 192.168.122.143
      ansible_connection: ssh
      ansible_user: youruser
```

`ansible_host` irá definir o IP da máquina que deseja configurar e `ansible_user` o usuário. Caso esteja realizando este processo para ser executado no seu próprio computador, troque por `localhost`.

Após isso execute o comando abaixo para listar todos os hosts e verificar se o nosso novo host foi encontrado pelo Ansible:

```shell
[user@localhost ~]# ansible -i inventory.yaml all --list-hosts
hosts (1):
    fedora
```

Com o host remoto configurado precisamos agora configurar o acesso via SSH. Para fazer isso adicione a chave pública do seu computador no `authorized_keys` de cada sistema remoto que deseja configurar, neste caso a nossa máquina virtual representado pelo IP `192.168.122.143`:

```shell
[user@localhost]# ssh-copy-id -i ~/.ssh/id_rsa.pub youruser@192.168.122.143
/usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/home/youruser/.ssh/id_rsa.pub"
The authenticity of host '192.168.122.143 (192.168.122.143)' can't be established.
ED25519 key fingerprint is ...
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
youruser@192.168.122.143's password:
bash: warning: setlocale: LC_ALL: cannot change locale (pt_BR.UTF-8)
/usr/bin/sh: warning: setlocale: LC_ALL: cannot change locale (pt_BR.UTF-8)
/usr/bin/sh: warning: setlocale: LC_ALL: cannot change locale (pt_BR.UTF-8)
/usr/bin/sh: warning: setlocale: LC_ALL: cannot change locale (pt_BR.UTF-8)

Number of key(s) added: 1

Now try logging into the machine, with:   "ssh 'youruser@192.168.122.143'"
and check to make sure that only the key(s) you wanted were added.youruser
```

Com a chave pública SSH configurada, podemos pedir para o Ansible pingar a nossa máquina remota e verificar se deu tudo certo. Caso tenha dado, você verá uma mensagem como a abaixo:

```shell
[user@localhost]# ansible -i inventory.yaml vms -m ping
fedora | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
    },
    "changed": false,
    "ping": "pong"
}
```

## Criando seu playbook

Playbooks são arquivos que o Ansible irá utilizar para configurar e fazer deploy dos seus hosts remotos. É nestes arquivos que iremos definir o que queremos e como queremos configurar nossa máquina remota.

Crie um novo arquivo chamado `playbook.yaml` com o seguinte conteúdo:

```yaml
- hosts: vms
  become: true
  tasks:
    - name: Atualiza os packages com DNF
      package:
	    name: '*'
        state: latest

    - name: Mensagem
      ansible.builtin.debug:
        msg: Os packages foram atualizados!
```

Depois, execute playbook com o seguinte comando. Note que estamos passando a flag `--ask-become-pass` pois estamos executando um comando que precisa de [permissões elevadas](https://docs.ansible.com/ansible/latest/user_guide/become.html). Desta forma o Ansible irá pedir a senha antes de executar o comando.

```shell
[user@localhost]# ansible-playbook -i inventory.yaml --ask-become-pass playbook.yaml
BECOME password:

PLAY [vms] ***********************************************************************************************

TASK [Gathering Facts] ***********************************************************************************
ok: [fedora]

TASK [DNF Update] ****************************************************************************************
ok: [fedora]

TASK [Print message] *************************************************************************************
ok: [fedora] => {
    "msg": "Done!"
}

PLAY RECAP ***********************************************************************************************
fedora                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

Com o output podemos notar que o nosso playbook foi executado com sucesso e o Ansible nos traz algumas informações úteis sobre a execução.

## Trabalhando com variáveis

Temos também a flexibilidade de trabalhar com variáveis, onde podemos definir diferentes valores para diferentes hosts remotos. As variáveis podem ser definidas em playbooks ou no próprio inventário, porém, dependendo de onde forem definidas o escopo delas pode ser diferentes.

#### Definindo variáveis no inventário

Para definir uma variável dentro de um inventário e torná-la disponível para todos os seus playbooks, basta editar o nosso arquivo de inventário `inventory.yaml` e adicionar:

```yaml
vms:
  hosts:
    fedora:
      ansible_host: 192.168.122.143
      ansible_connection: ssh
      ansible_user: youruser
      remote_install_path: /usr/bin
```

E para utilizá-las eu seu playbook basta referenciá-las desta forma:

```yaml
- hosts: vms
  become: true
  tasks:
    - name: Copia script de instalação
      ansible.builtin.copy:
        src: /home/user/Downlodas/my-script.sh
        dest: '{{ remote_install_path }}/my-script.sh'
```

#### Definindo variáveis do tipo lista

Podemos definir também variáveis em forma de lista:

```yaml
vms:
  hosts:
    fedora:
      ansible_host: 192.168.122.143
      ansible_connection: ssh
      ansible_user: youruser
      folders:
        - /usr/bin
        - /usr/local/bin
```

E para utilizá-las:

```yaml
folder: "{{ folders[0] }}"
```

#### Variáveis de retorno

Ansible oferece uma maneira bem interessante de registrar variáveis que é criando elas através da saída de outros comandos e as utilizando posteriormente, por exemplo:

```yaml
- hosts: vms
  become: true
  tasks:
    - name: Checa a arquitetura
      ansible.builtin.shell: uname -m
      register: host_arch
    
    - name: Exibe arquitetura
      ansible.builtin.debug:
        msg: '{{ host_arch.stdout }}'
      
    - name: Exibe erro caso não seja suportada
      ansible.builtin.debug:
        msg: Sem suporte para aarch64
      when: host_arch.stdout == 'aarch64'	
```

É importante notar que as variáveis carregadas pelo Ansible tem uma ordem de precedência, para saber qual é esta ordem acesse [Understanding variable precedence](https://docs.ansible.com/ansible/latest/user_guide/playbooks_variables.html#understanding-variable-precedence) na documentação oficial.

## Organização do playbook

Como vimos acima podemos definir um único playbook e todas as suas tasks dentro dele. Mas e se tivermos muitas tasks que queremos deixar separadas e organizadas por contexto? Neste caso podemos importar tasks de outros arquivos e defini-las separadamente:

```yaml
- hosts: vms
  become: true
  tasks:
    - name: Configura DNF
      ansible.builtin.import_tasks: tasks/configure_dnf.yaml
    
    - name: Instala os packages básicos
	  ansible.builtin.import_tasks: tasks/install_base_packages.yaml
      
    - name: Configura o docker
      ansible.builtin.import_tasks: tasks/configure_docker.yaml
```

E o seu arquivo de tasks, por exemplo `tasks/configure_dnf.yaml`, ficaria assim:

```yaml
- name: Configura DNF para melhor performace
  ansible.builtin.blockinfile:
    backup: yes
    path: /etc/dnf/dnf.conf
    block: |
      fastestmirror=true
      max_parallel_downloads=20
```

Ou então podemos importar também outros playbooks:

```yaml
- hosts: vms
- import_playbook: webservers.yaml
- import_playbook: databases.yml
- import_playbook: containers.yml
```

## Módulos

Ansible vem com alguns módulos (vários, na verdade) para facilitar no provisionamento e gerenciamento de configurações dos seus hosts remotos. Alguns deles seguem abaixo para que você possa ter uma ideia do que é possível realizar:

- **ansible.builtin.copy**: Copiar arquivos locais ou do host remoto para algum local do host remoto;  
- **ansible.builtin.find**: Retorna uma lista de arquivos utilizando um critério;  
- **ansible.builtin.cron**: Administra as crons e as variáveis de ambiente;  
- **ansible.builtin.dnf**: Administra os packages com o package manager `dnf`;  
- **ansible.builtin.git**: Faz o deploy de softwares utilizando git checkout;  
- **ansible.builtin.pause**: Pausa a execução do playbook;  
- **ansible.builtin.lineinfile**: Lida com linhas em arquivos, altera linhas individuais em arquivos.  

Para ver a lista completa de módulos que vem integrados ao Ansible, [clique aqui](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html).

## Um solução completa

Como podemos ver acima de forma bem básica o Ansible tem muito a oferecer, é uma ferramenta completa para fazer deploy de servidores, configurá-los e manter aquele seu homelab seguro e sempre pronto para um novo setup caso aconteça alguma coisa.

Recomendo a leitura da [documentação](https://docs.ansible.com/ansible/latest/getting_started/index.html) que é bem completa e também este artigo do Martin Fowler intitulado [SnowflakeServer](https://martinfowler.com/bliki/SnowflakeServer.html).