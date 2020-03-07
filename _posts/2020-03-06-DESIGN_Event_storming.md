---
layout: post
title:  "Event storming"
categories: DESIGN
tags: DDD
author: Albéric Martel
description: Atelier d'Event storming.
---

# Event Storming

## Qu'est que c'est ?

L'event storming est un atelier qui permet de comprendre rapidement le problem domain en rassemblant les équipes de développement
et du business. Les développeurs ont les questions et les experts métier apportent les réponses.
Il permet de briser les limites des silos en mettant tout le monde ensemble pour construire une histoire cohérente sur l'ensemble d'un système, d'avoir une timeline partagée.

Cet atelier est très utile pour déterminer un Ubiquitous language, ainsi que les interactions entre les différentes parties du système.
Il permet de révéler les sous domaines ainsi que le core domain du problem domain.

## L'atelier

### Déroulement

J'ai découvert l'event storming lors d'un meetup conjoint entre les software crafters de Lyon le CARA Lyon.
 
Concrètement le déroulement est le suivant :

On dispose une bande de papier, le canvas, sur le mur, suffisament grande pour permettre de modéliser le système qui nous intéresse. Il représente une time line.

- On commence par poser des post-it représentant des **évènements** du domaine et utiles au business. Un évènement est décrit par un verbe au passé. On les dispose suivant une timeline.
Dans un bounded context, les évènements sont consistants avec le langage du domaine. En sortie du bounded context, il doit être traduit pour refléter son sens pour le business. 
Entre les bounded contexts ils peuvent être plus généraux et sont plus indépendants des implémentations locales.
- Puis ce qui a déclenché ces évènements, des **acteurs**, **systèmes externes** qui ont effectué des **commandes**.
- Ces **commandes** ont pu être déclenchées suite à une information récupérée par exemple du **Read Model** (ou Query Model, cf CQRS).
- Après le déclenchement d'un évènement, on décide quoi en faire, on a donc une **policy** qui définira cela, et déclenchera une commande. Les policies sont à explorer. Elles peuvent 
être implicites (sans accord explicite), explicites (définies mais qui peuvent ne pas être suivies), automatisées.
C'est à ce moment qu'on introduit la notion d'aggrégat. Un aggrégat est une portion de système qui reçoit des évènements et en fonction de son état émet de nouvelles commandes.
Ne pas s'occuper du naming à ce moment là pour éviter de focus sur les données.
Entourer les post-its pour représenter les aggrégats.

![L'image qui explique tout]({{ site.baseurl }}/assets/images/posts/Event_storming_brandolini_everything.png)

Donc au fur et à mesure on commence à s'apercevoir que les évènements déclenchés font émerger des structures, des acteurs et systèmes apparaissent, des discussions se créent. 
De nouvelles opportunités apparaissent, des inconsistences se révèlent.

Au final on obtient une visibilité sur la globalité du process, une connaissance partagée entre les silos et un consensus sur le core problem.
On explore les frontières (les systèmes externes et les acteurs), on obtient les bottlenecks et le core domain.

Ensuite, les gens votent pour ce qu'ils pensent être le plus important à traiter. Le but en travaillant autour des bottlenecks est de résoudre les blocages les plus importants rapidement.
On peut expérimenter, par exemple avec le Model Exploration Whirlpool ou autre.

L'atelier peut être fait sur un scope réduit, une epic ou un set de features avec en entrée des préconditions, ce qu'on a ou veut en sortie et au milieu un espace pour le flux à découvrir
et modéliser.

Dans tous les cas, il n'y a pas de surdesign, on explore le coeur du problème. Les définitions que l'on a donné aux choses seront progressivement réécrites.
Il faut s'attendre à jeter beaucoup mais cela assainit le problème.

### Découverte de Read Models

Un Read Model n'est pas qu'un paquet de données. Il représente plutôt les données fournies à un utilisateur pour prendre des décisions.
La question à se poser est quelles sont les données/sources de données dont j'ai besoin pour résoudre un problème.
Cet atelier est tout indiqué pour y répondre.

### Sources

https://ziobrando.blogspot.com/2013/11/introducing-event-storming.html#.U3sb71h_u5I

https://www.youtube.com/watch?v=1i6QYvYhlYQ

https://www.eventstorming.com/