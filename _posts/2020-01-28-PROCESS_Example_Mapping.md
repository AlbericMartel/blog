---
layout: post
title:  "Example mapping"
categories: Process
tags: Process US
author: Albéric Martel
description: Atelier d'Example mapping pour discuter et préciser une User Story.
---

# Example mapping

_Retour d'un atelier organisé par Bruno Boucard dans une meetup du groupe DDD/Software craftmanship à Lyon le 27-01-2020_

## Qu'est que c'est ?
Avant de pouvoir développer une feature, il vaut mieux être certain que tous les acteurs impliqués aient la même compréhension
des besoins client.
L'example mapping est un atelier créé par Matt Wynne dont le but est d'approfondir et raffiner une User Story. 
Avec cet atelier, nous pouvons mieux définir toutes les exigences nécessaires au développement, les raffiner, et subdiviser
une User Story. 
De plus, cet atelier permet de faire ressortir par la discussion des cas non pensés auparavant.
Ici, nous n'allons pas définir une US de zéro, mais bien discuter d'une US déjà pensée.
Par contre, le but n'est pas de concevoir un système complet.

## Comment se déroule cet atelier ?
Avant l'atelier, l'expert métier doit avoir déjà bien travaillé le sujet. 
Il est essentiel d'expliciter les besoins de façon à ce qu'ils soient clair un maximum entre tous les acteurs du développement de la feature.

Les acteurs que l'on veut impliquer sont ceux qui vont contribuer au développement effectif de la feature. En général :

- Un expert du domain, du business
- Les développeurs qui implémenteront l'US
- Un QA/testeur

Au début de l'atelier, l'expert business va raconter son histoire sur la feature, puis les autres vont approfondir le sujet en discutant
avec lui pour diviser l'US en règles et exemples.

Pour cela, on va utiliser des post-it :
- Jaunes pour l'US
- Bleus pour les règles
- Verts pour les exemples/use cases
- Rouges pour les questions sans réponse

Les règles représentent les critères d'acceptance.
Pour chaque règle, définir un ou plusieurs exemples (passants, non passants). Ils sont la traduction directe des tests d'acceptance.
Un exemple peut être matérialisé par un Given/When/Then, un dessin ou n'importe quoi qui permettra d'illustrer la règle.
En discutant les exemples, de nouvelles questions inattendues peuvent être soulevées. Ecrivons les sur des post-its rouges.

Ensuite on continue jusqu'à ce qu'on ait répondu à toutes les questions et que le scope soit clair pour tous.
Le backlog aura maturé et des US auront pu émerger ou été divisées en plusieurs US.
Dnas le cas où des questions restent en suspens à la fin de l'atelier, au moins nous avons moins de questions non posées. C'est un bon progrès.

Dans le cas où l'attendu d'un exemple n'est pas clair, alors ce n'est ppas un exemple mais une nouvelle question.

Comment définir une bonne US ?
En se basant sur INVEST :
- I : Independant. Chaque US est indépendante des autres
- N : Negotiable. Les détails sont négociables
- V : Value. Chaque Us doit ajouter de la valeur au business
- E : Estimable. 
- S : Small. Chaque US doit avoir été suffisamment divisée pour être dévelopée suivant un cycle court (Sprint)
- T : Testable. 

En moyenne, après 5 ateliers d'example mapping, l'exercice devient fluide et efficace.
Une bonne durée pour l'atelier est d'environ 25 min après que les participants soient expérimentés.

## Fin de l'atelier

- Trop de post-its rouges : les exigences ne sont pas assez bien définies.
- Trop de post-its bleus : l'US est trop grosse et peut être subdivisée.
- Trop de post-its verts par règle : Plusieurs règles peuvent se cacher dessous.

## BDD

Cet atelier a pour but de discuter des exigences pour les rendre claires et faire apparaitre des cas non pensés auparavant.
Par la suite, l'on peut écrire des tests Gerkhin. C'est mieux de ne pas le faire pendant l'atelier pour se concentrer sur les échanges avec le business.

## Tips and tricks

- Ne pas travailelr sur trop d'US à la fois. Un atelier de 25 min sur une seule US permet de rester concentré et frais.
- Si l'équipe est géographiquement répartie, on peut représenter les post-its avec google sheets, des mind maps, ou 
 n'importe quoi tant que l'on peut les matérialiser. 

### Sources

https://cucumber.io/blog/bdd/example-mapping-introduction/

Pour essayer : https://speakerdeck.com/mattwynne/rules-vs-examples-bddx-london-2014