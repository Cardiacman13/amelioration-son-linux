# Certains d’entre vous l’ont remarqué, c’est particulièrement flagrant sur les PC portables, **le son sous Linux est plus faible et plus plat** que sous Windows.

> ⚠️ **WARNING — Risque pour tes haut-parleurs / ton audition**
> Ce tuto **modifie la chaîne audio** (EQ, compression, convolver, gain, etc.). Si tu pousses les réglages **n’importe comment**, tu peux **faire saturer (clipping)**, créer de la **distorsion**, et **endommager tes haut-parleurs** (surtout ceux de PC portable, très fragiles) — voire te faire mal aux oreilles.
>
> ✅ Règles de base :
>
> * Monte le volume **progressivement** et teste à faible niveau d’abord.
> * Si tu entends **craquements / grésillements / distorsion** → **stop**, baisse le gain / les basses.
> * **Limiteur OBLIGATOIRE en dernier** (et idéalement ceiling à **-1 dB**).
> * Le **Convolver peut booster énormément** : ne l’active jamais sans limiteur derrière.
>
> Tu suis ce tuto **à tes risques** : je ne peux pas garantir un résultat sûr pour tous les modèles de PC / enceintes.

Vidéo de démonstration : [Vanilla Linux sound vs Preset by digitalone1](https://youtu.be/otikXMm0ftQ)
Je précise sur la vidéo on entend déjà bien la différence mais appliqué en dur le son est encore largement meilleur.

Vidéo ou je fais rapidement ce tuto : [Installation Et Setup De EasyEffect](https://youtu.be/eCN7mJDihAg)

Tout d'abord merci à CachyOS de m'avoir mis sur la piste : [CachyOS Améliorations audio et logicielles](https://wiki.cachyos.org/fr/configuration/general_system_tweaks/#am%C3%A9liorations-audio-et-logicielles)

### Pourquoi ?

Sous Windows, les constructeurs (Realtek + Dell/Alienware,HP,LENOVO, Dolby/Waves/MaxxAudio, etc.) appliquent une chaîne d'effets adaptées au materiel (EQ, loudness, compression, spatialisation).
Sous Linux, le son sort “brut” de pipewire → donc il sort moins fort et moins punchy. Certains casques et enceintes ultra haut de gamme s'en sortent bien mais la plupart du matériel et surtout les petites enceintes de nos pc portables ont besoin de ça pour sortir un son correct.

👉 La solution propre : **PipeWire + EasyEffects** avec une chaîne d’effets bien placée, et des profils automatiques.

---

## 1) Installation pour Arch Linux, je ne traite pas le reste je n'utilise que des bases Arch désolé.

### Paquets requis

```bash
sudo pacman -S easyeffects lsp-plugins-lv2 zam-plugins calf mda.lv2
```

Si on te demande choisi **jalv** et **qtractor** en dépendances.

> Remarque : `lsp-plugins-lv2` est le plus important (EQ/compresseur/loudness de très bonne qualité).

---

## 2) Configuration de base (EasyEffects)

### Lance **EasyEffects** et mets en démarrage auto :

<img width="986" height="645" alt="image" src="https://github.com/user-attachments/assets/81df96d1-7b6b-49a6-9078-6f1acaaff935" />

<img width="986" height="645" alt="image" src="https://github.com/user-attachments/assets/53c6333f-3887-4a5f-b216-f9a3edfe8414" />

<img width="1118" height="716" alt="image" src="https://github.com/user-attachments/assets/f3601904-8c38-4d21-8a59-4f8b0f25430d" />

---

## 3) Presets communautaires

Le plus simple : partir d’un preset existant puis ajuster. 

Celui de Digital1 est assez passe-partout : https://github.com/Digitalone1/EasyEffects-Presets

Paquet natif :
```bash
cd ~/Téléchargements
git clone https://github.com/Digitalone1/EasyEffects-Presets.git
mkdir -p ~/.local/share/easyeffects/output
cp -v EasyEffects-Presets/LoudnessEqualizer.json ~/.local/share/easyeffects/output/
```

Et les presets devraient apparaitre dans local vous pouvez le renomer Haut-Parleurs.

<img width="1224" height="881" alt="image" src="https://github.com/user-attachments/assets/6b2d2387-7f8c-4d89-a777-acc67c7238fb" />


### 3.1 Importer un preset local

* Télécharge un preset (ex : Digitalone1, ou autre) par exemple [ici](https://github.com/wwmm/easyeffects/wiki/Community-presets)
* Tu peux peut être en trouver un spécifique pour ton pc ici : [wiki de la communauté Easy Effect](https://github.com/wwmm/easyeffects/wiki/Community-presets)
* Dans EasyEffects : **Préréglages → Importer un préréglage depuis le stockage local**
* Cherche le preset .json ou autres

### Emplacement des presets

**Version native :**

* Sortie : `~/.local/share/easyeffects/output`
* Entrée : `~/.local/share/easyeffects/input`

---

## 4) Créer un profil “haut-parleurs”

Dans **Préréglages**, clique sur **+** et crée un nouveau preset :
Par exemple : `haut-parleurs`

### Chaîne d’effets recommandée (ordre très important)

Pour haut-parleurs de pc portable, une base fiable :

1. **Égaliseur** (EQ)
2. **Compresseur** (ou Loudness/Maximizer selon ton goût)
3. **Convolver** *(optionnel, voir section 6)*
4. **Limiteur** **EN DERNIER** (crucial contre la distorsion)

> Pourquoi cet ordre ?

* EQ et compression “sculptent” le signal
* Convolver ajoute souvent du gain/perception de volume
* Limiteur en dernier empêche l’écrêtage (clipping)

---

## 5) Profil “Casques” 

Crée un second preset :

Comme le conseille digital1 on ajoute flux croisé à la fin pour les casques. Le crossfeed fait passer un petit peu de gauche vers droite (et inversement), avec un filtrage/délai, pour imiter l’écoute sur enceintes (où chaque oreille entend les deux enceintes). Ça rend le son plus naturel.

### Chaîne recommandée pour casque

1. Égaliseur
2. Compresseur / Loudness (léger)
3. Limiteur 
4. **Flux Croisé (Crossfeed)** **tout à la fin**

### Réglages de départ Crossfeed
* Coupure : **700 Hz**
* Flux : **4.5 dB**

Ajustement :

* trop fort / trop “resserré” → Flux **3 dB**
* pas assez → Flux **6 dB**

---

## 6) Bonus : Convolver (effet “Dolby Atmos laptop” / amélioration des haut-parleurs )

Le **Convolver** peut énormément améliorer le son des haut-parleurs si tu as une **impulse response (IR) adaptée**. C'est  surtout pour les pc portables compatibles sur le reste ça sonnera robotique.

Vous pouvez tester ceux là qui sont assez passe-partout : https://stuff.kurz.pw/arch/P14s_G4/Speakers/

Mais essayez de trouver un conçu pour votre pc portable.

### 6.1 Où le placer ?

Place le **Convolver AVANT le Limiteur**.

**Convolver → Limiteur** est obligatoire, parce que le Convolver peut booster énormément le signal.

### 6.2 Quel fichier charger ?

Le Convolver charge une **IR au format .wav**.

<img width="1703" height="1306" alt="image" src="https://github.com/user-attachments/assets/193f8242-b606-4991-ae6a-0258db7cd866" />

<img width="686" height="138" alt="image" src="https://github.com/user-attachments/assets/ba31fd7f-af15-4bd1-a43e-a9de254aa9fa" />

<img width="1528" height="1143" alt="image" src="https://github.com/user-attachments/assets/421704e5-070c-4142-90b7-af045adfe08c" />


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

Après le Convolver : **Limiteur**
Réglage conseillé :

* Ceiling / Output : **-1.0 dB**

---

## 7) Conseils d’ajustement

* Ajuste **petit à petit**
* Compare souvent : **Effects ON/OFF**
* Si tu entends de la distorsion :
  * baisse le gain (ou l’EQ bass)
  * assure-toi que **Limiteur est le dernier**
* Active l’auto-démarrage dans EasyEffects

---

## 8) Auto-chargement des presets (Casque / haut-parleurs )

Associer un preset par périphérique.

Dans EasyEffects :

* Onglet préréglages / Chargement automatique

<img width="676" height="860" alt="image" src="https://github.com/user-attachments/assets/bf6e0305-2fea-4388-927b-beca50075bb4" />

### Exemple

* “Built-in Speakers / Haut-parleurs internes” → `haut-parleurs`
* “Bose QC Ultra Headphones” → `casque`

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

* un preset **Laptop-Speakers** bien chaîné avec le convolver
* un preset **Headphones** avec Flux Croisé mais SANS le convolver
* auto-chargement par périphérique
* mitigation WirePlumber pour Bluetooth

…tu retrouves un son **plus fort**, plus “plein”,spacialisé, et plus agréable, proche de ce que Windows donne via ses DSP.
