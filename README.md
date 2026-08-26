# Cofocus
Café - Coworking


# CoFocus ☕

[![Build Status](https://img.shields.io/github/actions/workflow/status/Pierre679717/cofocus/ci.yml?branch=main&label=build)](https://github.com/Pierre679717/cofocus/actions)
[![Angular](https://img.shields.io/badge/Angular-18-red)](https://angular.dev)
[![TypeScript](https://img.shields.io/badge/TypeScript-5.5-blue)](https://www.typescriptlang.org)
[![SCSS](https://img.shields.io/badge/SCSS-design%20system-pink)](https://sass-lang.com)
[![Accessibility](https://img.shields.io/badge/a11y-WCAG%202.1%20AA-green)](https://www.w3.org/WAI/WCAG21/quickref/)
[![License](https://img.shields.io/badge/license-MIT-lightgrey)](LICENSE)

> **Le bon endroit pour travailler et se régaler.**
>
> CoFocus est un café-coworking toulousain fictif pour lequel j'ai construit un site complet : vitrine, carte du café et réservation de places à l'heure. Le cœur du projet est un plan de salle interactif piloté par les signals d'Angular — la démonstration concrète d'une gestion d'état réactive moderne, sans une seule ligne de RxJS.

---

## Table des matières

- [Vision du projet](#vision-du-projet)
- [Architecture](#architecture)
- [Stack technique](#stack-technique)
- [Quick Start](#quick-start)
- [Fonctionnalités par version](#fonctionnalités-par-version)
- [Le plan de salle interactif](#le-plan-de-salle-interactif)
- [Accessibilité & performance](#accessibilité--performance)
- [Tests](#tests)
- [Roadmap](#roadmap)
- [Réflexion d'architecture](#réflexion-darchitecture)
- [Structure du repo](#structure-du-repo)

---

## Vision du projet

La quasi-totalité des portfolios Angular juniors racontent la même histoire : une liste de produits, un panier, un total. Le recruteur en voit dix par semaine et n'en retient aucun.

CoFocus prend le problème par l'autre bout. Le site vitrine existe parce qu'un vrai commerce en a besoin — mais l'objet du projet, c'est **le plan de salle** : vingt-quatre places positionnées sur un fond de salle, cliquables, dont l'état, le prix et la disponibilité se propagent tout seuls dans l'interface. C'est exactement le type de composant où une mauvaise gestion d'état se voit immédiatement, et où les signals d'Angular 17+ montrent ce qu'ils valent face à un `BehaviorSubject`.

Autour de cette pièce centrale, le projet applique de bout en bout ce qu'on attend d'un dev Angular en 2026 : composants standalone, control flow `@if` / `@for`, formulaires réactifs validés, design system SCSS sans aucune valeur en dur, tests unitaires sur la logique métier, intégration continue et déploiement automatique.

```
┌─────────────────────────────────────────────────────────┐
│                    NAVIGATEUR                           │
└────────────────────────┬────────────────────────────────┘
                         │
                         ▼
┌─────────────────────────────────────────────────────────┐
│              app.routes.ts — routes lazy                │
│   /  ·  /cafe  ·  /coworking  ·  /contact  ·  /profil   │
└──────┬──────────────────┬──────────────────┬────────────┘
       │                  │                  │
       ▼                  ▼                  ▼
┌──────────────┐  ┌────────────────┐  ┌──────────────────┐
│    LAYOUT    │  │     PAGES      │  │  SHARED / UI     │
│   header     │  │  home  cafe    │  │  button  cards   │
│   footer     │  │  coworking     │  │  form-error      │
└──────────────┘  │  contact       │  └────────┬─────────┘
                  │  profil        │           │
                  └───────┬────────┘           │
                          │                    ▼
                          │           ┌──────────────────┐
                          │           │  styles/         │
                          │           │  tokens SCSS     │
                          │           │  mixins responsive│
                          │           └──────────────────┘
                          ▼
┌─────────────────────────────────────────────────────────┐
│                  core/services — ÉTAT                   │
│  CoworkingService   MenuService   UserService           │
│  signal() · computed() · immutabilité (map + spread)    │
└────────────────────────┬────────────────────────────────┘
                         │ effect()
                         ▼
                ┌──────────────────┐
                │   localStorage   │
                │  réservations    │
                │  persistées      │
                └──────────────────┘
```

---

## Stack technique

| Couche              | Technologie                                | Version |
| ------------------- | ------------------------------------------ | ------- |
| Framework           | Angular — standalone, signals, control flow | 18.2    |
| Langage             | TypeScript                                 | 5.5     |
| Styles              | SCSS — variables, mixins, mobile-first     | —       |
| État                | Angular Signals (`signal` / `computed` / `effect`) | natif   |
| Formulaires         | Reactive Forms + validateurs personnalisés | natif   |
| Persistance         | `localStorage` (stand-in backend)          | —       |
| Tests               | Jasmine + Karma (ChromeHeadless)           | 5.x     |
| Qualité             | ESLint (angular-eslint) + Prettier         | 18.x    |
| Images              | `NgOptimizedImage` + WebP (Squoosh)        | natif   |
| Audit               | Lighthouse + axe DevTools                  | —       |
| CI/CD               | GitHub Actions — lint · test · build       | —       |
| Hébergement         | Vercel — déploiement continu               | —       |
| Runtime             | Node.js LTS                                | 22.x    |

---

## Quick Start

```bash
# Cloner le repo
git clone https://github.com/Pierre679717/cofocus.git
cd cofocus

# Installer les dépendances (versions verrouillées par package-lock.json)
npm ci

# Lancer le serveur de développement
ng serve

# L'application est disponible sur http://localhost:4200
```

```bash
ng test --watch=false --browsers=ChromeHeadless   # tests unitaires
ng lint                                           # analyse statique
ng build --configuration production               # build optimisé
```

> **Note :** aucun backend, aucune variable d'environnement, aucune clé d'API. `npm ci && ng serve` suffit — un recruteur teste le projet en trente secondes.

---

## Fonctionnalités par version

### v0.1 — Les fondations

La coquille du site, construite avant toute fonctionnalité.

- **Design system SCSS** — couleurs, échelle typographique, échelle d'espacement, rayons, ombres et breakpoints extraits des maquettes et centralisés dans `_variables.scss`
- **Mixins responsive** — `respond-to($bp)` en mobile-first, `card`, `flex-center`
- **Header** sticky en HTML sémantique avec landmarks ARIA, états `:hover` et `:focus-visible`
- **Footer** collé en bas même sur page courte (`min-height: 100dvh` + flex)
- **Routing lazy** — cinq routes en `loadComponent()`, `routerLinkActive`, page 404 sur la route `**`

### v0.2 — Les pages vitrine

- **Accueil** — hero avec image WebP et `NgOptimizedImage priority`, section services en grille, carrousel de témoignages avec défilement automatique nettoyé dans `ngOnDestroy`
- **Carte du café** — modèle `MenuItem` typé, `MenuService` avec regroupement par catégorie en `computed`, prix formatés en EUR / fr-FR via `registerLocaleData`
- **Filtres combinés** — catégorie et recherche texte fusionnées dans un seul signal calculé, état vide géré par le bloc `@empty` de `@for`
- **Intégration continue** — GitHub Actions : `npm ci` → `ng lint` → `ng test` → `ng build` à chaque push

### v0.3 — Le plan de salle

Voir [Le plan de salle interactif](#le-plan-de-salle-interactif).

### v0.4 — Les formulaires

- **Formulaire de contact** construit au `FormBuilder` : validation sur le nom, l'email, le téléphone au format français, le message
- **Composant `form-error` réutilisable** — reçoit un `AbstractControl`, n'affiche le message qu'après `touched`, choisit le texte selon la clé d'erreur, `role="alert"` pour les lecteurs d'écran
- **Soumission gérée** — `markAllAsTouched()` si invalide, état `isSubmitting`, message de succès, `reset()`
- **Page Contact** — coordonnées, liens `mailto:` / `tel:`, carte en `<iframe loading="lazy">` avec mention RGPD

### v0.5 — L'espace membre

- Profil éditable — bascule affichage / édition par un signal, formulaire prérempli par `patchValue`
- Historique des réservations — deux `computed` séparant « à venir » et « passées », dates en `dd/MM/yyyy HH:mm` locale fr
- Programme de fidélité — points, barre de progression pilotée par `[style.width.%]`, récompenses

### v1.0 — Finitions et mise en ligne

- Responsive de 375 px à 1200 px, menu burger avec fermeture automatique au changement de route
- Accessibilité WCAG 2.1 AA vérifiée à l'audit
- Lighthouse > 90 sur les quatre axes
- Déploiement continu sur Vercel

---

## Le plan de salle interactif

> **La pièce non triviale du projet.** C'est celle sur laquelle je suis prêt à être interrogé ligne par ligne.

### Le modèle

Vingt-quatre places réparties en zones, chacune positionnée en pourcentage sur le conteneur — jamais en pixels, pour que le plan se redimensionne avec la fenêtre.

```ts
interface Seat {
  id: string;
  label: string;
  zone: Zone;
  type: SeatType;
  pricePerHour: number;
  x: number;          // % depuis la gauche du conteneur
  y: number;          // % depuis le haut
  status: SeatStatus; // 'available' | 'occupied' | 'selected'
}
```

### L'état, sans mutation

`toggleSeat()` ne modifie jamais le tableau existant : il en reconstruit un nouveau. C'est ce qui garantit que les `computed` qui en dépendent se réévaluent.

```ts
toggleSeat(id: string): void {
  this.seats.update(seats =>
    seats.map(s =>
      s.id === id && s.status !== 'occupied'
        ? { ...s, status: s.status === 'selected' ? 'available' : 'selected' }
        : s
    )
  );
}

readonly selectedSeats  = computed(() => this.seats().filter(s => s.status === 'selected'));
readonly availableCount = computed(() => this.seats().filter(s => s.status === 'available').length);
```

### Le prix, calculé et jamais stocké

Le total croise deux sources — les places sélectionnées et la durée choisie dans le formulaire — et n'existe nulle part en mémoire :

```ts
readonly totalPrice = computed(() =>
  this.selectedSeats().reduce((sum, s) => sum + s.pricePerHour, 0) * this.durationHours()
);
```

Un prix qu'on ne stocke pas est un prix qu'on ne peut pas désynchroniser.

### La persistance

Un unique `effect()` observe le signal des réservations et le sérialise dans le `localStorage` à chaque changement. Rien à appeler à la main, aucun risque d'oublier une sauvegarde dans une branche du code.

### L'accessibilité du composant

Une place n'est pas une `<div>` colorée : c'est un élément focalisable, doté d'un `aria-label` explicite (« Place A3, zone silencieuse, 4 € de l'heure, disponible »), inaccessible au clavier et à la souris lorsqu'elle est occupée (`pointer-events: none` + `aria-disabled`).

---

## Accessibilité & performance

> **Posture honnête :** l'accessibilité de ce projet a été vérifiée à l'outil et au clavier, pas auditée par un utilisateur de lecteur d'écran. C'est un niveau AA revendiqué, pas certifié.

| Exigence                     | Élément CoFocus                                        |
| ---------------------------- | ------------------------------------------------------ |
| Contrastes AA                | Tokens de couleur validés au ratio 4.5:1               |
| Navigation clavier           | Site entièrement parcourable sans souris               |
| Focus visible                | `:focus-visible` stylé sur tous les éléments interactifs |
| Structure sémantique         | Landmarks `header` / `nav` / `main` / `footer`         |
| Alternatives textuelles      | `alt` sur chaque image, SVG décoratifs en `aria-hidden` |
| Erreurs de formulaire        | `role="alert"` et liaison `aria-describedby`           |
| Cibles tactiles              | Minimum 44 × 44 px sur mobile                          |
| Chargement                   | Une route = un chunk séparé, images WebP, `priority` sur le seul hero |

---

## Tests

| Niveau               | Outil               | Ce qui est testé                                            |
| -------------------- | ------------------- | ----------------------------------------------------------- |
| Service métier       | Jasmine + TestBed   | `CoworkingService` — sélection, place occupée non sélectionnable, calcul du total, remise à zéro |
| Service de données   | Jasmine + TestBed   | `MenuService` — filtrage par catégorie, recherche insensible à la casse, regroupement |
| Composants           | ComponentFixture    | Affichage des `input()`, émission des `output()`, carrousel  |
| Formulaires          | TestBed             | Message d'erreur après blur, soumission bloquée si invalide, reset après succès |
| Qualité continue     | GitHub Actions      | Lint, tests et build à chaque push — badge en haut de ce README |

Le parti pris : tester la **logique**, pas le rendu. Un service testé vaut trois composants stylés en entretien.

---

## Roadmap

Ce qui est identifié comme « vraie application » mais hors scope de cette version :

- [ ] Backend Spring Boot remplaçant les données simulées — c'est le pont naturel avec [BankBridge](https://github.com/Pierre679717/BANKBRIDGE)
- [ ] Authentification réelle (JWT) à la place du profil simulé
- [ ] Réservations concurrentes : verrouillage d'une place le temps du tunnel
- [ ] Paiement en ligne (Stripe) et envoi d'un email de confirmation
- [ ] Internationalisation FR / EN via `@angular/localize`
- [ ] Tests end-to-end Playwright sur le parcours de réservation complet

---

## Réflexion d'architecture

### Pourquoi les signals et pas RxJS ?

Un `BehaviorSubject` marche parfaitement ici — mais il oblige à s'abonner, à se désabonner, à penser au pipe `async` et à la stratégie de détection de changement. Les signals suppriment ces quatre préoccupations pour un état synchrone et local comme une sélection de places. RxJS reste supérieur dès qu'il y a de l'asynchrone à composer, du `debounce`, de l'annulation de requête : c'est pour ça qu'il aurait sa place le jour où un backend arrive. Choisir l'outil selon la nature du problème, et non selon l'habitude, fait partie de la démonstration.

### Pourquoi pas de backend ?

Parce que la compétence que ce projet doit prouver est **Angular**, et qu'un backend bâclé aurait dilué le propos sans rien ajouter. Les données sont typées, cohérentes et centralisées dans des services : le jour où une vraie API arrive, seul le corps des méthodes change, pas la signature. La démonstration de la couche serveur, elle, est faite ailleurs — sur un projet Java/COBOL dédié.

### Pourquoi un design system maison plutôt qu'Angular Material ?

Material aurait livré un site correct en un quart du temps — et n'aurait rien prouvé. Écrire ses tokens, ses mixins et ses composants oblige à comprendre la cascade, les media queries mobile-first et les états de focus. Sur un projet de portfolio, la valeur est dans ce qu'on sait construire, pas dans ce qu'on sait installer.

### Pourquoi la syntaxe `input()` et `@for` plutôt que `@Input()` et `*ngFor` ?

Parce que c'est la syntaxe d'Angular 17+, que les `input()` sont des signals — donc intégrés au graphe de réactivité — et que `@for` impose un `track`, ce qui élimine par construction toute une famille de bugs de rendu. Je connais l'ancienne syntaxe et je sais expliquer la différence ; j'écris la nouvelle.

---

## Structure du repo

```
cofocus/
├── README.md
├── angular.json
├── vercel.json
├── docs/
│   ├── PREREQUISITES.md
│   ├── ARCHITECTURE.md
│   ├── DESIGN-SYSTEM.md
│   ├── SIGNALS.md
│   ├── COWORKING-FEATURE.md
│   └── RESPONSIVE-AUDIT.md
├── .github/
│   └── workflows/
│       └── ci.yml
└── src/
    ├── styles/
    │   ├── _variables.scss
    │   ├── _mixins.scss
    │   └── styles.scss
    └── app/
        ├── core/
        │   ├── models/
        │   ├── data/
        │   └── services/
        ├── shared/
        │   └── ui/
        ├── layout/
        │   ├── header/
        │   └── footer/
        ├── pages/
        │   ├── home/
        │   ├── cafe/
        │   ├── coworking/
        │   ├── contact/
        │   └── profile/
        └── app.routes.ts
```

---

## Auteur

Projet réalisé dans le cadre d'une certification de développeur web (RNCP 37674) et d'une trajectoire vers le développement bancaire, avec ambition d'expatriation au Luxembourg.
Toute remarque, retour technique ou opportunité sont les bienvenus.

**Pierre** · [GitHub](https://github.com/Pierre679717) · [LinkedIn](https://linkedin.com/in/pierre)

> *« Le Terminal et la Plume »*
