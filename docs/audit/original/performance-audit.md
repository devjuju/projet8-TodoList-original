# Audit de performance – Application originale

## Contexte

Le fichier `web/app_dev.php` du projet original limitait l'accès au mode développement aux requêtes provenant de `127.0.0.1`.

Dans un environnement Docker, les requêtes transitent par le réseau interne des conteneurs et utilisent une adresse IP de type `172.x.x.x`. Cette vérification empêchait donc l'accès au Profiler Symfony.

Pour réaliser l'audit de performance, cette restriction a été désactivée sur la branche d'audit afin d'autoriser le mode développement dans l'environnement Docker, sans modifier le fonctionnement de l'application en production.

---

## 🎯 Objectif

L'objectif de cet audit est d'évaluer les performances de l'application originale avant toute évolution fonctionnelle.

Les mesures ont été réalisées à l'aide :

- du profiler Symfony ;
- des métriques locales de Chrome DevTools.

---

## Environnement

- Symfony 3.1
- PHP 7.4
- Apache
- MySQL 5.7
- Docker

---

## Outils utilisés

- Symfony Web Profiler
- Chrome DevTools

---

## Mesures observées

### Temps d'exécution

| Indicateur             | Valeur     |
| ---------------------- | ---------- |
| Temps total            | **239 ms** |
| Initialisation Symfony | **46 ms**  |
| Mémoire maximale       | **8 MB**   |

---

### Web Vitals

| Indicateur                      | Valeur     |
| ------------------------------- | ---------- |
| Largest Contentful Paint (LCP)  | **0.60 s** |
| Cumulative Layout Shift (CLS)   | **0**      |
| Interaction to Next Paint (INP) | **0 ms**   |

Ces valeurs sont considérées comme très bonnes pour une exécution en environnement local.

---

## Analyse du profiler Symfony

Le profiler montre les étapes principales de traitement d'une requête.

### Firewall

```text
24 ms
```

Une partie du temps est consacrée au composant Security.

C'est normal puisqu'il s'agit de la page de connexion.

---

### Firewall

```text
91 ms
```

Le contrôleur représente la partie la plus importante du traitement.

La majeure partie du temps correspond au rendu de la page.

---

### Rendu Twig

```text
security/login.html.twig
18 ms
```

Le rendu des templates reste rapide.

Aucune lenteur particulière n'a été observée.

---

### Réponse

```text
53 ms
```

Une partie de ce temps provient du Web Profiler lui-même.

Le profiler ajoute naturellement un surcoût lorsqu'il est activé.

### Mémoire

Le pic mémoire est faible :

```text
8 MB
```

Aucune consommation excessive n'a été observée.

## Observations

L'application présente de bonnes performances pour une application Symfony 3.1.

Les temps de réponse restent faibles.

Les Web Vitals sont excellents.

Aucun ralentissement important n'a été détecté.

En revanche, l'application reste construite sur une pile logicielle ancienne.

Les performances observées sont donc satisfaisantes, mais les versions utilisées ne sont plus maintenues.

## Conclusion

L'audit montre que les performances de l'application originale sont satisfaisantes.

Les principaux constats sont :

- temps d'exécution inférieur à 250 ms ;
- faible consommation mémoire (8 MB) ;
- Web Vitals excellents (LCP 0,60 s, CLS 0, INP 0 ms)
- aucun goulet d'étranglement majeur détecté dans le profiler Symfony.

Les travaux réalisés dans les branches suivantes viseront principalement à améliorer la qualité du code, la sécurité, la maintenabilité et la conformité au cahier des charges, davantage que les performances pures de l'application.
