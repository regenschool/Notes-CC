# Saisie des notes de contrôle continu — Regen School (option A)

Page web (`index.html`, hébergée sur GitHub Pages) qui sert d'interface enseignante au classeur
**Notes_CC_Regen_School_2026-2027 - Automate.xlsx**. Elle écrit directement dans les tableaux Excel de ce classeur,
au format attendu par le script `ExtraireEvaluationCompletes`. **Le flux Power Automate n'est pas modifié.**

## Ce que fait la page

1. **Connexion.** L'enseignant se connecte avec son compte Microsoft Regen School. Son onglet s'ouvre automatiquement s'il arrive par son lien personnel ; sinon, il choisit son nom.
2. **Lecture.** La page lit les tableaux Excel de son onglet : modules, étudiants, ID de base Notion, et notes déjà soumises.
3. **Saisie.** Il crée ses épreuves (intitulé, type parmi les cinq valeurs du classeur, pondération), puis saisit les notes sur 20. Sa saisie est conservée dans son navigateur tant qu'elle n'est pas soumise.
4. **Soumission.** Il soumet après une confirmation. Chaque note remplit une ligne vide préremplie de l'étudiant ; s'il n'en reste plus, une ligne est ajoutée au tableau.
5. **Verrouillage.** Une note soumise est **définitive** : la page ne modifie jamais une ligne qui contient déjà une note. L'enseignant peut seulement consulter ses notes soumises, compléter les étudiants manquants ou ajouter de nouvelles épreuves.

L'onglet **Administration** lit tous les onglets et exporte les notes soumises en CSV, au complet ou par enseignant et par classe.

## Ce qui est écrit dans le classeur

| Colonne Excel | Valeur écrite |
|---|---|
| Intitulé de l’épreuve | intitulé saisi |
| Type d’épreuve | Ecrit individuel, Ecrit collectif, Oral individuel, Oral collectif ou Autre |
| Note | nombre de 0 à 20 (14,5 enregistré en 14.5) |
| Pondération dans la moyenne (%) | fraction : 40 % est enregistré **0,4** |
| Commentaire | texte facultatif |

Les colonnes Étudiant, Lien espace Notion, ID base Notes Notion, Nom du module et Enseignant ne sont jamais modifiées. Elles sont seulement recopiées quand une ligne doit être ajoutée.

**Absences.** Le script ignorant toute valeur non numérique, la page n'accepte que des nombres. Pour une absence, l'enseignant laisse la case vide.

## Limites connues (acceptées pour cette première phase)

- **Accès au classeur entier.** Pour que la page puisse écrire, chaque enseignant doit avoir le classeur partagé en modification. Il pourrait donc l'ouvrir directement dans Excel et y voir les autres onglets.
- **Corrections hors de la page.** Une correction faite directement dans Excel créerait un doublon dans Notion au lancement suivant du flux. Les corrections restent du ressort de l'équipe pédagogique.
- **Structure du classeur à préserver.** Ne renommez pas les colonnes et ne transformez pas les tableaux Excel en simples plages : le script et la page s'appuient dessus.

---

## Mise en place

### Partie 1 — Partager le classeur (5 minutes)

1. Dans OneDrive, sélectionnez **Notes_CC_Regen_School_2026-2027 - Automate.xlsx** › **Partager**.
2. Choisissez **Personnes spécifiques**, ajoutez les enseignants (ou le groupe « Enseignants CC ») avec le droit **Peut modifier**, puis décochez l'envoi d'un e-mail si vous ne souhaitez pas les prévenir maintenant.
3. Ne déplacez pas le fichier vers un autre OneDrive ou un autre site : la page le retrouve par son identifiant. Le renommer ou le déplacer dans votre OneDrive ne pose pas de problème.

### Partie 2 — Inscrire l'application dans Entra ID (10 minutes)

1. Allez sur **entra.microsoft.com › Applications › Inscriptions d'applications › Nouvelle inscription**.
   - Nom : `Notes CC`
   - Types de comptes : **Comptes dans cet annuaire d'organisation uniquement**
   - URI de redirection : plateforme **Application monopage (SPA)**, adresse `https://VOTRE-COMPTE.github.io/notes-cc/`
   - Cliquez sur **Inscrire**.
2. Dans **Authentification**, ajoutez une seconde URI SPA : `https://VOTRE-COMPTE.github.io/notes-cc/index.html`.
3. Sur la page **Vue d'ensemble**, copiez l'**ID d'application (client)** et l'**ID de l'annuaire (locataire)**.
4. Dans **Autorisations d'API › Ajouter une autorisation › Microsoft Graph › Autorisations déléguées**, cochez **Files.ReadWrite.All**, puis cliquez sur **Ajouter des autorisations**. User.Read est déjà présente.
5. Cliquez sur **Accorder un consentement d'administrateur pour Regen School**.

Pourquoi « déléguée » : la page agit avec les droits de la personne connectée, jamais au-delà. Elle ne peut donc atteindre que les fichiers que cette personne peut déjà ouvrir. L'autorisation « .All » est nécessaire pour écrire dans un fichier partagé par quelqu'un d'autre.

### Partie 3 — Configurer la page

1. Sur GitHub, ouvrez `index.html`, cliquez sur le crayon (Edit).
2. En haut du fichier, renseignez `tenantId` et `clientId`. Les champs `driveId` et `itemId`, qui désignent le classeur, sont déjà remplis.
3. Cliquez sur **Commit changes**.

### Partie 4 — Tester avant d'envoyer les liens

1. Ouvrez la page, connectez-vous, choisissez un onglet de test (par exemple le vôtre), créez une épreuve « TEST » et soumettez une note.
2. Vérifiez dans Excel pour le web que la ligne est remplie, avec la pondération affichée en pourcentage.
3. Lancez le flux Power Automate et vérifiez la création dans la base Notion de l'étudiant.
4. Supprimez la ligne de test dans Excel et la page de test dans Notion.

---

## Liens personnels

`Codes_acces_enseignants.xlsx` (confidentiel, à ne pas déposer sur GitHub) contient un lien par enseignant : il ouvre directement son onglet après la connexion. Sans lien, l'enseignant choisit son nom dans une liste.
