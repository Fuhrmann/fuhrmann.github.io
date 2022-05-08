---
title: "Introdução à Gerência da Configuração"
date: 2022-05-08T00:00:00+00:00
author: Ricardo Fuhrmann
layout: post
permalink: /introducao-gerencia-da-configuracao/
categories: introducao
---

Softwares estão sempre mudando, novas versões, novos recursos, novos requisitos. A maioria dos sistemas pode ser pensado como um conjunto de versões, onde cada uma necessita de gerenciamento e que seja mantido um histórico. Este gerenciamento de configuração está ligado com as políticas, processos e ferramentas para o gerenciamento de mudanças de tais sistemas.

É necessário que possamos controlar itens de configuração do projeto e seu versionamento, incluindo as funcionalidades, características, documentação, banco de dados e outros itens.

Os processos e as ferramentas de gerenciamento da configuração fazem parte também de exigências de algumas certificações e além disso adotar tais processos significa ter controle sobre quais mudanças foram realizadas no sistema de forma ágil, ter acesso a informação de desenvolvimento e não interferir no trabalho de outras equipes, evitando assim conflitos.

## Atividades da Gerência de Configuração

**Gerenciamento de mudanças** tem como objetivo controlar mudanças de um sistema, como por exemplo, resolução de bugs ou o desenvolvimento de novas funcionalidades. É neste processo que é feita a estimativa de custos e do impacto de tais mudanças na equipe.

**Gerenciamento de versões** acompanha a evolução das versões do sistema e garante livre interferência entre partes do sistema.

**Gerenciamento da construção** do sistema faz a montage de componentes do sistema, dados e bibliotecas, executa a compilação e o relacionamento dos mesmos.

**Gerenciamento de releases** prepara o software para release e mantém um histórico das versões lançadas durante toda sua vida.

## Distinção dos termos

É importante distinguir alguns termos presentes em gerencia da configuração e seus significados:

**Controle de configuração** é o processo que irá garantir que as versões de sistemas e de seus componentes sejam registradas e mantidas. Tais mudanças devem ser gerenciadas e armazenadas pela vida toda do sistema.

**Item de configuração** é qualquer coisa que pertença a um projeto de software que foi colocado sobre controle de configuração. Por exemplo: código fonte, documentação, dados de testes, modelos. Ou seja, qualquer item que possa ter mudanças durante o ciclo de vida de um projeto e que possa ser rastreado.

**Versão** é um item de configuração que possui um identificador único e que se diferencia entre outras versões da mesma instância. Dentro de versões temos releases que são versões que foram liberadas para uso fora do ambiente de desenvolvimento.

**Baseline** é a situação de uma coleção de itens de configuração similares em um momento dado do ciclo de vida de um software. Um conjunto de itens de configuração identificados e liberados para uso, independente da versão.

**Branch** faz parte da atividade de controle de versões e pode ser definido como a ação de se iniciar uma nova linha de desenvolvimento em paralelo com uma já existente.
