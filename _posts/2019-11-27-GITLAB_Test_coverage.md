---
layout: post
title:  "Couverture de test dans GITLAB"
categories: CI/CD
tags: Gitlab
author: Albéric Martel
description: Intégrer la couverture de test dans Gitlab.
---

# Couverture de test et intégration dans Gitlab

Sur un projet basé sur :
- Java 1.8
- Maven 3.6.0
- Gitlab 12.2.5

Notre but sera ici d'afficher un badge affichant la couverture de test du projet.

## POM

Si le projet est divisé en plusieurs modules, les résultats de la couverture de test sont rendus disponibles dans le répertoire target de chaque module.

Pour obtenir la couverture de test globale, il est nécessaire d'aggréger les résultats. Nous allons créer un module spécifique chargé de cette tâche.Let's name it module-report.
Le badge sera affiché avec sa valeur seulement dans le cas d'une pipeline en succès. Si un job manuel est proposé, alors la pipeline n'est pas complète, et le badge sera au status "Unknown".

### POM principal

    <properties>
        ...
        <jacoco-maven-plugin.version>0.8.5</jacoco-maven-plugin.version>
        <jacoco.reportPath>${project.basedir}/../target/jacoco.exec</jacoco.reportPath>
    </properties>
    
    <dependencies>
        ...
        <dependency>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>${jacoco-maven-plugin.version}</version>
        </dependency>
    </dependencies>
    
    <plugins>
        ...
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <executions>
                <execution>
                    <id>prepare-agent</id>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>

### POM du module en charge de l'aggrégation

Ajouter les autres modules en dépendances et configurer jacoco-maven-plugin de la façon suivante :

    <build>
        <plugins>
            <plugin>
                <groupId>org.jacoco</groupId>
                <artifactId>jacoco-maven-plugin</artifactId>
                <version>${jacoco-maven-plugin.version}</version>
                <executions>
                    <execution>
                        <id>report-aggregate</id>
                        <phase>verify</phase>
                        <goals>
                            <goal>report-aggregate</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>

### .gitlab-ci.yml

Afficher le contenu du fichier résultant de l'éxécution de jacoco _index.html_ dans les logs de la pipelines. Gitlab parsera le log afin d'obtenir le pourcentage de couverture total.

    script:
        - mvn clean verify
        - cat alertes-report/target/site/jacoco-aggregate/index.html
        
### Configuration Gitlab

Pour affiche le badge :

##### Dans Settings > General > Badges
 
Ajouter un nouveau badge

Dans "Link and Badge image URL", renseigner le champ :

    https://[GITLAB_URL]/[REPOSITORY]/badges/%{default_branch}/coverage.svg
    
Remplacer [GITLAB_URL] et [REPOSITORY] par les valeurs réelles.

___

##### Dans Settings > CI/CD > General pipelines

Dans la partie "Test coverage parsing" :

    Total.*?([0-9]{1,3})%
    
### Sources

- https://gitlab.tennaxia.org/help/user/project/badges
- https://gitlab.tennaxia.org/help/user/project/pipelines/settings.md#test-coverage-report-badge
- https://github.com/jacoco/jacoco/pull/488#issuecomment-316602731
