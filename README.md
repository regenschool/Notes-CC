# Saisie des notes de contrôle continu — Regen School

Page web autonome (un seul fichier `index.html`). Elle contient déjà les modules et les listes d'étudiants de chaque enseignant. L'enseignant ouvre son lien personnel, crée ses épreuves, saisit ses notes et télécharge un CSV. L'équipe pédagogique consolide ensuite tous les CSV reçus.

## Confidentialité

Les données (noms des étudiants, liens Notion) sont **chiffrées dans la page**, enseignant par enseignant. Chaque enseignant a son propre code d'accès, qui ne déchiffre que ses modules. Le dépôt peut donc être public : sans code, la page ne révèle aucun nom.

Dans le lien personnel, le code se trouve après le `#`. Cette partie de l'adresse n'est jamais transmise à GitHub.

Le fichier `Codes_acces_enseignants.xlsx` est confidentiel : ne le déposez jamais dans le dépôt.

## Mise en ligne sur GitHub Pages

1. Sur github.com, créez un dépôt (par exemple `notes-cc`).
2. Cliquez sur **Add file › Upload files**, déposez `index.html` et ce `README.md`, puis **Commit changes**.
3. Ouvrez **Settings › Pages**. Dans « Build and deployment », choisissez **Deploy from a branch**, branche `main`, dossier `/ (root)`, puis **Save**.
4. Après une à deux minutes, la page est disponible à l'adresse `https://<votre-compte>.github.io/notes-cc/`.
5. Dans `Codes_acces_enseignants.xlsx`, remplacez l'adresse de la cellule B2 par celle de votre page : les 45 liens personnels se mettent à jour.

## Déroulé

1. **Envoi.** Chaque enseignant reçoit son lien personnel (colonne E du fichier des codes).
2. **Saisie.** En ouvrant son lien, il voit ses modules et ses étudiants. Il crée ses épreuves (intitulé et pondération, total de 100 % par module), puis saisit les notes sur 20. La virgule est acceptée, et « ABS » ou « ABJ » indiquent une absence. Il peut aussi ajouter un commentaire.
3. **Reprise.** La saisie est sauvegardée automatiquement dans son navigateur : en rouvrant son lien, il retrouve son travail. Pour changer d'ordinateur, il peut recharger le CSV qu'il a exporté.
4. **Retour.** Il clique sur « Télécharger mon CSV » et vous l'envoie.
5. **Consolidation.** Dans l'onglet « Consolidation », déposez tous les CSV reçus pour obtenir un fichier unique. Les doublons sont écartés et les notes vides sont signalées.

## Mettre à jour les listes (nouvel étudiant, nouveau module…)

Modifiez le classeur Excel source, puis régénérez la page :

```
pip install openpyxl cryptography
python3 outils/build_data.py Notes_CC_Regen_School_2026-2027.xlsx index.html Codes_acces_enseignants.xlsx
```

Les codes déjà attribués sont conservés ; seuls les nouveaux enseignants reçoivent un code. Déposez ensuite le nouvel `index.html` sur GitHub. Les saisies déjà faites par les enseignants sont conservées dans leur navigateur.

## Format du CSV

Séparateur `;`, encodage UTF-8 (avec BOM, pour une ouverture directe dans Excel en français), une ligne par étudiant et par épreuve :

`Enseignant ; Intitulé du module ; Promotion ; Campus ; Semestre ; Code module ; Étudiant ; Lien espace Notion ; Intitulé de l'épreuve ; Note ; Pondération dans la moyenne (%) ; Commentaire`
