
# Certains d’entre vous l’ont remarqué : c’est particulièrement flagrant sur les PC portables, **le son sous Linux est souvent plus faible et plus plat** que sous Windows.

Vidéo de démonstration : [Vanilla Linux sound vs Preset by digitalone1](https://youtu.be/otikXMm0ftQ)
Je précise sur la vidéo on entend déjà bien la différence mais appliqué en dur le son est encore largement meilleur.


Tout d'abord merci à CachyOS de m'avoir mis sur la piste : [CachyOS Améliorations audio et logicielles](https://wiki.cachyos.org/fr/configuration/general_system_tweaks/#am%C3%A9liorations-audio-et-logicielles)

### Pourquoi ?

Sous Windows, les constructeurs (Realtek + Dell/Alienware, Dolby/Waves/MaxxAudio, etc.) appliquent une chaîne DSP (EQ, loudness, compression, spatialisation).
Sous Linux, le son sort “brut” de pipewire → donc il sort moins fort et moins punchy. Certains casques et enceintes s'en sortent bien mais la plupart du matériel et surtout les petites enceintes de nos pc portables ont besoin de ça pour sortir un son correct.

👉 La solution propre : **PipeWire + EasyEffects** avec une chaîne d’effets bien placée, et des profils automatiques.

---

## 1) Installation pour Arch Linux, je ne traite pas le reste je n'utilise que des bases Arch désolé.

### Paquets requis

```bash
sudo pacman -Syu
sudo pacman -S easyeffects
```

### Plugins recommandés (indispensables pour EQ/compresseur/loudness, etc.)

```bash
sudo pacman -S lsp-plugins-lv2 zam-plugins-lv2 calf mda.lv2
```
On choisi jalv et qtractor en dépendances.

> Remarque : `lsp-plugins-lv2` est le plus important (EQ/compresseur/loudness de très bonne qualité).

---

## 2) Configuration de base (EasyEffects)

1. Lance **EasyEffects**
2. Va dans l’onglet **Sortie (Output)**
3. Va dans **Effets (Effects)** pour gérer ta chaîne

<img width="2089" height="1225" alt="image" src="https://github.com/user-attachments/assets/a2620531-279e-4077-a340-ef044f89e5f0" />


✅ Objectif : créer un preset “Speakers” et un preset “Headphones”, avec auto-chargement.

---

## 3) Presets communautaires

Le plus simple : partir d’un preset existant puis ajuster. 

Celui de Digital1 est assez passe-partout : https://github.com/Digitalone1/EasyEffects-Presets

```bash
cd ~/Téléchargements
git clone https://github.com/Digitalone1/EasyEffects-Presets.git

mkdir -p ~/.local/share/easyeffects/output
cp -v EasyEffects-Presets/LoudnessEqualizer.json ~/.local/share/easyeffects/output/
cp -v EasyEffects-Presets/LoudnessCrystalEqualizer.json ~/.local/share/easyeffects/output/
```

### 3.1 Importer un preset local

* Télécharge un preset (ex : Digitalone1, ou autre)
* Dans EasyEffects : **Préréglages → Importer un préréglage depuis le stockage local**
* Une fois importé : **Load**

### Emplacement des presets

**Version native :**

* Sortie : `~/.local/share/easyeffects/output`
* Entrée : `~/.local/share/easyeffects/input`

**Version Flatpak :**

* Sortie : `~/.var/app/com.github.wwmm.easyeffects/data/easyeffects/output`
* Entrée : `~/.var/app/com.github.wwmm.easyeffects/data/easyeffects/input`

---

## 4) Créer un profil “haut-parleurs”

Dans **Préréglages**, clique sur **+** et crée un nouveau preset :
Par exemple : `haut-parleurs`

### Chaîne d’effets recommandée (ordre très important)

Pour haut-parleurs de laptop, une base fiable :

1. **Égaliseur** (EQ)
2. **Compresseur** (ou Loudness/Maximizer selon ton goût)
3. **Convolver** *(optionnel, voir section 6)*
4. **Limiteur** ✅ **TOUJOURS EN DERNIER** (crucial contre la distorsion)

> Pourquoi cet ordre ?

* EQ et compression “sculptent” le signal
* Convolver ajoute souvent du gain/perception de volume
* Limiteur en dernier empêche l’écrêtage (clipping)

---

## 5) Profil “Casques” 

Crée un second preset :
✅ Nom conseillé : `Headphones`

Comme le conseille digital1 on ajoute flux croisé à la fin pour les casques. Le crossfeed fait passer un petit peu de gauche vers droite (et inversement), avec un filtrage/délai, pour imiter l’écoute sur enceintes (où chaque oreille entend les deux enceintes). Ça rend le son plus naturel.

### Chaîne recommandée pour casque

1. Égaliseur
2. Compresseur / Loudness (léger)
3. Limiteur ✅ (toujours dernier “niveau”)
4. **Flux Croisé (Crossfeed)** **tout à la fin**

### Réglages de départ Crossfeed (safe)

* Coupure : **700 Hz**
* Flux : **4.5 dB**

Ajustement :

* trop fort / trop “resserré” → Flux **3 dB**
* pas assez → Flux **6 dB**

---

## 6) Bonus : Convolver (effet “Dolby Atmos laptop” / amélioration des HP)

Le **Convolver** peut transformer les haut-parleurs si tu as une **impulse response (IR) adaptée**.

### 6.1 Où le placer ?

✅ Place le **Convolver AVANT le Limiteur**.

**Convolver → Limiteur** est obligatoire, parce que le Convolver peut booster énormément le signal.

### 6.2 Quel fichier charger ?

Le Convolver charge une **IR au format .wav**.

Le mieux : une IR spécifique à ton modèle (quand ça existe).
Quelques ressources (exemples donnés par CachyOS) :

* ArchWiki ThinkPad T14 (AMD) (IR speakers)
* Repo ThinkPad P14s G4
* Repo linux-thinkpad-speaker-improvements

📌 Pour ton matériel il n’y a pas forcément une IR “prête” et fiable.
Donc :
* soit tu restes sur EQ+compression+limiteur (déjà énorme)
* soit tu testes des IR “laptop” génériques (risque de son artificiel)

### 6.3 Prévenir l’écrêtage (clipping)

✅ Après le Convolver : **Limiteur**
Réglage conseillé :

* Ceiling / Output : **-1.0 dB**

---

## 7) Conseils d’ajustement (pro)

* Ajuste **petit à petit** (±2–4 dB sur EQ)
* Compare souvent : **toggle EasyEffects ON/OFF**
* Si tu entends de la distorsion :

  * baisse le gain (ou l’EQ bass)
  * assure-toi que **Limiteur est le dernier**
* Active l’auto-démarrage dans EasyEffects

---

## 8) Auto-chargement des presets (Speakers vs Headphones)

Associer un preset par périphérique.

Dans EasyEffects :

* Onglet **PipeWire → Presets Autoloading** (ou “Chargement automatique” selon version)

<img width="2089" height="1229" alt="image" src="https://github.com/user-attachments/assets/8bc6680e-515a-493e-838b-ede6dbe073a7" />


### Exemple

* “Built-in Speakers / Haut-parleurs internes” → `Laptop-Speakers`
* “Bose QC Ultra Headphones” → `Headphones-Universal`
* (Optionnel) casque filaire → `Headphones-Universal`

---

## 9) HORS SUJET mais Bonus pour le plaisir : éviter le mode “main libre” (HFP/HSP) inutile

Problème classique : dès qu’une app touche au micro BT, certains casques basculent en **mode main libre** → qualité audio catastrophique.

### Mitigation WirePlumber

Crée :

```bash
mkdir -p ~/.config/wireplumber/wireplumber.conf.d
nano ~/.config/wireplumber/wireplumber.conf.d/51-disable-hsp-autoswitch.conf
```

Ajoute :

```conf
wireplumber.settings = {
  bluetooth.autoswitch-to-headset-profile = false
}

monitor.bluez.properties = {
  bluez5.roles = [ a2dp_sink a2dp_source ]
}
```

Puis :

```bash
systemctl --user restart wireplumber
```

✅ Résultat : ton casque reste en **A2DP High Fidelity**, et ne bascule plus en “main libre” pour rien.

---

# Conclusion

Avec :

* un preset **Laptop-Speakers** bien chaîné
* un preset **Headphones** avec Flux Croisé
* auto-chargement par périphérique
* mitigation WirePlumber pour Bluetooth

…tu retrouves un son **plus fort**, plus “plein”, et plus agréable, proche de ce que Windows donne via ses DSP.
