# Politique de sécurité — MR LOCK Website

> Politique de sécurité du dépôt public `mr-lock-website`. Ce document définit les exigences de sécurité attendues, les contrôles à appliquer avant déploiement et la procédure de signalement d'une vulnérabilité.

## 1. Périmètre et objectif

Le projet peut évoluer vers un écosystème comprenant :

- site vitrine et personal branding ;
- produits digitaux ;
- produits et services IA ;
- paiements en ligne ;
- formulaires et prises de contact ;
- intégrations tierces ;
- éventuellement des espaces authentifiés.

La sécurité doit être intégrée à la conception, au développement, au déploiement et à la maintenance.

Cette politique décrit les contrôles attendus ; elle ne prétend pas prouver que chaque contrôle est déjà implémenté. L'état réel du dépôt et de l'application doit toujours être vérifié.

## 2. Principe fondamental : ne jamais faire confiance au client

**Tout ce qui arrive du navigateur, d'un utilisateur ou d'un service externe doit être considéré comme non fiable jusqu'à validation appropriée.**

L'interface utilisateur ne constitue jamais une preuve d'autorisation, de paiement, d'identité ou de propriété.

Toute règle de sécurité importante doit être appliquée sur une frontière de confiance appropriée, généralement côté serveur.

## 3. Secrets et variables d'environnement

Ne jamais publier dans Git :

- clés API ;
- tokens d'accès ;
- secrets FedaPay ;
- secrets de webhook ;
- clés privées ;
- identifiants de base de données ;
- credentials OAuth ;
- secrets Vercel ou autres plateformes ;
- mots de passe ;
- fichiers `.env` contenant des valeurs réelles.

Utiliser des variables d'environnement et fournir uniquement les noms nécessaires dans `.env.example`.

### Secret exposé

Si un secret est accidentellement publié :

1. considérer le secret comme compromis immédiatement ;
2. le révoquer ou le faire tourner chez le fournisseur ;
3. supprimer la valeur du code courant ;
4. rechercher son exposition dans l'historique Git ;
5. vérifier les usages/journaux lorsque le fournisseur le permet ;
6. considérer l'incident comme clos uniquement après rotation et vérification appropriées.

Supprimer un secret dans un nouveau commit ne révoque pas le secret déjà exposé dans l'historique.

## 4. Paiements

Pour FedaPay ou tout autre prestataire :

- les clés secrètes restent côté serveur ;
- le produit, le prix, la devise et l'état de la commande sont déterminés ou validés côté serveur ;
- le statut de transaction est vérifié côté serveur ;
- les webhooks sont authentifiés selon la documentation officielle actuelle du fournisseur ;
- les traitements de paiement et de livraison sont idempotents ;
- une URL de retour navigateur ne suffit jamais à débloquer un produit ;
- une notification client ne constitue jamais une preuve de paiement ;
- les événements répétés, retardés ou reçus dans un ordre inattendu sont traités sans double livraison.

## 5. Authentification et autorisation

Si une authentification est ajoutée :

- appliquer le principe du moindre privilège ;
- vérifier l'autorisation côté serveur pour chaque opération sensible ;
- ne jamais considérer une route masquée comme protégée ;
- protéger les opérations administratives contre les accès directs ;
- éviter l'énumération inutile de comptes ou ressources ;
- gérer correctement expiration, rotation et révocation des sessions/tokens ;
- limiter les tentatives lorsque le contexte le justifie ;
- ne jamais stocker de mots de passe en clair.

## 6. Validation des entrées

Valider côté serveur toutes les données provenant de :

- formulaires ;
- query strings ;
- paramètres de route ;
- APIs publiques ;
- webhooks ;
- uploads ;
- intégrations tierces.

Les validations doivent couvrir, lorsque pertinent :

- type ;
- structure ;
- format ;
- longueur/taille ;
- valeurs autorisées ;
- limites métier ;
- encodage.

Rejeter les entrées invalides sans divulguer de détails internes.

## 7. XSS et contenu utilisateur

Tout contenu fourni par un utilisateur doit être échappé, nettoyé ou rendu via un mécanisme sûr adapté au contexte.

Éviter l'injection HTML arbitraire et les mécanismes d'exécution dynamique non nécessaires.

Ne jamais injecter directement une entrée utilisateur non fiable dans :

- HTML ;
- JavaScript ;
- attributs sensibles ;
- requêtes SQL ;
- commandes système ;
- templates interprétés.

## 8. Injection et accès aux données

Utiliser des requêtes paramétrées, des ORM sûrs ou des APIs structurées adaptées.

Ne jamais construire une requête sensible par concaténation directe d'une entrée utilisateur non validée.

Appliquer également le principe du moindre privilège aux comptes et services ayant accès aux données.

Les APIs ne doivent retourner que les données nécessaires à l'opération demandée.

## 9. CSRF et actions sensibles

Les opérations modifiant des données ou déclenchant une action importante doivent être protégées contre les requêtes non autorisées lorsque le mécanisme d'authentification utilisé le nécessite.

Pour toute action sensible, vérifier :

- identité ;
- autorisation ;
- validité de la requête ;
- état courant de la ressource ;
- éventuelle protection contre le rejeu.

## 10. Webhooks

Tout webhook est une entrée externe non fiable.

Avant traitement :

1. vérifier son authenticité/signature selon le fournisseur ;
2. valider sa structure et ses types ;
3. vérifier les identifiants attendus ;
4. rendre le traitement idempotent ;
5. empêcher qu'un payload client falsifie l'état métier ;
6. gérer les doublons et les événements hors ordre ;
7. journaliser suffisamment pour diagnostiquer sans enregistrer de secrets.

## 11. Uploads

Si le site accepte des fichiers :

- limiter la taille ;
- limiter les types autorisés ;
- valider le contenu, pas uniquement l'extension ;
- générer des noms de fichiers sûrs ;
- éviter l'exécution directe des fichiers uploadés ;
- utiliser un stockage et des permissions adaptés ;
- contrôler les téléchargements et URLs d'accès ;
- envisager un scan antivirus/malware selon le risque.

## 12. Headers et navigateur

Lorsque l'architecture le permet, appliquer des politiques HTTP adaptées, notamment :

- Content-Security-Policy ;
- Strict-Transport-Security lorsque HTTPS est garanti ;
- X-Content-Type-Options ;
- Referrer-Policy ;
- permissions/politiques navigateur adaptées aux besoins réels.

Tester les headers avec les scripts, fonts, images, analytics et services réellement utilisés. Ne pas déployer une CSP aveuglément.

## 13. CORS, cookies et sessions

Si une API ou une authentification est ajoutée :

- limiter CORS aux origines nécessaires ;
- éviter `*` lorsque des credentials sont impliqués ;
- utiliser des cookies `Secure` lorsque HTTPS est disponible ;
- utiliser `HttpOnly` pour les cookies qui ne doivent pas être accessibles au JavaScript ;
- choisir `SameSite` selon le besoin réel ;
- ne pas stocker inutilement des secrets persistants dans `localStorage`.

## 14. Rate limiting et abus

Les endpoints exposés publiquement doivent être évalués contre :

- spam de formulaires ;
- brute force ;
- création massive de transactions ;
- appels API excessifs ;
- abus de ressources coûteuses ;
- répétition de webhooks ou actions métier.

Ajouter du rate limiting, des quotas ou des contrôles anti-abus lorsque le risque le justifie.

## 15. Dépendances et chaîne d'approvisionnement

Avant d'ajouter une dépendance :

- vérifier qu'elle est réellement nécessaire ;
- préférer une version maintenue ;
- vérifier les vulnérabilités connues ;
- évaluer les dépendances transitives lorsque le risque le justifie ;
- vérifier la licence ;
- conserver les lockfiles ;
- éviter les scripts distants arbitraires et les actions CI non maîtrisées.

Les dépendances doivent être mises à jour selon un processus contrôlé et les changements majeurs doivent être testés.

## 16. Protection des données

Ne collecter que les données nécessaires à une finalité définie.

Éviter de stocker inutilement :

- mots de passe en clair ;
- données personnelles non nécessaires ;
- informations de paiement sensibles ;
- tokens ou secrets dans les logs.

Les données sensibles ne doivent pas être placées dans les URLs lorsque cela peut être évité.

## 17. Logs, erreurs et observabilité

Les logs doivent permettre le diagnostic sans devenir une fuite d'informations.

Ne jamais journaliser en clair :

- mots de passe ;
- tokens ;
- clés API ;
- secrets de webhook ;
- données de paiement sensibles.

Les erreurs publiques ne doivent pas révéler :

- stack traces internes ;
- chemins sensibles ;
- credentials ;
- secrets ;
- détails inutiles sur l'infrastructure.

Conserver les détails de diagnostic côté serveur lorsque nécessaire.

## 18. Sécurité GitHub et CI/CD

Le dépôt étant public :

- aucun secret ne doit être commit ;
- les fichiers de configuration locale doivent être ignorés ;
- les workflows GitHub Actions doivent utiliser des permissions minimales ;
- les secrets CI doivent être stockés dans le mécanisme de secrets approprié ;
- les actions tierces doivent être choisies et versionnées avec prudence ;
- les dépendances doivent être surveillées ;
- les changements sensibles doivent être revus avant déploiement.

Lorsque le projet sera suffisamment mature, activer des protections de branche et contrôles automatisés adaptés au niveau de risque.

## 19. Checklist avant déploiement

### Code et données

- [ ] Aucun secret ou credential dans le dépôt.
- [ ] Aucun secret dans les logs.
- [ ] Entrées externes validées aux frontières.
- [ ] Sorties sensibles limitées au strict nécessaire.
- [ ] Autorisations vérifiées côté serveur.
- [ ] Erreurs contrôlées.

### Paiements et webhooks

- [ ] Prix, devise et produit validés côté serveur.
- [ ] Paiement vérifié côté serveur.
- [ ] Webhook authentifié.
- [ ] Webhook idempotent.
- [ ] Double livraison empêchée.
- [ ] Retour navigateur non utilisé comme preuve de paiement.

### Infrastructure

- [ ] HTTPS actif.
- [ ] Headers de sécurité adaptés.
- [ ] CORS restreint si applicable.
- [ ] Rate limiting/anti-abus évalué.
- [ ] Dépendances et lockfile vérifiés.
- [ ] CI/CD sans privilèges inutiles.

### Validation

- [ ] Tests disponibles exécutés.
- [ ] Lint exécuté si configuré.
- [ ] Type-check exécuté si configuré.
- [ ] Build de production exécuté si applicable.
- [ ] Revue manuelle des changements sensibles effectuée.

## 20. Procédure de réponse à incident

En cas d'incident de sécurité :

1. Contenir l'incident sans détruire les éléments utiles au diagnostic.
2. Révoquer/faire tourner les secrets compromis.
3. Identifier les systèmes et données potentiellement concernés.
4. Examiner les logs disponibles.
5. Corriger la cause racine.
6. Déployer le correctif de manière contrôlée.
7. Vérifier que le vecteur d'attaque est fermé.
8. Documenter l'incident et les actions correctives.
9. Informer les parties concernées lorsque cela est requis.

Ne pas poursuivre une exploitation après démonstration d'une vulnérabilité.

## 21. Signaler une vulnérabilité

Ne publiez pas les détails d'une vulnérabilité non corrigée dans une issue publique.

Pour un problème de sécurité, contactez directement le mainteneur via un canal privé associé au compte GitHub `iamrlock`.

Fournissez autant que possible :

- une description claire ;
- les étapes de reproduction ;
- l'impact potentiel ;
- les préconditions ;
- une preuve de concept raisonnable et non destructive ;
- une suggestion de correction si disponible.

N'accédez pas à des données qui ne vous appartiennent pas, ne dégradez pas le service et ne supprimez aucune donnée.

## 22. Limites

Cette politique décrit les pratiques attendues. Elle ne constitue pas une garantie de sécurité absolue et ne prouve pas qu'un contrôle est effectivement déployé.

Toute nouvelle fonctionnalité importante — notamment authentification, paiement, stockage de données personnelles, upload de fichiers ou intégration d'API — doit déclencher une revue de sécurité spécifique.
