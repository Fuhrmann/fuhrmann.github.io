---
title: "Personalizando o seu terminal Tilix"
date: 2019-06-04T00:00:00+00:00
author: Ricardo Fuhrmann
layout: post
permalink: /personalizando-terminal-tilix/
categories: terminal
---

## Introdução

Tilix é um terminal do tipo tiling, isso quer dizer que pode rodar mais de um terminal na mesma janela ao mesmo tempo. Dependendo das suas necessidades isso pode significar um bom aumento na sua produtividade e diminuir muito o uso do seu `alt+tab`! O projeto é open source e seu código pode ser encontrado [aqui](https://github.com/gnunn1/tilix/).

## Instalação

Para instalar é fácil, dependendo da sua distribuição o processo pode ser diferente:

| Distribuição | Onde                                                                                                                                                                                        |
|--------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Antergos     | Disponível no repositório padrão                                                                                                                                                            |
| Archlinux    | pacman -S tilix                                                                                                                                                                             |
| CentOS       | Disponível via [EPEL](https://copr.fedorainfracloud.org/coprs/ivoarch/Tilix/)                                                                                                               |
| Fedora       | Para Fedora 24/25, disponível no repositório [COPR](https://copr.fedorainfracloud.org/coprs/heikoada/terminix). Para Fedora 26 e versões mais novas, está disponível no repositório padrão. |
| Ubuntu       | Disponível para [artful](https://packages.ubuntu.com/artful/tilix) e [bionic](https://packages.ubuntu.com/bionic/tilix)                                                                     |

## Temas

Como a maioria dos terminais Tilix também dá suporte a personalização da sua paleta de cores:

| ![Personalização das cores](/assets/images/personalizando-tilix/tilix_cores.png) |
|:--:|
| *Visão geral do tema* |

Os temas inclusos no Tilix podem ser encontrados na pasta `/usr/share/tilix/schemes` e você pode criar os seus próprios temas colocando-os na pasta `~/.config/tilix/schemes`. Um exemplo de tema seria:

```json
{
    "name": "MeuTema",
    "comment": "Um tema personalizado para Tilix",
    "foreground-color": "#c9cacc",
    "background-color": "#1d1f21",
    "use-theme-colors": false,
    "use-highlight-color": false,
    "highlight-foreground-color": "#1958a6",
    "highlight-background-color": "#c9cacc",
    "use-cursor-color": false,
    "cursor-foreground-color": "#ffffff",
    "cursor-background-color": "#efefef",
    "use-badge-color": true,
    "badge-color": "#8abeb7",
    "palette": [
        "#1d1f20",
        "#bd3327",
        "#2bbc8a",
        "#f99157",
        "#1f77b3",
        "#ca99ca",
        "#6f9a9c",
        "#eeeeee",
        "#1d1f20",
        "#bd3327",
        "#2bbc8a",
        "#f99157",
        "#1f77b3",
        "#ca99ca",
        "#6f9a9c",
        "#eeeeee"
    ]
}
```

Resultando em:

| ![Tema Personalizado](/assets/images/personalizando-tilix/tilix_tema.png) |
|:--:|
| *Um tema personalizado! Não muito bonito, mas já é algo* |

A paleta de cores pode parecer um pouco confusa, mas podemos organizá-la assim:

```
"#1d1f20", // referencia a preto
"#bd3327", // referencia a vermelho
"#2bbc8a", // referencia a verde
"#f99157", // referencia a laranja
"#1f77b3", // referencia a azul
"#ca99ca", // referencia a roxo
"#6f9a9c", // referencia a turquesa
"#eeeeee", // referencia a cinza
"#1d1f20", // referencia a preto claro
"#bd3327", // referencia a vermelho claro
"#2bbc8a", // referencia a verde claro
"#f99157", // referencia a laranja escuro
"#1f77b3", // referencia a azul claro    
"#ca99ca", // referencia a roxo claro
"#6f9a9c", // referencia a turquesa clara
"#eeeeee" // referencia a cinza claro
```

Onde cada linha é traduzida para as cores padrões do Tilix:

| ![Tema Personalizado](/assets/images/personalizando-tilix/tilix_cores_padroes.png) |
|:--:|
| *As cores configuráveis disponíveis* |

### Links Úteis

- [Tilix Themes](https://github.com/storm119/Tilix-Themes) - Repositório com diversos temas.
- [gogh-to-tilix](https://github.com/isacikgoz/gogh-to-tilix) - Uma ferramenta para gerar temas a partir de outros temas.
- [snazzy-theme](http://rodrigo.ebrmx.com/github_/pablohenriq/snazzy-theme) - Um tema elegante com cores brilhantes.

## Perfis

Tilix suporta múltiplos perfis, isso quer dizer, dependendo da sua necessidade você pode configurar cores, título do terminal, tamanho e entre outras personalizações por perfil! Por exemplo, você pode ter um perfil que executa um comando sempre que inicia, outro perfil que executa um shell específico, outro perfil para quando você estiver trabalhando a noite, de dia, as possibilidades são diversas.

Para criar novos perfis basta clicar no ícone de mais que tem na parte inferior direta das configurações que um novo perfil será criado:

| ![Perfis](/assets/images/personalizando-tilix/tilix_perfil.png) |
|:--:|
| *É possível criar diversos perfis* |

É possível também configurar o Tilix para detectar automaticamente qual perfil ele deve usar dependendo do usuário, host ou diretório. A configuração é feita na aba `Avançado`, na sessão do seu perfil. Para adicionar uma deteção automática baseado no host, por exemplo, em alguma conexão ssh sua, basta adicionar:

`usuario@hostname:directory`

Onde `usuario`, `hostname` ou `directory` podem ser omitidos, mas não todos. Pelo menos um delimitador também tem que estar presente ou `@` ou `:`.

### Links Úteis

- [Automatic Profile Switching](https://gnunn1.github.io/tilix-web/manual/profileswitch/) - Documentação oficial sobre detecção automática de perfis.

## Atalhos

Tilix já vem com um monte de atalho pré-definidos, mas estes são os que eu considero mais úteis e as minhas bindings:

- Adicionar terminal abaixo: `Ctrl+Alt+D`
- Adicionar terminal à direita: `Ctrl+Alt+R`
- Alternar para o próximo terminal: `Ctrl+Tab`
- Alternar para o terminal anterior: `Ctrl+Shift+Tab`
- Abrir as preferências: `Ctrl+Alt+S`
- Abrir uma nova sesão: `Ctrl+Shift+T`
- Abrir uma nova janela: `Ctrl+Shift+N`
- Redimensionar terminal para baixo: `Alt+Shift+Abaixo`
- Redimensionar terminal para esquerda: `Alt+Shift+Esquerda`
- Redimensionar terminal para direita: `Alt+Shift+Direita`
- Redimensionar terminal para cima: `Alt+Shift+Acim`

É possível alterar os atalhos e configurá-los do jeito que quiser, para isso basta acessar a aba `Atalhos do teclado`:

| ![Atalhos](/assets/images/personalizando-tilix/tilix_atalhos.png) |
|:--:|
| *Exemplo de alguns dos atalhos* |

## Tiling

Tiling pode ser de grande ajuda em sua produtivade no dia a dia. Precisa logar no seu servidor via ssh? `Ctrl+Alt+R` e um novo terminal aparece na mesma janela que você já abriu! Precisa voltar para sua máquina para consultar um arquivo? `Ctrl+Shift+Tab` e você está de volta no terminal logado em sua máquina. Tudo isso acontencendo em uma só janela.

| ![Atalhos](/assets/images/personalizando-tilix/tilix_tiling.png) |
|:--:|
| *Uma janela, diversos terminais* |

## Conclusão

Aconselho você a dar uma olhada na [Documentação Oficial](https://gnunn1.github.io/tilix-web/manual/), visitar o [repositório](https://github.com/gnunn1/tilix/) e tentar mexer com as configurações para customizá-lo o máximo possível.
