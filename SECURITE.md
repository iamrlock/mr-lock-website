# Politique de sécurité — MR LOCK Website

> Ce document décrit les règles de sécurité du dépôt public `mr-lock-website` et la manière de signaler une vulnérabilité.

## 1. Objectif

Le site MR LOCK est un actif public pouvant évoluer vers un écosystème comprenant :

- site vitrine et personal branding ;
- produits digitaux ;
- produits et services IA ;
- paiements en ligne ;
- formulaires et prises de contact ;
- intégrations tierces ;
- éventuellement des espaces authentifiés.

La sécurité doit donc être intégrée dès la conception et non ajoutée après coup.

## 2. Principe fondamental

**Tout ce qui arrive du navigateur, d'un utilisateur ou d'un service externe doit être considéré comme non fiable jusqu'à validation côté serveur.**

L'interface utilisateur ne constitue jamais une preuve d'autorisation, de paiement ou de propriété.

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
- fichiers `.env` contenant des valeurs réelles.

Utiliser des variables d'environnement et fournir uniquement les noms nécessaires dans un fichier `.env.example`.

Si un secret est accidentellement publié :

1. considérer le secret comme compromis ;
2. le révoquer/faire tourner immédiatement chez le fournisseur concerné ;
3. supprimer sa présence du code courant ;
4. vérifier les journaux et usages lorsque le fournisseur le permet ;
5. ne pas considérer un simple nouveau commit comme une révocation du secret exposé dans l'historique Git.

## 4. Paiements

Pour toute intégration FedaPay ou autre prestataire de paiement :

- les clés secrètes restent côté serveur ;
- le montant et le produit doivent être déterminés/validés côté serveur ;
- le statut d'une transaction doit être vérifié côté serveur ;
- les webhooks doivent être authentifiés selon la documentation officielle du fournisseur ;
- le traitement d'un webhook doit être idempotent ;
- une URL de retour navigateur ne doit jamais suffire à débloquer un produit ;
- une notification provenant du client ne doit jamais être considérée comme une preuve de paiement.

## 5. Authentification et autorisation

Si une authentification est ajoutée :

- appliquer le principe du moindre privilège ;
- vérifier les permissions côté serveur ;
- ne jamais considérer une route masquée comme protégée ;
- protéger les opérations sensibles contre les accès directs ;
- éviter de révéler des informations permettant d'énumérer des comptes ou ressources privés ;
- invalider correctement les sessions et tokens lors de leur expiration ou révocation.

## 6. Validation des entrées

Valider côté serveur toutes les données provenant de :

- formulaires ;
- query strings ;
- paramètres de route ;
- APIs publiques ;
- webhooks ;
- uploads ;
- intégrations tierces.

Les validations doivent couvrir au minimum le type, le format, la taille et les valeurs autorisées lorsqu'elles sont connues.

## 7. XSS et contenu utilisateur

Tout contenu fourni par un utilisateur doit être échappé ou rendu via un mécanisme sûr.

Éviter l'injection HTML arbitraire et l'utilisation non nécessaire de mécanismes permettant l'exécution de HTML/JavaScript fourni dynamiquement.

Ne jamais injecter directement du contenu utilisateur dans :

- HTML ;
- JavaScript ;
- attributs sensibles ;
- requêtes SQL ;
- commandes système.

## 8. Injection et accès aux données

Les requêtes vers une base de données ou un service externe doivent utiliser des mécanismes paramétrés ou structurés adaptés.

Ne jamais construire une requête sensible en concaténant directement une entrée utilisateur non validée.

Limiter également les données retournées par les APIs au strict nécessaire.

## 9. CSRF et actions sensibles

Les opérations modifiant des données ou déclenchant une action importante doivent être protégées contre les requêtes non autorisées lorsque le mécanisme d'authentification utilisé le nécessite.

Les actions sensibles doivent vérifier :

- identité ;
- autorisation ;
- validité de la requête ;
- état courant de la ressource.

## 10. Webhooks

Tout webhook doit être traité comme une entrée externe non fiable.

Avant traitement :

1. vérifier son authenticité/signature selon le fournisseur ;
2. valider sa structure ;
3. vérifier les identifiants attendus ;
4. rendre le traitement idempotent ;
5. éviter de faire confiance aveuglément aux valeurs fournies ;
6. journaliser suffisamment pour diagnostiquer un incident sans enregistrer de secrets.

## 11. Uploads

Si le site accepte un jour des fichiers :

- limiter la taille ;
- limiter les types autorisés ;
- ne pas faire confiance uniquement à l'extension ;
- éviter de rendre directement exécutables les fichiers uploadés ;
- stocker les fichiers selon un modèle d'accès contrôlé ;
- utiliser des noms générés plutôt que des noms fournis directement par l'utilisateur.

## 12. Headers et navigateur

Lorsque l'architecture le permet, appliquer une politique de sécurité HTTP adaptée au site, notamment :

- Content Security Policy ;
- HSTS lorsque HTTPS est garanti ;
- X-Content-Type-Options ;
- Referrer-Policy ;
- permissions/politiques navigateur adaptées aux besoins réels.

Une politique stricte ne doit pas être ajoutée aveuglément : elle doit être testée avec les scripts, fonts, images, analytics et services réellement utilisés.

## 13. Dépendances

Avant d'ajouter une dépendance :

- vérifier qu'elle est réellement nécessaire ;
- préférer des versions maintenues ;
- éviter les packages abandonnés ou excessivement puissants ;
- surveiller les vulnérabilités connues ;
- limiter les dépendances inutiles afin de réduire la surface d'attaque.

Les lockfiles doivent être conservés lorsqu'ils font partie du workflow du projet.

## 14. Protection des données

Ne collecter que les données nécessaires à une finalité clairement définie.

Éviter de stocker inutilement :

- mots de passe en clair ;
- données personnelles non nécessaires ;
- informations de paiement sensibles ;
- tokens ou secrets dans les logs.

Les données sensibles ne doivent pas être exposées dans les URLs lorsque cela peut être évité.

## 15. Logs

Les logs doivent aider au diagnostic sans devenir une fuite d'informations.

Ne pas journaliser en clair :

- mots de passe ;
- tokens ;
- clés API ;
- secrets de webhook ;
- données de paiement sensibles.

## 16. Erreurs

Les messages d'erreur publics doivent rester suffisamment génériques pour ne pas révéler :

- secrets ;
- stack traces internes ;
- chemins de fichiers sensibles ;
- credentials ;
- détails inutiles sur l'infrastructure.

Les détails de diagnostic doivent rester côté serveur lorsque cela est nécessaire.

## 17. Sécurité du dépôt GitHub

Le dépôt étant public :

- aucun secret ne doit être commit ;
- les fichiers de configuration locale doivent être ignorés ;
- les workflows GitHub Actions doivent utiliser des permissions minimales ;
- les actions tierces doivent être choisies avec prudence ;
- les dépendances doivent être maintenues ;
- les changements sensibles doivent être revus avant déploiement.

Lorsque le dépôt sera suffisamment mature, envisager également les protections de branche et les contrôles automatisés adaptés au niveau de risque du projet.

## 18. Revue de sécurité avant déploiement

Pour toute fonctionnalité sensible, vérifier au minimum :

- [ ] aucune clé ou donnée sensible dans le code ;
- [ ] entrées validées côté serveur ;
- [ ] permissions vérifiées côté serveur ;
- [ ] erreurs contrôlées ;
- [ ] secrets absents des logs ;
- [ ] dépendances à jour ou risque connu documenté ;
- [ ] paiements vérifiés côté serveur ;
- [ ] webhooks authentifiés et idempotents ;
- [ ] headers de sécurité adaptés ;
- [ ] tests/build exécutés lorsque disponibles.

## 19. Signaler une vulnérabilité

Ne publiez pas les détails d'une vulnérabilité non corrigée dans une issue publique.

Pour un problème de sécurité, contactez directement le mainteneur du projet via un canal privé associé au compte GitHub `iamrlock` et fournissez :

- une description claire du problème ;
- les étapes de reproduction ;
- l'impact potentiel ;
- les conditions nécessaires à l'exploitation ;
- toute preuve de concept raisonnable et non destructive ;
- une suggestion de correction si vous en avez une.

Ne tentez pas d'accéder à des données qui ne vous appartiennent pas, de dégrader le service, de supprimer des données ou de poursuivre une exploitation après avoir démontré le problème.

## 20. Limites de cette politique

Ce document décrit les pratiques de sécurité attendues dans le dépôt. Il ne constitue pas une garantie de sécurité absolue.

Toute nouvelle fonctionnalité importante — notamment authentification, paiement, stockage de données personnelles, téléchargement de fichiers ou intégration d'API — doit déclencher une revue de sécurité spécifique.
