---
layout: post
title:  "Afficher les résultats de  Sonar dans une MR GITLAB"
categories: CI/CD
tags: Gitlab
author: Albéric Martel
description: Afficher les résultats de  Sonar dans une MR GITLAB.
---

# Ajouter Sonar sur un projet Java

Sur un projet basé sur :
- Java 1.8
- Maven 3.6.0
- Gitlab 12.2.5 et un runner Gitlab avec un docker executor et un projet forké
- Sonarqube 7.1 et son plugin gitlab

### Configurer un build de référence sonar

Configurer un build de référence Sonar permettra d'avoir une base de comparaison lorsqu'on passera Sonar sur les builds de MR
Ce job mettra à jour le projet de référence sur la branche master.

- Dans sonar, Administration > Security > Users, ajouter un token à votre utilisateur. Le nommer par exemple gitlab-ci.
- Dans gitlab, Settings > CI/CD > Variables ou au niveau du groupe, configurer les variables suivantes :
    - SONAR_HOST_URL : type Variable
    - SONAR_AUTH_TOKEN : type Variable. Utiliser le token précédemment généré dans Sonar.
- Dans .gitlab-ci.yml :


    stages:
      - sonar
    
    maven-scheduled-sonar:
      image: maven:3.3-jdk-8-alpine
      stage: sonar
      script:
        - mvn clean verify sonar:sonar -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.branch=master
      only:
        - master
        - schedules

Pour le programmer une fois par jour à 22h, configurer une pipeline programmée dans CI/CD > Schedules avec les valeurs suivantes :

- Interval pattern : Choisir Custom et renseigner "0 22 * * *"
- Target branch : master

Le worker sidekiq utilisé par gitlab pour gérer le scheduling est configuré par défaut pour s'exécuter chaque heure à la 19è minute.
Pour changer ce comportement, modifier gitlab.rb comme suit (ici pour l'exécuter toutes les 10 minutes) :

    pipeline_schedule_worker_cron: '*/10 * * * *'

Après exécutino de ce job, aller dans sonar et dans la section Gitlab de l'administration du projet, renseigner le project id avec la valeur correspondant
à la variable Gitlab prédéfinie **CI_MERGE_REQUEST_SOURCE_PROJECT_PATH**.

Par exemple : Pour http://gitlaburl/namespace/project **CI_MERGE_REQUEST_SOURCE_PROJECT_PATH** sera **namespace/project**.

### Configurer le build SONAR de la MR pour se comparer à la référence

Pour commencer, dans notre MR, nous voulons builder sur un merge result. Ce qui veut dire que la pipeline fera un checkout de la branche destination et mergera avec la branche 
candidate au merge dans la MR.

Pour ce job, j'ai fait face à plusieurs problèmes :
- Exécuter le build sur un merge result n'est disponible qu'à partir de la version premium de gitlab : https://docs.gitlab.com/ee/ci/merge_request_pipelines/pipelines_for_merged_results/index.html
- Pour des raisons de sécurité, Gitlab ne passe pas les varialbes du repository principal à un repo forké. A suivre : https://gitlab.com/gitlab-org/gitlab/issues/11934
 
Donc, pour builder sur un merge result, j'ai du manuellement merger les branches source et destination.
Pour pouvoir faire ça, j'ai besoin d'ajouter à mon profil Gitlab (ou un utilisateur dédié aux runners) la clé SSH publique de la machine hébergeant le runner et créer
une variable sur le repository forké contenant la clé privée : GITLABRUNNER_PRIVATE_KEY.

- Dans gitlab, sur le repository forké, Settings > CI/CD > Variables, configurer les variables suivantes :
    - GITLABRUNNER_PRIVATE_KEY : type File
    - SONAR_HOST_URL : type Variable
    - SONAR_AUTH_TOKEN : type Variable
- Dans .gitlab-ci.yml :


    .merge-result: &merge-result
      variables:
        MY_REPO: git@$CI_SERVER_HOST/$CI_MERGE_REQUEST_PROJECT_PATH.git
      before_script:
        - "which ssh-agent || ( apk update && apk upgrade && apk add git && apk add openssh-client)"
        - chmod 600 $GITLABRUNNER_PRIVATE_KEY
        - git config core.sshCommand "ssh -o StrictHostKeyChecking=no -i $GITLABRUNNER_PRIVATE_KEY -F /dev/null"
        - grep -q $MY_REPO .git/config && git remote rm my_repo
        - git remote add my_repo ssh://$MY_REPO -f
        - git merge my_repo/$CI_MERGE_REQUEST_TARGET_BRANCH_NAME
    
    maven-build:
      image: maven:3.3-jdk-8-alpine
      stage: build
      <<: *merge-result
      script:
        - mvn clean verify sonar:sonar
          --batch-mode
          -Dsonar.host.url=$SONAR_HOST_URL
          -Dsonar.login=$SONAR_AUTH_TOKEN
          -Dsonar.branch=master
          -Dsonar.analysis.mode=preview                               # deprecated mais permet de ne pas mettre à jour le projet de référence. Remplacé par la fonctionnalité "branch" dans l'édition developer de Sonar
          -Dsonar.issuesReport.console.enable=true
          -Dsonar.gitlab.commit_sha=$CI_COMMIT_SHA                    # commit à analyser
          -Dsonar.gitlab.ref_name=$CI_COMMIT_REF_NAME
          -Dsonar.gitlab.disable_global_comment=true
          -Dsonar.gitlab.only_issue_from_commit_file=true             # seul le contenu du commit spécifié sera analysé
          -Dsonar.gitlab.project_id=$CI_MERGE_REQUEST_PROJECT_ID
      only:
        - merge_requests
      except:
        - schedules

Sonar commentera les changements de la base de code dans la MR avec les issues remontées.

### Sources

- https://docs.gitlab.com/ee/ci/merge_request_pipelines/
- https://docs.gitlab.com/ee/ci/merge_request_pipelines/pipelines_for_merged_results/index.html
- https://gitlab.com/gitlab-org/gitlab/issues/11934
- https://docs.gitlab.com/ee/user/project/pipelines/schedules.html#advanced-configuration
- https://github.com/gabrie-allaigre/sonar-gitlab-plugin