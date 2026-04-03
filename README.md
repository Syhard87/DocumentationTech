# EC03 — Documentation technique CI/CD  
## UrbanHub Park — Module Stationnement

---

## 1. Contexte

Dans le cadre de l’épreuve EC03, l’objectif est de concevoir et documenter un pipeline CI/CD permettant d’automatiser la validation, la sécurisation et le déploiement d’un module applicatif.

Cette documentation correspond à la **partie 2**, qui vise à rendre la solution :

- compréhensible
- maintenable
- exploitable par une équipe technique

---

## 2. Objectifs du pipeline

Le pipeline CI/CD permet de :

- automatiser le build de l’application
- exécuter les tests automatiquement
- détecter les vulnérabilités (DevSecOps)
- déployer l’application de manière reproductible

👉 Cela s’inscrit dans une logique **DevOps / DevSecOps**

---

## 3. Architecture du pipeline

Le pipeline est structuré en 4 étapes :

```yaml
stages:
  - build
  - test
  - security
  - deploy
Logique
build → validation technique rapide
test → validation fonctionnelle
security → analyse sécurité
deploy → mise en production

👉 Approche shift-left : détecter les erreurs le plus tôt possible

4. Détail des étapes
🔹 Build
python -m py_compile app.py database.py

✔ Vérifie la syntaxe Python
✔ Évite d’aller plus loin si le code est invalide

🔹 Tests
pytest -q tests/test_api.py

✔ Validation du comportement de l’API
✔ Détection des régressions

👉 Limite : couverture partielle

🔹 Sécurité (DevSecOps)
pip-audit -r requirements.txt

✔ Détection des vulnérabilités connues
✔ Analyse des dépendances

👉 Important : sécurité intégrée dans le pipeline

⚠️ Une vulnérabilité est ignorée → doit être justifiée

🔹 Déploiement
récupération du code
build Docker
lancement via Docker Compose
vérification via curl

✔ Déploiement automatisé
✔ Reproductibilité

5. Conteneurisation (Docker)
5.1 Analyse du Dockerfile
FROM python:3.11-slim

✔ Image légère → performance améliorée

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

✔ Optimisation Python :

pas de fichiers .pyc
logs immédiats
WORKDIR /app

✔ Répertoire de travail du conteneur

COPY requirements.txt /app/
RUN pip install -r requirements.txt

✔ Installation des dépendances

RUN mkdir -p /app/data

✔ Préparation du stockage

useradd appuser
USER appuser

✔ 🔥 IMPORTANT sécurité
✔ Le conteneur ne tourne pas en root

👉 Très bon point DevSecOps

EXPOSE 5000

✔ Port utilisé par l’application

CMD ["python", "app.py"]

✔ Lancement de l’application

🧠 Analyse globale

Points forts :

image légère
utilisateur non root
structure propre

Limite :

pas de multi-stage build
pas de scan d’image
5.2 Analyse du docker-compose.yml
services:
  api:
    image: flask-api:latest

✔ Utilisation de l’image construite

ports:
  - "5001:5000"

✔ Mapping port :

5000 → conteneur
5001 → machine

👉 ⚠️ IMPORTANT :
L’accès réel se fait sur localhost:5001

env_file:
  - .env

✔ Gestion des variables d’environnement

volumes:
  - sqlite_data:/app/data

✔ Persistance des données

👉 Les données ne sont pas perdues

restart: unless-stopped

✔ Résilience :

redémarre automatiquement
🧠 Analyse globale

Points forts :

configuration simple et efficace
persistance des données
gestion environnement

Limites :

pas de séparation dev/prod
pas de scaling
pas d’orchestration avancée (Kubernetes)
6. Cohérence globale

Le pipeline + Docker permettent :

✔ automatisation complète
✔ reproductibilité
✔ isolation environnement

👉 Bonne base DevOps

7. Risques identifiés
vulnérabilité ignorée
couverture de tests limitée
incohérence possible port 5000 / 5001
déploiement direct sur main
8. Limites
pas de tests e2e
pas de monitoring
pas de rollback
pas d’analyse SonarQube
9. Recommandations
Qualité
ajouter SonarQube
augmenter couverture
Sécurité
corriger vulnérabilités
scanner image Docker
Déploiement
séparer environnements
ajouter rollback
Exploitation
monitoring
logs centralisés
10. Conclusion

Le pipeline CI/CD mis en place permet :

automatisation du cycle de livraison
validation continue
intégration DevSecOps
déploiement conteneurisé

👉 Solution cohérente, mais améliorable vers un niveau industriel
