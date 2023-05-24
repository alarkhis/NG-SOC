# NG-SOC
**Le deploiement du Next Generation Security operation Center (NG-SOC) Open source:**


**I. Presentation du Next Generation Security operation Center (NG-SOC) Open source:**
Le Security Operations Center (SOC) est une plateforme où les systèmes d'information de l'entreprise sont surveillés, évalués et défendus contre les failles de sécurité. Il a pour objectif d'identifier et de réduire de manière proactive les risques de sécurité, en prenant des mesures préventives pour protéger les actifs numériques de l'entreprise.

L'ajout du terme "NG" (Next Generation) au SOC (Security Operations Center) fait référence à une évolution et à une amélioration significatives du concept traditionnel de SOC. Il indique que le NG-SOC adopte des technologies et des approches avancées pour faire face aux nouvelles menaces et défis en matière de sécurité.

**II.Les outils open source utilisees:**

Voici quelques exemples d'outils open source qui peuvent être utilisés pour déployer un NG-SOC (Next-Generation Security Operations Center) :

SIEM: Un système de gestion des informations et des événements de sécurité qui collecte, agrège, indexe et analyse les données de sécurité pour détecter les intrusions, identifier les menaces et repérer d'éventuelles anomalies de comportement qui pourraient se produire.

EDR: l'outil axé sur la détection et la réponse aux menaces au niveau des terminaux.

IDS/IPS:La détection d'intrusion. Il analyse le trafic réseau en temps réel, identifie les signatures de malwares connus et les comportements suspects, et génère des alertes en cas de détection d'activités malveillantes.

Parefeu:Il agit comme une barrière de protection entre le réseau interne et les réseaux externes non fiables, tels qu'Internet. 

OSINT:L'OSINT, ou renseignement à source ouverte, est devenu essentiel dans le domaine de la sécurité et du renseignement. 

SIRP:Plateforme d’intervention en cas d’incident de sécurité permettant de faciliter le traitement des incidents de sécurité de la création à la clôture.

SOAR:Les outils open source tels que SOAR (Security Orchestration, Automation, and Response) permettent d'automatiser les tâches de sécurité, d'orchestrer les processus de réponse aux incidents et de rationaliser les opérations de sécurité.

 En plus des outils mentionnés précédemment, il existe d'autres outils et technologies qui peupeuvent être utilisés pour réaliser un NG-SOC (Next-Generation Security Operations Center).
 
**III.L'architecture**

L'architecture ci-dessous represente les outils utilisees pour deploier NG-SOC
On a integre Snort avec parefeu pour renforcer la sécurité de notre réseau en détectant et en bloquant rapidement les menaces potentielles.

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/361d9974-9855-4266-9d0a-bd877fc0fb58)

**IV. Test**

En raison du manque de ressources, il est difficile de développer pleinement le potentiel de ce projet. Donc c'est à vous de devlopper.

**IV.1. La tentative de Connexion SSH:**

Comme je l'ai déjà mentionné, en raison du manque de ressources, je vais utiliser la tentative de connexion SSH comme exemple de test.

La tentative de connexion SSH consiste en une succession d'essais visant à découvrir un couple utilisateur/mot de passe valide, dans le but de prendre le contrôle de la machine.
On a accede au parefeu (Pfsense)  à travers le serveur Wazuh.

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/bf572eb6-16ad-46bc-843d-044900a8fe8b)

On observe ci-dessous que Wazuh a détecté une tentative d'accès au pare-feu par SSH.

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/05e6ea5a-a21a-4d6d-afde-994c346f177a)

On observe ci-dessous que que Snort, qui est intégré au pare-feu, a détecté une tentative d'accès au pare-feu par SSH.

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/f921877a-9708-4850-8cbf-28058d1e8434)


On peut conclure que le serveur Wazuh a réussi à détecter la tentative d'accès au pare-feu Pfsense via le service SSH. Cependant, il n'a pas été en mesure de fournir des détails supplémentaires sur les incidents détectés, ce qui signifie qu'il manque des preuves permettant aux administrateurs d'intervenir en cas d'alertes réelles.

En ajoutant MISP et Cortex à la plateforme TheHive, on renforce la capacité à traiter les incidents de sécurité de manière plus proactive et à prendre des mesures plus rapides et précises. Grâce à une meilleure collaboration et à des fonctionnalités d'analyse avancée, on améliore la visibilité, la précision et l'efficacité de la réponse aux incidents, réduisant ainsi le temps de résolution global et minimisant les impacts sur l'environnement informatique.

La figure ci-dessous présente en détail les règles de sécurité collectées par TheHive:

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/d2ae0be8-8c4b-43b0-9a8a-82adb855098b)

On peut conclure que le serveur Wazuh a réussi à détecter la tentative d'accès par le service SSH et a envoyé les journaux au serveur TheHive. Par la suite, TheHive a procédé au traitement et à l'identification des incidents en les classant avec le niveau de risque moyen « M » , représenté par la couleur jaune. 

Cependant, dès le moment de la détection, l'identification du niveau de risque et la réponse à cet incident (telles que les analyses et la détermination des étapes nécessaires pour résoudre l'incident) nécessitent beaucoup de temps, ce qui entraîne des retards.

Afin de réduire ces délais, il est nécessaire d'ajouter une solution d'orchestration, d'automatisation et de réponse aux incidents de sécurité. Cette solution permettra de mettre en place des processus automatisés pour l'analyse, la classification et la réponse aux incidents, accélérant ainsi la résolution des problèmes de sécurité.

**En raison du manque de ressources, je ne peux pas expliquer cette partie à l'aide de photos, mais je vais vous fournir les étapes pour réaliser cette partie :**

Dans ce contexte, vous pouvez configurer un flux de travail Shuffle pour gérer les actions de prise de décision et de réponse automatiques à un accès SSH non autorisé. Voici comment cela pourrait fonctionner :

1. Configuration des règles de détection : Vous pouvez configurer les règles de détection d'accès SSH non autorisé dans Wazuh pour générer des alertes appropriées.

2. Intégration de TheHive, Cortex, MISP et Wazuh avec Shuffle : Vous pouvez intégrer ces outils dans votre flux de travail Shuffle en utilisant les connecteurs et les actions disponibles.

3. Réception de l'alerte : Lorsqu'une alerte est générée par Wazuh, elle peut être reçue automatiquement par TheHive via un connecteur.

3.1. Intégration d'ElastAlert : ElastAlert est un système d'alerte basé sur les journaux (log) qui peut être intégré avec Elasticsearch, ce qui permet de rechercher et d'analyser les journaux de manière puissante.

3.2. Configuration d'ElastAlert : Configurez ElastAlert pour surveiller les journaux d'accès SSH dans Elasticsearch. Vous pouvez définir des règles de détection personnalisées pour identifier les accès non autorisés en utilisant des filtres, des agrégations et des expressions de requête.

3.3. Déclenchement d'alertes avec ElastAlert : Lorsque ElastAlert détecte un accès SSH non autorisé en analysant les journaux, il peut déclencher une alerte. Cette alerte peut être transmise à TheHive via une intégration ou un connecteur dédié. 

4. :Prise de décision manuellement:

4.1. Analyse avec Cortex manuellement: Cortex peut être configuré pour analyser automatiquement l'alerte reçue de Wazuh. Il peut effectuer des recherches sur MISP pour obtenir des informations supplémentaires sur l'adresse IP ou le comportement malveillant potentiel.

4.2. Analyse avec Cortex automatisée : Sur la base des résultats de l'analyse de Cortex, vous pouvez configurer des règles dans Shuffle pour prendre des décisions automatisées. Par exemple, si l'analyse détecte une menace grave( elastalert) , Shuffle peut déclencher une action pour bloquer l'adresse IP source au niveau du pare-feu.

5. Actions supplémentaires : Shuffle peut également être configuré pour prendre d'autres actions en fonction des résultats de l'analyse. Par exemple, il peut ajouter l'adresse IP à une liste de surveillance pour une investigation plus approfondie ou lancer une recherche supplémentaire sur MISP pour obtenir plus d'informations.

En automatisant ces actions avec Shuffle, vous pouvez accélérer le processus de prise de décision et de réponse aux accès SSH non autorisés, en réduisant le temps de réaction et en permettant une réponse plus cohérente et efficace aux menaces potentielles.

**IV.2. L’hameçonnage par mail:**

L'hameçonnage reste le vecteur d'attaque le plus courant pour les brèches réussies. Cependant, enquêter sur les courriels suspects implique des tâches fastidieuses telles que l'analyse des en-têtes et des pièces jointes, la vérification des adresses URL ou le suivi des demandes suspectes de renseignements sensibles.
 
J'ai décidé d'utiliser l'hameçonnage par e-mail comme deuxième test pour expliquer l'automatisation et l'orchestration avec SOAR (Shuffle) et les gains de temps.

Cidessous on exprime l'architecture de flux que l'email a traversée pour être testé
![image](https://github.com/alarkhis/NG-SOC/assets/58915338/9dfdb5bb-968a-49e0-b6c9-96ff5ae14d7b)

Cet email contient un fichier(filename) et un lien(uri):

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/5fd7ce27-988f-4a9b-b026-e261cd787ce4)

La figure ci-dessous présente une description de l'e-mail et des observables détectés:

*L'expediteur: .com

*Destinataire: .fr

*Les adresses ip: (local et publiques)

*Les domaines (DNS) 

*Fichiers: 3 taches

*URI: thehive


![image](https://github.com/alarkhis/NG-SOC/assets/58915338/546e88f2-2a19-481e-8285-80f0c85ce1e7)

voici l'interface de Cortex qui répertorie les exécutions du test :
 
![image](https://github.com/alarkhis/NG-SOC/assets/58915338/bccc4499-ffd7-4114-b10e-8d6ec339c07b)

 
![image](https://github.com/alarkhis/NG-SOC/assets/58915338/7f1b5983-0abf-4de6-8c3a-2671e59af866)

La figure ci-dessous représente l'interface de Cortex qui affiche la détection d'une adresse IP malveillante et la vérification via Abuse IPDB:

![image](https://github.com/alarkhis/NG-SOC/assets/58915338/cbf69e07-8f80-4078-a27b-33f2dd9d75de)

