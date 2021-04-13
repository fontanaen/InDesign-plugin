
# Documentation ADOBE InDesign scripts [BROUILLON]


Plugin visant à simplifier l'importation de produit du module XML Data Provider d'EasyCatalog dans InDesign + documentation technique d'ADOBE InDesign scripts.

## Introduction
### Informations
 - Logiciel : ADOBE InDesign 2020 
 - Plugins : EasyCatalog XML Data Provider, Scripting 
 - Langage : ExtendScripts (.jsx mais version ADOBE)

### Exécution des scripts
Pour exécuter un script ADOBE dans InDesign, il faut se rendre dans :
> **Fenêtre > Utilitaires > Scripts**

Un panneau va s'ouvrir avec à l'intérieur deux dossiers :
- Application
- Utilisateur

D'ici vous pourrez exécuter vos scripts mais **attention**, les changements apportés par ces scripts seront perdus lorsque l'application s'arrête. Par exemple dans le cas d'un menu personnalisé.
Cela s'explique car les dossiers proposés ne sont pas exécutés au démarrage, il faut placer vos scripts dans un autre dossier pour cela.

**Windows.**
En cliquant droit sur le dossier application, vous pourrez le visualiser dans l'explorateur.

> C:\Program Files\Adobe\Adobe InDesign 2020\Scripts\Scripts Panel\Samples\JavaScript

En remontant dans les dossiers vous devez trouver ce chemin (pour moi dans l'exemple suivant) et chercher le dossier  `startup scripts` 
> C:\Program Files\Adobe\Adobe InDesign 2020\Scripts\startup scripts\

Tout les scripts placés ici seront exécuté au **démarrage** de l'application.

### Importer des fichiers .jsx
Une syntaxe propre à InDesign est necéssaire pour importer des fichiers dans InDesign :

    //@include "filename.jsx"

Ou bien
	

    // Créer le chemin vers le fichier
    var file = Folder(app.activeScript.path); // Chemin du fichier actuel
	file = myFolder.parent + '/Scripts Panel/MyTest1.jsx'; // Chemin du dossier parent
    		
	// Liaison vers le script
	var scriptfunction = function(){
		app.doScript(File(file));
	};

### Ouvrir l'explorateur de fichier

Pour récupérer un fichier ou le contenu d'un dossier :

    const getFolder = Folder.selectDialog ("Choose the Source folder")
	const getFile = File.openDialog("Choose Data File (Excel or csv)")

## Session / Moteur

`#targetengine` est spécifique aux scripts Adobe dans InDesign, PhotoShop, Illustrator, etc. - ce n'est pas une fonctionnalité Javascript générale.

Il spécifie comment gérer tous les «trucs» globaux - non seulement les variables mais aussi les déclarations de fonctions et tout autre changement du statut global.

Si vous utilisez le moteur «principal» par défaut, tous les globaux disparaissent dès que le script se termine. Si vous utilisez le moteur de «session», tous les globaux sont conservés tant que l'application hôte continue de fonctionner. Cela signifie que si vous exécutez le script:

```
// S'affiche comme étant une erreur mais ce n'est pas le cas
#targetengine "session" 

// Autre syntaxe qui ne s'affichage pas en erreur
//@targetengine "session" 

var test = "test";

```
puis exécutez le script:
```
#targetengine "session"

alert(test);
```
vous obtenez une boîte de message qui s'affiche `test`au lieu de donner une erreur

Outre les deux moteurs standard 'main' et 'session', vous pouvez créer les vôtres, avec des noms arbitraires - donc si vous exécutez le script

```
#targetengine "mine"

var test = "another test";
```
puis exécutez
```
#targetengine "mine"

alert(test);
```
vous obtenez une boîte de message `another test`, mais si vous exécutez à nouveau
```
#targetengine "session"

alert(test);
```
vous obtenez toujours `test`: il y a deux variables globales 'test' différentes, une dans le moteur 'session' et une dans le (nouvellement créé) 'mine' one.

Ce concept est important pour les scripts utilisant des **eventListeners**
  
## Source de données (EasyCatalog - XML)
### Créer une source de données
L'importation d'une source de données locale comme distante est possible.

	// Dans le cas d'une source XML distante
    var myEC = app.easycatalogObject;

	// On spécifie le nom et le type de données
    var myDS = myEC.datasources.add(name, "XML");

	// dataSourceSpecifier = ["File:" ou "URL:"] + "URL/Chemin" 
    myDS.dataSourceSpecifier = "URL:http://www.w3schools.com/xml/plant_catalog.xml";

### Configurer les champs
### Synchroniser avec la source de données
### Supprimer une source de données 

## Menus


### Menus principaux

Les menus principaux ce situent en haut de l'application avec notamment *Fichier*, *Edition* ..
``` 
// Contient le menu principal
var MainMenu = app.menus.item("$ID/Main"); 

// Pour obtenir ses sous menus
var Submenus = MainMenu.submenus 
```

### Sous menus
Les sous menus permettent de rassembler plusieurs menus et les affichent sous forme de liste déroulante

```
// Obtenir des sous menus
var SubMenus = parentMenu.submenus

// Obtenir un sous menu spécifique
var Menu = SubMenus.item("nom")

// Ajouter
Submenus.add("nouveau sous menu")

// Supprimer
Submenus.item("Nom du menu à supprimer").remove()

// Vérifier l'existence
Submenus.item("Nom").isValid()

> true // Si le sous menu existe
> false // Si le sous menu n'existe pas
```

### Menu d'actions

Les menus d'actions comme leurs nom l'indique, réalisent des actions comme lancer d'autres scripts.

Il faut donc créer des scripts en amont et les lier au menu.

    // Créer le chemin vers le menu
    var myFolder = Folder(app.activeScript.path); // Chemin du fichier actuel
	myFolder = myFolder.parent + '/Scripts Panel/'; // Chemin du fichier parent
	
	// Liaison vers le script
	var menuItem1Handler = function(/*onInvoke*/){
		app.doScript(File(myFolder + 'MyTest1.jsx'));
	};
	
	// Création du menu d'action et configuration du déclenchement, ici au clic
	subMenu1 = app.scriptMenuActions.add("Nom");
	subMenu1.eventListeners.add("onInvoke", menuItem1Handler);

	// Ajout du menu d'action dans un sous menu
	var menu = app.menus.item("$ID/Main").submenus.item("Nom")
	menu.menuItems.add(subMenu1);
	
	

### Exemple de menu personnalisé

    var  myFolder  =  Folder(app.activeScript.path);
	myFolder  =  myFolder.parent  +  '/Scripts Panel/';

	var  menuItem1Handler  =  function(/*onInvoke*/){
		app.doScript(File(myFolder  +  'MyTest1.jsx'));
	};

	var  menuItem2Handler  =  function(/*onInvoke*/){
		app.doScript(File(myFolder  +  'MyTest2.jsx'));
	};
	  
	var  menuItem3Handler  =  function(/*onInvoke*/){
		app.doScript(File(myFolder  +  'MyTest3.jsx'));
	};
	
    var  menuInstaller = menuInstaller||(function(){
		var menuItem1T = "My Menu Item 1",
			menuItem2T = "My Menu Item 2",
			menuItem3T = "My Menu Item 3",
			menuT = "MyTestMenu",
			menu,
			subs = app.menus.item("$ID/Main").submenus;
		var refItem = app.menus.item("$ID/Main").submenus.item("$ID/&Layout");
		
		subMenu1 = app.scriptMenuActions.add(menuItem1T);
		subMenu1.eventListeners.add("onInvoke", menuItem1Handler);

		subMenu2 = app.scriptMenuActions.add(menuItem2T);
		subMenu2.eventListeners.add("onInvoke", menuItem2Handler);
		
		subMenu3 = app.scriptMenuActions.add(menuItem3T);
		subMenu3.eventListeners.add("onInvoke", menuItem3Handler);

		menu = subs.item(menuT);
		
		// Vérifie si le menu existe 
		if( !mnu.isValid ) mnu = subs.add(menuT, LocationOptions.after, refItem);

		menu.submenus.add("subMenu1");
		menu.menuItems.add(subMenu2);
		menu.menuSeparators.add(); // Sépare avec une ligne
		menu.menuItems.add(subMenu3);

		return true;
	})();

## Boîte de dialogue
La mise en forme d'une boîte de dialogue est caractérisé par de l'imbrication d'éléments, le dialogue, le panel et le groupe sont les trois éléments pouvant contenir d'autres éléments.

Le site [suivant](https://scriptui.joonas.me/) permet de mettre en forme (juste en forme !) des boîtes de dialogue personnalisées. 

### Dialogue

Le dialogue est l'élément principal à afficher, la fenêtre en elle même.

On l'instancie de la façon suivante ; 

    const dialog  =  new  Window("dialog");
		  dialog.text  =  "Configuration de l'importation des produits"; // Titre
		  dialog.preferredSize.width  =  700; // Largeur
		  dialog.preferredSize.height  =  350; // Hauteur
	      dialog.alignChildren  = [
				"center", /* X : left, right, center, fill */
				"top" /* Y :  top, center, bottom */
		  ];
		  dialog.spacing  =  10; // padding
		  dialog.margins  =  16; // margin

### Panel

    const  panel1  =  dialog.add(
							  "panel", // Type
							  undefined, // Taille fixe [0,0,0 /* width */,0 /* height */] 
							  undefined, //
							  {name: "panel1"}
							  );
	       panel1.text  =  "Général"; // Titre
		   panel1.preferredSize.width  =  650; // Largeur suggérée
		   panel1.orientation  =  "column"; // column ou row
		   panel1.alignChildren  = ["left","top"]; // Alignement
		   panel1.spacing  =  10; // padding
		   panel1.margins  =  10; // margin

### Groupes

    const  group  =  panel.add("group", undefined, {name: "group"});
		   group5.orientation  =  "column"; // column ou row
		   group5.alignChildren  = ["left","center"];
		   group5.spacing  =  10;
		   group5.margins  =  0;

### Text static

    const  statictext2  =  group.add("statictext", undefined, undefined, {name: "statictext"});
		   statictext2.text  =  "Exemple de texte";

###  Zone de texte

    var  edittext =  group.add('edittext {properties: {name: "edittext"}}');
    edittext.preferredSize.width  =  630;

### Images
Pour importer des images, le plus simple est d'utiliser le lien donné précédemment et de créer un élément image en important une image spécifique. En faisant exporter, vous pourrez constater dans le code que l'image à été transformé en chaîne de caractère, il ne vous reste plus qu'à copier coller.  

    const  image1_imgString  =  "%C2%89PNG%0D%0A%1A%0A%00%00%00%0DIHDR%00%00% ...;
    const  image1  =  group1.add("image", undefined, File.decode(image1_imgString), {name: "image1"});

### Listbox

    const items = ["item1", "item2", "item3"]
    const  listboxInsert  =  group5.add(
		    "listbox", // Type
		    [0,0,630,300], // Taille
		    items , // Données
		    {name: "listbox2", multiselect: true} // Propriétés
		    );

## Panneaux
