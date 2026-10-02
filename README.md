<div align="center">

# YourLauncher

**Launcher Minecraft desktop moderne**, avec gestion de profils, installation automatique de mods, Discord Rich Presence et synchronisation directe avec l'écosystème YourMC.

![Status](https://img.shields.io/badge/status-production-success)
![Platform](https://img.shields.io/badge/platform-Windows%20%7C%20macOS%20%7C%20Linux-informational)

</div>

---

> **À propos de ce dépôt.** YourLauncher fait partie de l'écosystème [YourMC](https://github.com/xZetaFR/YourMC) ; le code source n'est pas publié ici. Ce dépôt documente l'architecture, les choix techniques et le fonctionnement du launcher, à des fins de portfolio.

## Le principe

YourLauncher remplace le launcher officiel par une expérience pensée pour les serveurs modded : plusieurs profils de jeu, installation des mods en un clic, synchronisation du compte avec la plateforme YourMC (licences, solde de monnaie en jeu) et mises à jour automatiques, le tout dans une interface desktop native multi-plateforme.

## Fonctionnalités

- **Comptes synchronisés avec YourMC** — connexion OAuth, licences et solde affichés en direct dans le launcher.
- **Profils multiples** — plusieurs installations en parallèle (version Minecraft, Forge/Fabric, modpack), sans se marcher dessus.
- **Installation automatique des mods** — téléchargement, vérification d'intégrité et mise en place des modpacks sans manipulation manuelle.
- **Discord Rich Presence** — affiche la partie en cours directement sur le profil Discord du joueur.
- **Mises à jour automatiques** — le launcher se met à jour tout seul au démarrage, sans action de l'utilisateur.
- **Multi-plateforme** — un seul launcher, distribué en `.exe` (Windows), `.dmg` (macOS) et `.AppImage` (Linux).

## Stack

`Electron` · `Node.js` · `JavaScript` · `EJS` · `Helios Core`

---

## Architecture technique

<details>
<summary><strong>Détails pour les développeurs</strong></summary>

### Structure

```
app/
  main.js            processus principal Electron
  preload.js          pont sécurisé process principal ↔ renderer
  assets/
    html/              interface (launcher, profils, paramètres, authentification)
    js/
      launcher.js       logique principale du launcher
      profile.js        gestion des profils
      account.js        gestion des comptes
      api.js             client API YourMC
      balancemanager.js  solde de monnaie en jeu, mis à jour en direct
clients/              binaires et librairies Minecraft par version
libraries/
  helios-core/          moteur de lancement Minecraft (téléchargement, vérification, launch)
scripts/
  run-dist.js            build de distribution
  sign-windows.js         signature Authenticode (Windows)
  codesign-macos.js       signature Apple (macOS)
```

### Lancement du jeu

Le cœur du lancement (téléchargement des assets/librairies Minecraft, résolution des dépendances Forge/Fabric, vérification des checksums, construction de la commande Java) repose sur **Helios Core**, qui encapsule la logique bas niveau du launcher officiel Mojang. L'app Electron orchestre par-dessus : sélection du profil, choix du compte, affichage de la progression, puis délègue le lancement effectif.

### Synchronisation avec la plateforme YourMC

Authentification OAuth2 contre l'API YourMC ; le token est conservé localement et rafraîchi automatiquement avant expiration. Le solde de monnaie en jeu est interrogé périodiquement (`GET /api/player/{uuid}/balance`) avec un court cache local, pour rester à jour sans spammer l'API.

### Distribution multi-OS

Build via `electron-builder`, packagé séparément pour chaque plateforme (`dist:win`, `dist:mac`, `dist:linux`). Les builds Windows et macOS sont signés (Authenticode / certificat Apple) pour éviter les avertissements de sécurité au premier lancement. Mise à jour automatique via `electron-updater` : vérification au démarrage, téléchargement silencieux en arrière-plan, redémarrage après installation.

</details>

---

<sub>Documentation de portfolio — le code source reste privé.</sub>
