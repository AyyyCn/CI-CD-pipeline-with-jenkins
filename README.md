# 📘 Documentation du Pipeline CI/CD avec Jenkins, Maven et Docker

## 🎯 Objectif du TP
Mettre en place un pipeline CI/CD avec Jenkins pour une application Java containerisée avec Docker. L’objectif est d’automatiser les étapes suivantes :

1. Clonage du dépôt GitHub contenant une application Spring Boot
2. Compilation et construction avec Maven
3. Exécution des tests automatisés avec JUnit
4. Génération d’un rapport de couverture avec JaCoCo
5. Construction d’une image Docker
6. Publication automatique sur Docker Hub

---

## 🛠️ Environnement utilisé
- **Jenkins** local (Windows)
- **Java 11** / **Maven Wrapper** (`mvnw.cmd`)
- **Docker Desktop** installé et actif
- **Docker Hub** : `ayyycn`
- **GitHub Repository** : [CI-CD-pipeline-with-jenkins](https://github.com/AyyyCn/CI-CD-pipeline-with-jenkins)

---

## 📁 Structure du projet

- `src/main/java/...` : Application Spring Boot (1 endpoint `/api/v1/welcome`)
- `Dockerfile` : Présent à la racine
- `Jenkinsfile` : Pipeline Jenkins declaratif
- `pom.xml` : Contient dépendances Spring Boot + JaCoCo

---

## ⚙️ Contenu du `Jenkinsfile`

```groovy
pipeline {
    agent any

    environment {
        IMAGE_NAME = "ayyycn/springboot-docker"
        VERSION = "latest"
    }

    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main', url: 'https://github.com/AyyyCn/CI-CD-pipeline-with-jenkins.git'
            }
        }

        stage('Build Maven') {
            steps {
                bat 'mvnw.cmd verify'
            }
        }

        stage('Build de l’image Docker') {
            steps {
                script {
                    dockerImage = docker.build("${IMAGE_NAME}:${VERSION}")
                }
            }
        }

        stage('Push sur Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    script {
                        docker.withRegistry("https://index.docker.io/v1/", "dockerhub") {
                            dockerImage.push()
                        }
                    }
                }
            }
        }
    }
}
```

---

## 🧪 Qualité des tests et couverture

L'application intègre des tests unitaires avec JUnit 5 pour valider l’endpoint REST `/api/v1/welcome`.

- La commande `mvn verify` exécute automatiquement les tests dans le pipeline.
- La couverture de code est mesurée avec **JaCoCo 0.8.8** et générée dans `target/site/jacoco/index.html`

### 📊 Rapport de couverture :
| Package                                 | Couverture |
|----------------------------------------|------------|
| `com.mycompany.applicationone.controller` | ✅ **100 %** |
| `com.mycompany.applicationone`             | ❌ **37 %** |
| **Total projet**                        | 🟡 **61 %** |

> 💬 L’ensemble du code métier est couvert à 100 %. La classe `main()` (non testable directement) diminue artificiellement la couverture globale.

---

## 📦 Test final de l’image Docker

```bash
docker pull ayyycn/springboot-docker:latest
docker run -p 8080:8080 ayyycn/springboot-docker:latest
```

### 🔍 Accès API REST :
```
GET http://localhost:8080/api/v1/welcome
```
Réponse :
```
Welcome to the world of Azure Containers!
```

---

## ✅ Résultat global du TP

✔️ Déploiement automatisé validé via Jenkins  
✔️ Pipeline CI/CD complet fonctionnel  
✔️ Tests automatisés + couverture intégrée  
✔️ Image Docker disponible sur Docker Hub  
✔️ Application fonctionnelle et testée en local

