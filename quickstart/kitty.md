# Installation Kitty

Date: 2025-10-04

## Objectif

Installer Kitty sur Ubuntu 24.04 et le configurer en 5–10 minutes (avec une base propre, police, thèmes, raccourcis utiles). 

---

## 1) Installation

- Installation
  ```
  sudo apt update
  sudo apt install -y kitty
  ```

- Définir Kitty comme terminal par défaut (si installé via apt)
  ```
  sudo update-alternatives --config x-terminal-emulator
  ```
  Choisissez “kitty”.

---

## 2) Police et couleurs (rapide)

- Police lisible
  ```
  sudo apt install -y fonts-firacode
  ```
  Si vous utilisez des glyphes “Nerd Font”, installez votre police Nerd favorite et sélectionnez-la plus bas.

- Thèmes Kitty (sélecteur interactif)
  ```
  kitty
  kitty +kitten themes
  ```

---

## 3) Fichier de config minimal
Créez ~/.config/kitty/kitty.conf avec ceci (modifiable à chaud: enregistrez le fichier, Kitty recharge tout seul):

```
# ~/.config/kitty/kitty.conf

# Police
font_family        FiraCode
font_size          12.5

# Ergonomie
cursor_shape       block
enable_audio_bell  no
confirm_os_window_close 0
window_padding_width 6

# Rendu
background_opacity 0.95
disable_ligatures  never

# Navigation (exemples de raccourcis utiles)
map ctrl+shift+enter new_window
map ctrl+shift+t     new_tab
map ctrl+shift+w     close_window
map ctrl+shift+f     search_in_terminal
map ctrl+shift+left  neighboring_window left
map ctrl+shift+right neighboring_window right
map ctrl+shift+up    neighboring_window up
map ctrl+shift+down  neighboring_window down
```

- Tous les changements sont appliqués live.

---

## 4) Démarrer et utiliser tout de suite

- Lancer Kitty
  - Via le menu Applications (Kitty) ou: `kitty`
- Splits/onglets de base
  - Nouveau split: Ctrl+Shift+Enter
  - Nouvel onglet: Ctrl+Shift+T
  - Fermer l’onglet/fenêtre: Ctrl+Shift+W
  - Naviguer entre splits: Ctrl+Shift+Flèches

---

## 5) Intégration shell (copier/coller, URL, etc.)

- Kitty fonctionne nativement sur Wayland et Xorg.
- Copier/coller fonctionne classiquement (Ctrl+Shift+C / Ctrl+Shift+V). Si besoin, vous pouvez remapper dans kitty.conf (map ctrl+shift+c copy_to_clipboard, etc.).
- Affichage d’images en terminal (sympa pour vérifier): `kitty +kitten icat image.png`.

---
