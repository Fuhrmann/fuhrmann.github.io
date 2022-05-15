---
title: "Produtividade com i3wm"
date: 2022-05-15
author: Ricardo Fuhrmann
layout: post
permalink: /produtividade-com-i3wm/
tags: software
---

Ser produtivo também significa ser capaz de navegar entre as janelas abertas no seu computador de forma eficiente no dia a dia. O famoso ALT + TAB para quem está acostumado pode não parecer tão ruim, mas é possível melhorar muito a nossa eficiência na hora de procurar aquela janela aberta com nossos arquivos ou aquela conversa no Slack que você recebeu uma notificação.

No Linux temos diversos gerenciadores de janelas que permitem diversos setups e entre eles está o que vamos falar hoje que é o I3.

## Importantes distinções

Antes de falarmos no i3 é importante definir alguns termos e onde exatamente o i3 se encaixa no mundo do Linux:

- Display servers: responsável por criar o ambiente gráfico (Xorg, XFree86, X11);
- Login managers: responsável por permitir logar no sistema local (gdm, lightdgm, cdm);
- **Gerenciadores de janelas: controla a distribuição e decoração das janelas (i3, fluxbox, metacity)**
- Desktop environment: suite de aplicações que compartilham da mesma interface gráfica (KDE, GNOME, XFCE)

## O que é o I3

É um gerenciador de janelas que suporta empilhamento, layout em abas e o tilling de janelas, que é o que torna o I3 tão eficiente. O layout em tilling organiza as janelas de forma que elas não se sobrepõem, organizando-as tanto na vertical como na horizontal.

![1.png](/assets/images/produtividade-com-i3wm/1.png)

É possível organizar suas janelas em diferentes workspaces, configurar atalhos para abrir programas com tamanhos específicos, janelas flutuantes, suporte a múltiplo monitores e muito mais.

## A tecla $mod

O i3 faz uso de uma tecla modificadora, que por padrão é ALT (Mod1), mas que foi sendo adotada pela comunidade com a tecla SUPER (tecla Windows, Mod4). Essa tecla modificadora é usada em conjunto de outras teclas para os atalhos definidos no arquivo de configuração, como por exemplo:

```
# Abrir o terminal
$mod+ENTER

# Fechar uma janela
$mod+SHIFT+Q

# Ir para o workspace 1
$mod+1
```

Durante este artigo será mostrado o atalho padrão de alguns comandos utilizando `$mod` para poder exemplificar algumas ações.

## Workspaces

Essa é uma funcionalidade de grande importância na hora de organizar suas janelas: é uma forma fácil de agrupar um conjunto de janelas em diferentes lugares, áreas de trabalho. Por exemplo, você escolhe o seu browser para abrir sempre no workspace 1, seus aplicativos de comunicação no workspace 2, no workspace 3 você específica para abrir sempre no seu segundo monitor com o Spotify e assim por diante.

Para mudar de um workspace para o outro basta pressionar `$mod` + `num` onde `num` é o número do workspace que você deseja abrir.

Para mudar uma janela para outro workspace basta pressionar `$mod` + `Shift` + `num` onde `num` é o número do workspace que você deseja enviar a janela.

## Configurando o i3

Para configurar o i3 basta editar o seu arquivo de configurações localizado em `~/.config/i3/config`. Ao executar o i3 pela primeira vez será oferecido uma opção para você gerar um arquivo de configuração com alguns padrões, que é sempre uma boa idéia aceitar, caso você ainda não tenha um arquivo já configurado.

Este arquivo terá alguns defaults bem interessantes para iniciar o uso do i3 como por exemplo alguns atalhos, regras de janelas entre outros. Vale lembrar que pelo arquivo de configurações é possível definir variáveis:

```
# Define workspaces
set $console "1: term"
set $browse "2: browse"
set $code "3: code"
set $study "4: study"

# Uso das variáveis:
# Atalho para acessar o workspace $study
bindsym $mod+4 workspace $study

# Associa sublime text a abrir sempre no workspace $study
assign [class="^Sublime_text$"] $study
```

### Definindo atalhos

Para definir atalhos para programas basta utilizar:

```
bindsym [--release] [<Group>+][<Modifiers>+]<keysym> command
bindcode [--release] [<Group>+][<Modifiers>+]<keycode> command
```

Alguns exemplos de atalhos:

```
# Ativa/desativa fullscreen
bindsym $mod+f fullscreen toggle

# Reinicia i3
bindsym $mod+Shift+r restart

# Simula o pressionamento de ctrl+v ao pressionar $mod+x
bindsym --release $mod+x exec --no-startup-id xdotool key --clearmodifiers ctrl+v
```

### Definindo regras para janelas

No i3 é possível especificar regras para diferentes janelas como comportamento, tamanho, local que a janela irá abrir. Para fazer isso é necessário ter em mãos uma ou mais das seguintes propriedades de uma janela: título ou classe. Para descobrir o título ou classe de uma janela podemos utilizar a ferramenta `xprop` que ao ser executada no terminal mudará o seu cursor para uma cross e você deverá clicar em cima da janela que deseja obter as informações. Após isso será apresentado no terminal a seguinte saída, com informações da janela.

```
**WM_CLASS(STRING) = "Alacritty", "Alacritty"**
XdndAware(ATOM) = BITMAP
_MOTIF_WM_HINTS(_MOTIF_WM_HINTS) = 0x2, 0x0, 0x1, 0x0, 0x0
_NET_WM_NAME(UTF8_STRING) = "Alacritty"
WM_NAME(STRING) = "Alacritty"
```

![7.gif](/assets/images/produtividade-com-i3wm/7.gif)

O texto em negrito representa a classe. Neste caso podemos criar uma regra para janelas da classe `Alacritty` para se comportarem de maneira flutuante e serem movidas para o workspace 4 com a seguinte configuração:

```
for_window [class="^Alacritty$"] floating enable, move container to workspace 4
```

Alguns outros exemplos:

```
# Adiciona borda de 1 pixel em todas as janelas removendo a decoração
for_window [class=".*"] border pixel 1

# Desativa o floating da janela e a torna fullscreen
for_window [class="^deadcells$"] floating disable, fullscreen enable

# Específica o tamanho que a janela tem que abrir, ativa o modo float e centraliza
for_window [class="^terminal"] resize set 900 600, floating enable float, move position center
```

### Definindo atalhos para programas

Para executar um programa é utilizado a seguinte sintaxe:

```
exec [--no-startup-id] <command>
```

Exemplos:

```
# Atalho para iniciar o google chrome
bindsym $mod+g exec google-chrome

# Abre as configurações do i3 no sublime
bindsym $mod+i exec --no-startup-id subl ~/.config/i3/config

# Muda o foco, faz split do container na vertical e inicial o terminal
bindsym Control+Mod1+d focus child; split v; exec --no-startup-id i3-sensible-terminal
```

### Associando janelas a workspaces

Uma grande funcionalidade é poder associar janelas a workspaces. Digamos que você faça um mapa mental que sempre o seu navegador estará no workspace 2, o Slack no workspace 8, o Spotify no workspace 7 e assim por diante. Fica muito mais fácil de encontrar aquilo que você procura não?
Para fazer isso basta acrescenter algumas regras da mesma forma que fazemos para a diretiva do `for_window` só que a sintaxe é um pouco diferente:

```
assign <criteria> [→] [workspace] [number] <workspace>
assign <criteria> [→] output left|right|up|down|primary|<output>
```

Exemplos:

```
# Associa o google chrome a abrir sempre no workspace número 2
assign [class="Google-chrome" window_role="browser"] 2

# Associa o PHPStorm a abrir sempre no workspace número 3
assign [class="jetbrains-phpstorm"] 3

# Associa o google chrome para abrir no monitor a direita do atual
assign [class="Google-chrome"] output right

# Associa o google chrome para abrir no monitor primário
assign [class="Google-chrome"] output primary
```

Existem muito mais possibilidades de configuração do i3 do que as que foram citadas aqui. Para ver mais consulte a [documentação aqui](https://i3wm.org/docs/userguide.html).

## Avançado: Como funciona o i3

No i3 toda informação sobre os workspaces e janelas é guardada em uma árvore. O nó raiz desta árvore é a janela raiz do X11 (display server), seguido pelos containers, workspaces e por fim as próprias janelas.

Containers podem ter outros containers ou janelas dentro de si próprios. Cada container tem uma orientação (horizontal, vertical ou não especificada) e a orientação depende do layout que o container está. Abaixo um exemplo de um container com splith, duas janelas:

![2.png](/assets/images/produtividade-com-i3wm/2.png)

E um exemplo de como ficaria representada na árvore:

![3.png](/assets/images/produtividade-com-i3wm/3.png)

Acima temos um exemplo de quando iniciamos o i3 com um único monitor, temos um único workspace e duas janelas de terminal. O layout padrão deste workspace é chamado de SPLITH. Se você resolver mudar o layout para SPLIV (vertical) e abrir dois terminais, acabará com algo assim:

![4.png](/assets/images/produtividade-com-i3wm/4.png)

Para deixar ainda mais interessante o i3 permite fazer o split de qualquer coisa. Digamos que no exemplo anterior você deseja abrir mais um terminal, mas dessa vez ao lado direito do TERMINAL2. Basta colocar o foco na janela do TERMINAL2 e fazer o SPLITH ($mod+H):

![5.png](/assets/images/produtividade-com-i3wm/5.png)

Exemplo de como ficaria a árvore:

![6.png](/assets/images/produtividade-com-i3wm/6.png)

## O i3 ajuda, mas não faz milagre

Com tudo já podemos ter uma idéia das possibilidades no gerenciamento e organização das janelas do ambiente e de como o i3 trabalha por trás dos panos para garantir essa flexibilidade no seu funcionamento.

Apesar do i3 nos dar a possibilidade de sermos mais ágeis e produtivos no nosso dia a dia, é de nossa responsabilidade ter um tempo para configurá-lo da melhor forma que atenda as nossas necessidades. Este tempo pode ser custoso, mas o resultado é incrível: permite que você faça muito além do básico em um sistema operacional do que aquilo que estamos acostumados.

Para uma visão mais completa de tudo que é oferecido pelo i3 deixo alguns links úteis abaixo para consulta (vale a pena!):

- [i3 user’s guide](https://i3wm.org/docs/userguide.html)
- [Alguns exemplos de telas](https://i3wm.org/screenshots/)
- [i3 Github discussions](https://github.com/i3/i3/discussions)
- [Debugando i3](https://i3wm.org/docs/debugging.html)
- [i3 IPC interface](https://i3wm.org/docs/ipc.html)
