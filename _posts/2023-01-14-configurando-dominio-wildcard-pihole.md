---
title: "Configurando domínio wildcard na Pihole"
date: 2023-01-14
author: Ricardo Fuhrmann
layout: post
permalink: /configurando-dominio-wildcard-pihole/
tags: linux, pihole
---

Caso esteja utilizando a Pihole no seu setup atual para bloquear propagandas e esteja configurada como o seu DNS resolver essa é uma das formas de fazer a resolução de subdomínios para um domínio específico.

No seu ambiente de desenvolvimento quando quiser apontar subdomínios diferentes de um mesmo domínio, exemplo, `*.local.dev`, onde o asterisco pode ser qualquer subdomínio devemos configurar a PiHole para saber como resolver e para qual IP apontar, visto que ela está sendo usada como servidor de DNS principal da rede.  

Para fazer isso entre na pasta `/etc/dnsmas.q`  (caso esteja utilizando a Pihole através do docker, a pasta é `etc-dnsmasq.d`) e crie um novo arquivo chamado `02-my-wildcard-dns.conf` com o seguinte conteúdo:

``` 02-my-wildcard-dns.conf
address=/local.dev/192.168.1.20
```

> Onde o IP acima é o IP da máquina que você deseja que o domínio acima seja resolvido.

Salve o arquivo e reinicie o serviço de DNS:

```bash
pihole restartdns
```

Ou pode fazer pela interface gráfica em `Settings > Restart DNS resolver`.  Após isso todos os subdomínios ( `ex.: meuprojeto.local.dev` ) e o próprio domínio  `local.dev` irão resolver para o IP especificado.