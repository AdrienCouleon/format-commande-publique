### Scripts de transformation des données envoyées par la DGFiP

**transformation.sh** : Un script bash qui orchestre :

    1. l'extraction des ZIPs mis à disposition par la DGFiP
    2. l'archivage des ZIPs et des XML
    1. le réencodage des fichiers XML d'ISO-8859-1 vers UTF-8
    3. la transformation des fichiers XML vers une structure valide
    3. le découpage des données par SIRET
    4. la validation des fichiers XML produits
    2. l'extraction et l'archivage des URL de profils d'acheteurs

**decpDGFIP.xslt** : Une feuille de style XSLT qui met en oeuvre les étapes 4 et 5 ci-dessus

### Paramétrage

Le script **transformation.sh** prend en paramètre (`$1`) le chemin vers le dossier contenant les archives ZIP du jour.

Un fichier `configTransformation.sh` doit être créé dans le même dossier pour renseigner les variables suivantes :

- `saxonJar` : chemin vers le JAR Saxon (à télécharger [ici](https://sourceforge.net/projects/saxon/files/Saxon-HE/9.9/SaxonHE9-9-0-1J.zip/download), extraire `saxon9he.jar`)
- `validatorJar` : chemin vers le JAR de xsd11-validator (à télécharger [ici](https://www.dropbox.com/s/939jv39ihnluem0/xsd11-validator.jar?dl=1))
- `schemasDir` : chemin vers le dossier qui contient les schémas XML
- `xsltDir` : chemin vers le dossier qui contient les feuilles de style XSLT (`decpDGFIP.xsl` et `merge.xsl`)

### Hiérarchie de dossiers produite par `transformation.sh`

Entre parenthèses, les fichiers contenus dans le dossier. Cette hiérarchie est produite dans le même dossier que le chemin passé en paramètre de `transformation.sh`.

- sourceXML (les XML du jour, en lecture seule)
- sourceZIP (les ZIP du jour, en lecture seule)
- sirets (un fichier tabulaire par SIRET repéré dans le fichier)
- sortieXML
    - *siret acheteur* (XML format données essentielles publié)
- `profilsAcheteurs.csv` (un CSV listant les URL de profils d'acheteur extraites lors du traitement)

### Modifications apportées aux les fichiers XML reçus de la DGFiP

Les modifications apportées aux XML reçus sont les suivantes :

- les éléments XML se voient retirer l'espace de nom (namespace) renseigné par la DGFiP (http://data.gouv.fr/marches/etalab)
- l'élément `dateSignatureModification` est remplacé par `dateNotificationModification`, conformément à [l'article 1er](https://www.legifrance.gouv.fr/affichTexteArticle.do;jsessionid=827F7B40490885361A36CF0736F37AA4.tplgfr29s_3?idArticle=JORFARTI000037283011&cidTexte=JORFTEXT000037282994&dateTexte=29990101&categorieLien=id) de l'arrêté modificatif du 27 juillet 2018
- les éléments suivants sont supprimés
    - `dateTransmissionDonneesEtalab`
    - `urlProfilAcheteur`
- l'élément `uid` est ajouté, concaténant le SIRET de l'acheteur et l'ID du marché