---
title: Como remover efeito glow em ListView no Flutter
tags: flutter
---

## Introdução

Se você já usou a `ListView` do Flutter deve ter notado que no Android um efeito glow é adicionado por padrão:

| ![Atalhos](/images/efeito_glow_list_view.jpg) | 
|:--:| 
| *Exemplo do efeito glow em ListView* |

Este efeito está representado no Flutter pelo widget [GlowingOverscrollIndicator](https://github.com/flutter/flutter/blob/master/packages/flutter/lib/src/widgets/overscroll_indicator.dart#L44) e indica quando um scroll atingiu o seu limite:

| ![Atalhos](/images/efeito_glow_definido.png) | 
|:--:| 
| *Parte do código onde o efeito é definido* |

As vezes pode ser útil, quando temos uma página inteira que é somente uma ListView. Mas existem casos que você possa querer remover este efeito. Atualmente não existe nenhuma flag que possa ser definida no `ListView`, mas ainda sim é bem fácil de remover este efeito.

## Como remover

Crie uma classe no seu projeto que estenda `ScrollBehavior`:

```dart
class NoGlowBehavior extends ScrollBehavior {
  @override
  Widget buildViewportChrome(BuildContext context, Widget child, AxisDirection axisDirection) {
    return child;
  }
}
```

E basta aplicá-lo como um widget pai nas `ListView` que deseja remover:

```dart
ScrollConfiguration(
  behavior: NoGlowBehavior(),
  child: ListView(
    ...
  ),
)
```

Se você chegou até aqui e deseja mudar a cor do efeito ao invês de remove-lo, basta alterar o `ThemeData.accentColor` do seu projeto que a cor será aplicada em todos os scrollables.
