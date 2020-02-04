# Composer patch
## Procédure
* Si le serveur est un serveur CHEOPS.
    * Avoir un compte CHEOPS
        * Si pas de compte CHEOPS, faire une demande à l'email : `Support-Cloud@maincare.fr`.
        * Renseigner dans les variables d'environnement :
            * %BASTIONCHEOPS_USER%
            * %BASTIONCHEOPS_PWD%

* Se rendre sur le serveur présent dans KeyPass via WinSCP.
* Dans `app/services/ideoportal`, ouvrir le fichier `BUILD` et copier son contenu dans Notepad++.
    * C'est le BUILD de départ et cela sera le premier argument de la commande `composer patch`.

* Pour la suite, il faut :
    * Que le projet soit à jour.
    * Avoir un `<tag/>` devant le dernier `<change>` du `change.log`
        * Si il n'y à pas de tag dans le `change.log`, alors il faut l'ajouter au dessus du dernier `<change>`
        * Pour ce faire, prendre le dernier tag et ajouter une version en fonction de l'importance du patch (majeur ou mineur)
            * Exemple : `<tag version="v1.2" date="2020-02-04" />`
    * Faire un `composer update`
    * Remplacer ce qui suit `project@` dans le BUILD local par le nom du tag 
        * Exemple : `[...]project@v1.2[...]`
    * Commit, push de ces modifs (généralement : `change.log` + `BUILD`).
        * Merge request vers la branche `dev-master`.
    * Une fois que la Merge Request à été acceptée et le change.log / BUILD ont la bonne valeur sur master, il faut créer le tag GIT.
    * Sur [GitLab](http://git.ido-in.com/), se positionner sur la branche `dev-master` et créer un nouveau tag (nouvelle étiquette - selon traduction).
        * Nom du tag : nom du dernier tag du `change.log` inséré plus haut.
        * Creer depuis : `master`
        * Créer le tag.
            * Se positionner sur le tag dans la liste des branche et controler le `BUILD` et le `change.log`
    * Récupérer le contenu du fichier `BUILD` sur le tag et le copier dans Notepad++.
        * C'est le BUILD d'arrivée et cela sera le deuxième argument de la commande `composer patch`.

* Le troisième argument de la commande `composer patch` est le chemin vers lequel le patch généré sera stocké.
    * Exemple : `D:/projets/patchs/[nom_projet][version]`

* Sur un CMD Git Bash ou via le CMD PHPStorm : 
    * Se positionner dans le projet
    * Lancer la commande : `composer patch "[build_depart]" "[build_arrivée]" "[chemin]"`; (avec des quotes pour éviter des potentiels problèmes d'interprétation)

* Vérifier le contenu du patch dans l'explorateur Windows au chemin configuré ci-avant.
<hr>

## Modifications à ajouter dans le patch
* Dans le patch généré, il faudra supprimer les dossiers suivants : 
    * `project/_src`
    * `project/changelog`
    * `project/common_private/configuration`
    * `ideoportal/common_private/configuration`
* Pour que la personne en charge de l'installation soit content(e), il faut : 
    * Copier le fichier `BUILD` à la racine du patch.
    * Créer un fichier sans extension indicatif avec le nom suivant (à la racine du patch) : 
        * `v [version_ideoportal_source] - [version_bureau_virtuel] - [tag_projet_livré]`
        * Exemple : `v4.4.2.5-2.1.1-1.4.2` (ideoportal/source : v4.4.2.5, bureau_virtuel : 2.1.1, tag_projet : 1.4.2)
<hr>

## Envoyer le patch
* Il faut zipper le patch en tgz (ou tar en fonction des préférences de la personne qui va installer le patch), pour cela :
    * Télécharger l'outil [Bandizip](https://fr.bandisoft.com/bandizip/) si ce n'est déja fait.
    * Sur l'explorateur Windows, dans votre dossier patch, sélectionner tous les dossiers/fichiers,
        * Clic-droit : Ajouter à l'archive Bandizip
        * Choisir le nom du fichier voulu
        * Type d'archive : TGZ (ou TAR)
        * Démarrer
* **Attention, il faut que le TGZ contienne directement les dossiers du patch racine (ideoportal/, library/, project/, [...]) et non un sous-dossier contenant les dossiers du patch.**

* Envoyer le patch par mail (ou tempo si le patch est trop lourd).
<hr>

## Erreurs potentielles
#### Tag non poussé
`error: pathspec 'v1.2.3' did not match any file(s) known to git
 fatal: ambiguous argument 'v1.2.3': unknown revision or path not in the working tree.
 Use '--' to separate paths from revisions, like this:
 'git <command> [<revision>...] -- [<file>...]'`
* Dans ce cas, il faut s'assurer que le tag `v1.2.3` soit bien poussé sur le projet Git.
    * Pour pousser le tag : 
        * Sur le projet, via le CMD de PHPStorm ou le CMD Git Bash : `git push origin [nom_tag]`, ici `git push origin v1.2.3`.
#### Tag pas au bon endroit (donc BUILD pas bon)
* Dans ce cas, il faut supprimer le tag existant sur le Git : `git push --delete origin [nom_tag]`
* Puis supprimer le tag existant en local : `git tag -d [nom_tag]`
* Puis le recréer au bon endroit (procédure détaillé ci-avant).