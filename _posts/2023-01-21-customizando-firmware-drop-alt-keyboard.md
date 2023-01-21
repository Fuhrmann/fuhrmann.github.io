---
title: "Customizando firmware Drop ALT Keyboard com docker"
date: 2023-01-21
author: Ricardo Fuhrmann
layout: post
tags: firmware, drop alt, keyboard, docker
---

Se você  tem um [Drop ALT Keyboard](https://drop.com/buy/drop-alt-mechanical-keyboard?defaultSelectionIds=966466%2C966468) provavelmente já deve ter pensado em como customizar o firmware e quais as possibilidades de customizações disponíveis. 

Seja para facilitar meu dia a dia, como por exemplo, trocar a funcionalidade da tecla <kbd>Caps Lock</kbd> pela <kbd>ESC</kbd>. A questão é que existe um mundo de customizações disponíveis, basta saber por onde começar.

Abaixo escrevi um exemplo de como ativar a funcionalidade da [Leader Key](https://docs.qmk.fm/#/feature_leader_key?id=the-leader-key-a-new-kind-of-modifier), desde a preparação do keymap até a compilação e upload do firmware para o teclado! Boa sorte!

## Baixando mdloader

O [mdloader](https://github.com/Massdrop/mdloader****) será responsável por fazer upload do firmware que iremos gerar (no final deste artigo) no teclado. Para baixá-lo acesse a página de [releases](https://github.com/Massdrop/mdloader/releases) e baixe a ultima versão. Vou assumir que iremos fazer todos os passos em uma pasta chamada `keyboard`, portanto:

```bash
wget https://github.com/Massdrop/mdloader/releases/download/1.0.7/mdloader-Linux.zip -P ~/apps/keyboard
unzip ~/apps/keyboard/mdloader-Linux.zip
```

Um arquivo com o nome de `mdloader` será extraído do arquivo zipado na pasta.

## Preparando o qmk_firmware

Para compilar e customizar o firmware iremos utilizar o [qmk_firmware](https://github.com/qmk/qmk_firmware) através do docker. Para isso, crie um `docker-compose.yml` na pasta que estamos trabalhando com o seguinte conteúdo:

```yaml
version: '3.7'

services:
  qmk:
    image: qmkfm/qmk_firmware
    container_name: qmk
    volumes:
      - ./keymap:/qmk_firmware/keyboards/massdrop/alt/keymaps/custom
      - ./build:/qmk_firmware/.build
    command: "make massdrop/alt:custom"
```

Precisamos agora do keymap padrão do Massdrop ALT para que possamos customizá-lo. Para isso criei um repositório contendo o arquivo padrão que pode ser baixado:

```bash
mkdir ~/apps/keyboard/keymap
wget https://raw.githubusercontent.com/Fuhrmann/massdrop-alt-default/main/default-keymap.c -O ~/apps/keyboard/keymap/keymap.c
```

Se tudo deu certo você verá o arquivo de `keymap` em `~/apps/keyboard/keymap/keymap.c`.

## O arquivo de rules

É no arquivo `rules.mk` que o `QMK` especifica quais features serão ativadas ao compilar o firmware. Para cada feature utilizada no `keymap.c` precisamos ativá-las antes neste arquivo.

Para referência, abaixo uma lista das principais funcionalidades que podem ser ativadas e seus valores:

- **AUTO_SHIFT_ENABLE**: ativa/desativa a funcionalidade de [autoshift](https://docs.qmk.fm/#/feature_auto_shift). Aperte uma tecla para ter o seu valor, segure a tecla para ter outro.
- **AUTOCORRECT_ENABLE**: ativa/desativa a funcionalidade de auto [correção](https://docs.qmk.fm/#/feature_autocorrect). Checa por erros de ortografia.
- **CAPS_WORD_ENABLE**: ativa/desativa a funcionalidade de [caps words](https://docs.qmk.fm/#/feature_caps_word). Ativa uma versão inteligente do <kbd>Caps Lock</kbd>
- **COMBO_ENABLE**: ativa/desativa a funcionalidade de [combos](https://docs.qmk.fm/#/feature_combo). Permite a utilização de combos ao pressionar diversas teclas e executar ações personalizadas.
- **KEY_LOCK_ENABLE**: ativa/desativa a funcionalidade de [keylock](https://docs.qmk.fm/#/feature_key_lock). Mantém pressionado uma certa tecla automaticamente até o próximo pressionamento.
- **KEY_OVERRIDE_ENABLE**: ativa/desativa a funcionalidade de [key overrides](https://docs.qmk.fm/#/feature_key_overrides). Permite que você substitua o comportamento de algumas teclas modificadores (shift, ctrl) quando usadas em conjunto com outras.
- **SECURE_ENABLE**: ativa/desativa a funcionalidade [secure](https://docs.qmk.fm/#/feature_secure). Permite bloquear o input de todas as teclas até certa combinação ter sido entrada.
- **SEND_STRING_ENABLE**: ativa/desativa a funcionalidade de [send string](https://docs.qmk.fm/#/feature_send_string). Permite o envio de sequências de letras serem enviadas em atalhos personalizados.
- **TAP_DANCE_ENABLE**: ativa/desativa a funcionalidade de [tap dance](https://docs.qmk.fm/#/feature_tap_dance). Permite especificar diferentes comportamentos para uma tecla ao pressionar ela mais de uma vez rapidamente.
- **LEADER_ENABLE**: ativa/desativa a funcionalidade de [leader key](https://docs.qmk.fm/#/feature_leader_key). Permite utilizar uma tecla específica para iniciar uma combinação de keystrokes no qual irão resultar em uma ação.

> Todos as funcionalidades do arquivo `rules.mk` recebem apenas dois valores: `yes` ou `no`. Para mais informações sobre o arquivo de `rules.mk`, consulte a [documentação oficial](https://docs.qmk.fm/#/getting_started_make_guide?id=rulesmk-options).

## Ativando a funcionalidade de Leader Key

A Leader Key (`KC_LEAD`)  permite que você especifique uma tecla que será a tecla líder: você irá pressionar ela e depois uma sequência de teclas para executar uma ação especifica. 

Por exemplo, digamos que você queria de forma fácil controlar a abertura e fechamento das abas do seu browser ou do seu editor. Para tal podemos traduzir o comportamento padrão dos atalhos utilizando a leader key desta forma:

<kbd>KC_LEAD + t</kbd>: reabre a última aba aberta;

<kbd>KC_LEAD + n + t</kbd>: abre uma nova aba;

Você irá apertar a tecla que representa o <kbd>KC_LEAD</kbd>, depois a tecla <kbd>n</kbd> e por último a tecla <kbd>t</kbd> para abrir uma nova aba, em sequência.

> A tecla que será utilizada como `KC_LEAD` é definida no arquivo  `keymap.c` mais abaixo!

Agora que sabemos as possíveis principais funcionalidades e como ativá-las crie o arquivo de `rules.mk`:

```bash
vi ~/apps/keyboard/keymap/rules.mk
```

E dentro dele vamos especificar que queremos a funcionalidade de `leader key` ativada

```
LEADER_ENABLE = yes
```

Você verá o arquivo de `rules.mk` em `~/apps/keyboard/keymap/rules.mk`.

## Criando o arquivo de configuração

Algumas das funcionalidades disponibilizadas pelo `QMK` podem ser personalizadas mais ainda, como por exemplo, mudar o timeout da leader key, personalizar comportamentos de teclas específicas, entre outros. Neste caso, vamos definir essas configurações em um arquivo chamado `config.h`. 

Como estamos ativando a funcionalidade de `leader key`, vamos alterar uma configuração específica dela que permite aumentar/diminuir o tempo de pressionamento das sequências. Para isso, crie o arquivo `config.h`:

```bash
vi ~/apps/keyboard/keymap/config.h
```

Com o seguinte conteúdo:

```
#pragma once

// Configura o timeout de pressionamento das sequências da leader key
#define LEADER_TIMEOUT 300

// Reseta o timeout após cada pressionamento
#define LEADER_PER_KEY_TIMING
```

Você verá o arquivo de `config.h` dentro da pasta `~/apps/keyboard/keymap/config.h`.

## Configurando a leader key

Para especificar a leader key (`KC_LEAD`) temos que editar o arquivo `~/apps/keyboard/keymap/keymap.c` que baixamos no início do artigo. Ao abrir este arquivo você irá se deparar com o layout do keyboard definido desta forma:

![[Pasted image 20230115222526.png]]

Onde cada linha de teclas do seu teclado é representado por uma linha da tabela acima e cada coluna respectivamente.

Note que existem três camadas (layers): a primeira (0) será a sua camada padrão das teclas, que ao pressionar qualquer tecla terá seu comportamento executado. A segunda (1) é uma camada que só é ativada com o pressionamento de uma tecla específica, neste caso representado pelo keycode `MO(1)`. E a terceira cama (X) está desativada.

Na primeira camada, escolha uma das teclas para representar a sua tecla `KC_LEAD`. Neste caso eu defini a <kbd>Caps Lock</kbd>:

![[Pasted image 20230115223236.png]]

## Especificando o comportamento

Ainda no arquivo `keymap.c` vamos adicionar uma nova função que será responsável por lidar com o comportamento das teclas que são pressionadas logo após o pressionamento da `KC_LEAD`. Para isso acrescente o código abaixo no final arquivo:

```c
LEADER_EXTERNS();

void matrix_scan_user(void) {
  LEADER_DICTIONARY() {
    leading = false;
    leader_end();
    
    // Aqui configuramos as sequências
    SEQ_ONE_KEY(KC_T) {
      // Ao pressionar KC_LEAD e então T, envia CTRL + SHIFT + T
      SEND_STRING(SS_LCTRL(SS_LSFT("t")));
    }

     SEQ_TWO_KEYS(KC_N, KC_T) {
      // Ao pressionar KC_LEAD and então N seguido de T, envia CTRL + T
      SEND_STRING(SS_LCTRL("t"));
    }
  }
}
```

Com a função criada estamos prontos para compilar o firmware e usar o `mdloader` para fazer upload do firmware no teclado.

## Compilando e enviando o firmware

A compilação é simples, basta executar:

```bash
docker compose run --rm qmk
```

Se tudo deu certo você verá essa mensagem no final:

```bash
QMK Firmware 0.13.26
Making massdrop/alt with keymap custom
..
..
..
..
Copying massdrop_alt_custom.bin to qmk_firmware folder                                              [OK]
(Firmware size check does not yet support cortex-m4 microprocessors; skipping.)
```

Após a compilação do firmware, temos que enviá-lo para o teclado. Para fazer isso vamos utilizar o `mdloader`. Os passos serão o seguinte:

1. Executar `./mdloader --first --download build/massdrop_alt_custom.hex --restart` para iniciar o upload do firmware para o teclado;
2. Apertar as teclas <kbd>Fn</kbd> + <kbd>b</kbd> por meio segundo para reiniciar o teclado no bootloader. Você pode achar essa tecla representada no `keymap.c` por meio do keycode `MD_BOOT` na layer 1;
3. Aguarde o teclado reiniciar e pronto. As leds devem acender e o teclado está pronto para utilização.

Abaixo um exemplo da saída esperada pelo comando se tudo der certo:

```bash
./mdloader --first --download build/massdrop_alt_custom.hex --restart
Massdrop Loader 1.07

Massdrop Loader  Copyright (C) 2018-2022 Massdrop Inc.
This program is Free Software and has ABSOLUTELY NO WARRANTY

Scanning for device for 60 seconds
.........
Device port: /dev/ttyACM0 (SAMD51J18A)

Opening port '/dev/ttyACM0'... Success!
Found MCU: SAMD51J18A
Bootloader version: v2.18Sep  4 2018 16:48:28
Applet Version: 1
Writing firmware... Complete!
Booting device... Success!
Closing port... Success!
```

Agora só testar se a sua leader key com as sequências funcionam:

<kbd>KC_LEAD + t</kbd>: reabre a última aba aberta;

<kbd>KC_LEAD + n + t</kbd>: abre uma nova aba no browser;

## O arquivo Makefile

Eu uso um arquivo `Makefile` que ajuda a não precisar ficar memorizando os comandos:

```
compile:
	docker compose run --rm qmk

flash:
	./mdloader --first --download .build/massdrop_alt_custom.hex --restart
```

Com este arquivo, os passos são esses:

1. Editar o arquivo `keymap.c`
2. make compile
3. make flash
4. Pressione <kbd>Fn</kbd> + <kbd>b</kbd> para reiniciar no bootloader
5. Profit!

## Resolvendo problemas

#### O mdloader não reconhece o teclado
- *Certifique-se que tenha apertado as teclas <kbd>Fn</kbd> + <kbd>b</kbd> para reiniciar o teclado em modo do bootloader por pelo menos meio segundo*. Você pode conferir qual tecla é utilizada para reiniciar para o modo bootloader no arquivo `keymap.c`, que é representado pelo keycode `MD_BOOT`.
- Talvez você precise adicionar o seu usuário no grupo `dialout` para acessar a porta do device;

## Links úteis e leitura recomendada

[Repositório com conteúdo deste artigo](https://github.com/Fuhrmann/massdrop-alt-default)

[The QMK Tutorial](https://docs.qmk.fm/#/newbs?id=the-qmk-tutorial)

[The Leader Key: A New Kind of Modifier](https://docs.qmk.fm/#/feature_leader_key)

[QMK Chearsheet](https://jayliu50.github.io/qmk-cheatsheet/)

[QMK Basics: Leader Key, using sequences for shortcuts](https://thomasbaart.nl/2018/12/20/qmk-basics-leader-key/)

[mdloader docs](https://github.com/Massdrop/mdloader/blob/master/README.md)

*[QMK]: Quantum Mechanical Keyboard
*[MO(1)]: Momentaneamente ativa a layer 1. Assim que a tecla for solta, a layer é desativada.