	
  
  
  - Git is a local open source VCS software :  version control system designed to handle everything from small to very large projects with speed and efficiency: enables to save snapshats of the prjects over time
	- Github a web based platform that incorparates Git version features / social coding ..(heberger git)
	- Github is a Microsoft platform 
	- With Git : Gitlab another platform : store code and use Github : same as github but no need for pull request (and other differences..)
	- Fork/pull/merge
	- Repo : file location 
	- Commit: command used to save nexw changes 
	- Stage : preperation step before commit 
	- Branch: the part of the project i'm changing 


Gestion de version 
	- en local (serveur) avec une base de données et des métadonnées (pour les infos de modification)
	- Centralisée (ex: cvs,svn..) : avec un depot serveur 
	- Dstribuées (ex: Git, Mercurial, Bazaar..): un depot serveur , recuperation de l'integration de l'historique en integral: l'historique est l'ensemble des commits faits 
	
	
- pwd: chemin du dossier dans leqquel je me trouve 
      	- mkdir: mkdir nom_dossier: créer un dossier 
      	- ls: lister les fichiers existants: ls nom_dossiers/
      	- cd nom_dossiers/ : se mettre dans le dossier 
      	- cd .. : se deplacer un dossier en arriere 
      	- cd chemin_complet/ 
      
      	- Git congig --global user.name".." 
      	Git congig --global user.email".." : configuration d'une facon global (nom+adreese mail une seule fois pour toujours )
      	- Git config --global --list: Pour lister la configuration de git
      	- Git est equivalent à git --help

	
	- Git status : l'etat actuel de l'evennement de travail : detection de fichiers ajoutés et pas encore commités ou autre genre de soucis ..
	- Git add .. : indexation 
	- Git add . : indexation totale ( de tous les fichiers)
	- Git reset ..: annuler l'indexation du fichier concerné
	- Git commit -m "message": un commit attaché d'un message 
	- Git diff: presente toutes les modifications presentes dans le workspace par rapport a la derniere version enregistrée dans le depo
	- Les modifications dans git sont toujours considées comme des ajouts /suppressions de lignes ( + ou -)
	
	
	- Acceder en SSH au repo github : pour pouvoir clone en SSH 
	
	
	- Contribuer à un projet OpenSource: 
	- 
	
	- 

	- Git switch -c nom_branche : creation d'une branche 
	
	- Lien direct depot local -depot publique du proprietaire open source: 
	Git remote add nom_proprio url_prrio
	- Git remote -vvv: regarder les remotes qui existent (avec leur destination: proprio ou origin avec les operations (fetch, push..)
	- Pour mettre a jour le repo en local par rapport au repo du proprietaire : 
	git pull nom_remote nom_branche
	Puis : git push nom_remote(origin) nom_branche(master) pour mettre a jour mon repo perso
	
	
	- Git Bash / Git GUI
	
	
- A that shows some basic commands of git is provided in the doc folder : http://codeur-pro.fr/cadeau-formation-git/
  





