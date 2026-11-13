# Passation : installation de CleanFrame sur ma TV Samsung

## Contexte

- Projet : dépôt `garwix/cleanframe`, branche `claude/modest-feynman-schc00`.
- TV : Samsung **UE55RU7025KXXC** (2019, pas une The Frame, Tizen 5.0), logiciel **T-MSLDEUC-1510.0**.
- Ordinateur : Windows avec WSL2 (Ubuntu), sur le même Wi-Fi que la TV.
- Répondre en français.

## Déjà fait dans le code

Testé dans le navigateur uniquement (`npm run check` et `CLEANFRAME_PUBLIC_FONTS=1 npm run check:browser` passent), jamais sur la TV :

- Interface entièrement en français, horloge au format 24 h.
- Fond par défaut « Vieux-Port de Marseille » (Velvet, Wikimedia Commons, CC BY-SA 4.0), crédité dans `app/assets/PHOTO-CREDIT.md`, `docs/asset-sources.md` et `THIRD_PARTY_NOTICES.md`.
- Applis de l'accueil : YouTube, Netflix, Disney+, Prime Video, Canal+, Arte, Spotify, Ligue 1+.
  - Canal+, Arte et Ligue 1+ n'ont pas d'identifiant Tizen publié : `app/device.js` les reconnaît par leur nom installé, et l'identifiant est lu au premier lancement.
  - Ligue 1+ n'est probablement pas disponible sur une Samsung de 2019 (la FAQ officielle indique les modèles 2020 et plus récents). À vérifier dans Samsung Apps ; retirer la tuile si l'appli est absente.
- Bouton « Entrées HDMI » sur l'accueil.
- Bouton Mode Art masqué, car la TV n'est pas une Frame (`SHOW_ART` dans `app/main.js`).

## Avancement de l'installation

- WSL2 (Ubuntu) installé.
- Tizen Studio installé dans WSL, avec Certificate-Manager, cert-add-on, TV-SAMSUNG-Extension-Tools et TV-SAMSUNG-Extension-Resources.
  - Le processus s'est terminé sur « Killed » : vérifier avec `~/tizen-studio/package-manager/package-manager-cli.bin show-pkgs`, et relancer l'installation si un paquet manque.
- Le mot de passe sudo d'Ubuntu est saisi par l'utilisateur lui-même dans sa fenêtre Ubuntu, jamais dans le chat.

## Étapes suivantes

1. Vérifier les paquets Tizen (voir ci-dessus).
2. Activer le mode développeur sur la TV : Applications → `12345` → On → IP de ce PC Windows, puis éteindre complètement la TV et la rallumer.
3. Tester la connexion depuis WSL : `nc -zv <IP_TV> 26101`, puis `sdb connect <IP_TV>:26101`, puis `npm run tv:info`.
   - En réseau NAT, la TV voit l'IP du PC Windows, pas celle de WSL. Le mode réseau « mirrored » de WSL (fichier `.wslconfig`) peut simplifier la connexion.
   - Si WSL ne joint pas la TV, utiliser `sdb.exe` de Tizen Studio pour Windows, uniquement pour la connexion et l'installation.
4. Créer le certificat Samsung. C'est l'utilisateur qui se connecte à son compte Samsung. Le certificat distributeur doit inclure le DUID de la TV.
5. Créer l'identité de l'appli avec le SDK et la renseigner dans `.local/signing.json`, à partir de `signing.example.json`.
6. `npm run package` (dans WSL), puis `npm run tv:install` et `npm run tv:launch`, en annonçant chaque action.
7. Vérifier à l'écran avec l'utilisateur :
   - la photo de Marseille et les textes en français s'affichent bien ;
   - les flèches de la télécommande, OK et Retour fonctionnent ;
   - les applis se lancent ;
   - le bouton Entrées HDMI fonctionne.
8. Optionnel : Paramètres → Général → Fonctions Smart → activer « Exécution auto. de la dernière app » pour que CleanFrame se rouvre au démarrage.

## Règles

- Lire `AGENTS.md`, `docs/development.md` (section signature) et `docs/agent-tv-setup-guide.md`.
- Le script de signature est prévu pour Mac/Linux : l'utiliser depuis WSL.
- Demander l'accord avant la première connexion à la TV ; annoncer chaque installation et chaque lancement.
- **Ne pas** modifier l'accueil natif ni le démarrage de la TV : non testé sur ce modèle, et risqué.
- Garder certificats, mots de passe, DUID et adresses IP dans `.local/`, qui n'est jamais publié.

## Ce que CleanFrame ne fait pas

- Il ne rend pas la TV plus rapide.
- Il ne supprime pas les publicités dans Netflix, YouTube, Disney+ ou Prime Video.
- Il ne s'affiche pas tout seul au démarrage, sauf avec l'option « dernière appli ».
