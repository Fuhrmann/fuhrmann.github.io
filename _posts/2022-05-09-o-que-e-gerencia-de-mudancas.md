---
title: "O que é Gerência de Mudanças"
date: 2022-05-09
author: Ricardo Fuhrmann
layout: post
permalink: /o-que-e-gerencia-de-mudancas/
tags: software
---

Nem todas mudanças em um projeto podem ou devem ser implementadas, algumas aumentam os custos do projeto, introduzem comportamentos inesperados ou não são de grande benefício para a maioria dos usuários. Tais mudanças se não forem controladas com algum tipo de critério podem prejudicar e causar instabilidade em um sistema estável.

O gerenciamento de mudanças ajuda na identificação e no gerenciamento das possíveis mudanças de um projeto, fazendo a análise dos impactos no cronograma, orçamento e qualidade. Com isso podemos garantir que tais mudanças sejam compatíveis com o projeto, aprovadas pelos envolvidos neste processo, diminuindo o impacto causado, assim como os custos.

## Modelo de Ian Sommerville

Um modelo muito conhecido para gerenciamento de mudanças é de Ian Sommerville. Neste modelo os passos seguem a seguinte ordem:

- O cliente envia a solicitação de mudança utilizando um formulário modelo;
- Alguém da equipe faz a análise deste pedido e verifica se é válido;
- Caso a mudança seja valida a equipe de desenvolvimento estuda qual a complexidade para	implantação desta mudança, incluindo na conta final o custo e o	impacto no projeto;
- A equipe responsável pelo controle de mudanças ou o próprio time de desenvolvimento faz	validação de quais das mudanças requisitadas serão realizadas e	quais deverão ser fechadas/rejeitadas;
- A equipe de desenvolvimento	implementa as mudanças aprovadas e faz os testes necessários para	garantir seu funcionamento;
- No fim, com todos os testes realizados e aprovados, a mudança	é considerada como completa e é fechada.

Algumas perguntas importantes a serem feitas na análise de uma mudança requisitada são:

- A mudança está relacionada com o	escopo do projeto?
- Qual o motivo dessa mudança ter	sido requisitada?
- Há benefícios para o projeto	como um todo caso essa mudança seja implementada?
- O tempo de conclusão do projeto	aumentará muito com essa mudança
- A mudança já foi realizada de alguma outra forma?

Tais perguntas devem estar em conformidade com a equipe para evitar que mudanças não necessárias passem para a equipe de desenvolvimento e sejam fechadas por algum motivo não coberta na pré análise da mesma.

## Implementar ou não uma mudança

As consequências de fazer ou não uma mudança em um projeto devem ser avaliadas com cuidado. A mudança está relacionada a um bug do sistema? Será preciso levar em consideração a gravidade do bug, o quão rápido a mudança deverá ser implementada priorizando tarefas da equipe de desenvolvimento.

Também é importante medir se a mudança traz benefícios para toda base de usuários do sistema ou só uma pequena parte. O esforço e o tempo para implementar a mudança está de acordo com a quantidade de usuários beneficiados? Caso não, a prioridade para a mudança pode ser baixa dando prioridade para outras que trarão mais benefícios.

Os custos para realizar a mudança também entram na conta. Mudanças que necessitam de alterações em muitas partes do sistema podem introduzir bugs, aumentando assim o tempo de desenvolvimento e consequentemente os custos..

## Ferramentas

Algumas ferramentas podem ser úteis no processo de gerenciamento de mudanças, como as citadas abaixo:

- JIRA: permite o gerenciamento de	mudanças focado em projetos ágeis. Permite a organização das	mudanças utilizando categorias, status personalizados, separação	por projetos, responsáveis e equipes.

- TRELLO: utiliza o modelo Kanban para gerenciamento de projetos. Cada projeto tem seu board com	listas dinâmicas criadas pelo próprio time. Com um pouco de	criatividade é possível fazer um gerenciamento de mudanças de	forma bem organizada. Oferece categorização de cards por labels,	prazo de entregas, anexos de documentos e mais.

- GITHUB: plataforma de hospedagem de código fonte e arquivos	com controle de versão que conta também com gerenciamento de	mudanças. Lá é possível categorizar as mudanças com labels,	utilizar bots para fechar mudanças que não serão implementadas. A	vantagem é que o gerenciamento de mudanças está estritamente	ligado ao controle de versão tornando fácil assim referenciar em	commits qual mudança é afetada, gerenciar branchs relacionados a	mudanças e mais.
