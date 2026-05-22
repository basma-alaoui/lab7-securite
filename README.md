Laboratoire : Analyse dynamique d’une application Android vulnérable avec MobSF et DIVA
📌 Objectifs pédagogiques
Ce laboratoire a pour but de maîtriser l’analyse dynamique (à l’exécution) d’une application Android à l’aide de MobSF (Mobile Security Framework).

À l’issue de ce TP, l’apprenant saura :

Configurer un environnement Android propre (émulateur sans Google Play)

Déployer MobSF via Docker

Réaliser une analyse statique puis dynamique d’un APK

Intercepter les logs système, le trafic réseau, et instrumenter avec Frida

Identifier des vulnérabilités comme le stockage non sécurisé, les logs sensibles, les intents exportés, etc.

📦 Prérequis techniques
Composant	Version recommandée
Android Studio	Dernière stable
Docker Desktop	Dernière stable
Git	Dernière stable
RAM	8 Go minimum
Processeur	64 bits
🧱 Architecture mise en place
Élément	Rôle
AVD (émulateur)	Environnement d’exécution Android vierge
MobSF (conteneur)	Plateforme d’analyse statique & dynamique
APK DIVA	Application cible volontairement vulnérable
Frida Server	Outil d’instrumentation dynamique
Proxy MobSF	Interception du trafic HTTPS
🚀 Déroulement pas à pas
                   
                Étape 1 : Création d’un émulateur Android sans trace Google
Ouvrir Android Studio → AVD Manager.

Cliquer sur Create Virtual Device et choisir un modèle (ex. Pixel 5).

Sélectionner une image système :

Choisir une version sans la mention « Google Play »

Exemple : Android 11 (API 30) x86_64 (ou API 29)

Télécharger l’image si nécessaire, puis valider.

Nommer l’AVD, par exemple : DIVA_MobSF_AVD.

                Étape 2 : Récupération de MobSF et démarrage de l’émulateur
bash
git clone https://github.com/MobSF/Mobile-Security-Framework-MobSF.git
cd Mobile-Security-Framework-MobSF
Lancer l’émulateur avec le script fourni :

bash
./scripts/start_avd.sh DIVA_MobSF_AVD
Vérifier la connexion :

bash
adb devices
               
               Étape 3 : Lancement du conteneur Docker MobSF
Dans un nouveau terminal (en laissant l’émulateur actif) :

bash
docker run -it --rm -p 8000:8000 --network=host -e MOBSF_ANALYZER_IDENTIFIER=emulator-5554 opensecurity/mobile-security-framework-mobsf:latest
💡 Si --network=host pose problème sur certains systèmes, utiliser l’IP locale ou host.docker.internal.

              Étape 4 : Accès à l’interface web MobSF
Navigateur : http://127.0.0.1:8000

Identifiant : mobsf

Mot de passe : mobsf

            Étape 5 : Obtention de l’APK DIVA
bash
git clone https://github.com/payatu/diva-android.git
L’APK se trouve dans le dépôt (fichier diva-beta.apk).

Étape 6 : Analyse statique initiale
Sur l’interface MobSF :

Cliquer sur Upload & Analyze

Choisir le fichier diva-beta.apk

Attendre le traitement (quelques secondes)

Un rapport statique apparaît : permissions, composants, certificats, etc.

            Étape 7 : Lancement de l’analyse dynamique
Dans la page du rapport statique, cliquer sur le bouton vert Start Dynamic Analysis.

MobSF va alors :

Installer l’APK sur l’émulateur

Déployer et lancer Frida Server automatiquement

Configurer un proxy d’interception HTTPS

Ouvrir le tableau de bord dynamique

             Étape 8 : Exploration de l’application dans l’émulateur
Depuis l’AVD, ouvrir l’application DIVA.

Dans MobSF, l’onglet Dynamic Analyzer permet de surveiller :

Logcat Stream : logs Android temps réel

Network Traffic : requêtes HTTP/HTTPS

File Monitor : accès aux fichiers de l’app

Frida Scripts : possibilité d’injecter des hooks personnalisés

        Étape 9 : Mise en évidence d’une vulnérabilité – Stockage non sécurisé (partie 1)
Dans DIVA, sélectionner Insecure Data Storage – Part 1

Saisir un login et un mot de passe (exemple : apprenti, secret123)

Appuyer sur SAVE

Dans MobSF → File Monitor :

Chercher les Shared Preferences de l’application

Observer que les identifiants sont stockés en clair dans un fichier XML

Dans Logcat Stream :

Filtrer les logs liés à l’application

Constater que les mêmes identifiants apparaissent également dans les logs

         Étape 10 : Génération du rapport dynamique
Sur la page Dynamic Analyzer, cliquer sur Generate Report pour exporter un document PDF ou JSON contenant l’ensemble des traces, captures et vulnérabilités détectées.

📊 Résultats observés
Vulnérabilité	Méthode de détection	Preuve visible
Stockage non sécurisé (SharedPref)	File Monitor	Identifiants en clair dans un XML
Journalisation excessive	Logcat Stream	Identifiants dans les logs
Identifiants codés en dur	Analyse statique (recherche dans bytecode)	Chaînes lisibles dans le code décompilé
Activités exportées	Activity Tester (MobSF)	Appel d’activités sans permission
🧰 Aide au dépannage
Problème rencontré	Solution possible
L’analyse dynamique échoue (bouton grisé)	Vérifier que adb devices reconnaît l’émulateur
Impossible de contacter host.docker.internal	Utiliser --network=host ou l’IP de l’hôte (ex. 172.17.0.1)
Émulateur trop lent	Passer à une image API 29 (Android 10) x86_64
Frida ne s’injecte pas	Redémarrer l’émulateur et relancer MobSF
📎 Références utiles
Documentation officielle MobSF

DIVA Android – Payatu

OWASP Mobile Security Testing Guide

🏁 Synthèse des compétences acquises
Ce laboratoire a permis de :

Mettre en place un environnement d’analyse complet (émulateur + MobSF Docker)

Effectuer une analyse statique puis dynamique d’un APK vulnérable

Détecter des failles classiques : stockage de données sensibles non chiffrées, traces de log, activités exportées

Utiliser Frida de manière transparente via MobSF

Générer un rapport d’audit professionnel

