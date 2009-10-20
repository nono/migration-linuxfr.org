Migration des données
=====================

Contexte
--------

Le site LinuxFr.org tourne actuellement avec le moteur templeet, mais une réécriture en Rails arrive. Il va donc falloir migrer les données de l'un vers l'autre, et ce n'est pas un mince boulot.


Le schéma Templeet
------------------

Légende :
 - **?** : je ne sais pas encore
 - X : ces données ne seront pas importées car elles sont considérées comme inutiles
 - O : ces données seront importées

Les tables :
 - X `avis` : les votes des relecteurs/modérateurs sur les dépêches. Effet : perte de statistiques de modération (nombre d'avis des modérateurs, liste des anciens modérateurs, etc.)
 - X `board` : les messages dans les tribunes ont une durée de vie limitée, et ne seront pas importés.
 - X `board_denied` : il y a actuellement 19 interdictions de tribune. Amnistie.
 - O `comments` : attention aux contenus masqués par raison légale ou par choix de l'auteur
 - O `comments_scored_user` : les pertinents/inutiles sur les commentaires. Données de 2004 à 2009.
 - X `commentsnew` : table obsolète y compris pour l'ancienne version...
 - O `contents_scored` : les pertinents/inutiles sur les contenus
 - O `forums_data` : à importer dans `posts`
 - O `forums_group` : à importer dans `forums`
 - O `interest` : à importer dans `nodes`
 - O `interviews` : à importer dans `interviews`
 - O `lang` : liste des drapeaux pour les langues des documents pointés par les URL
 - X `locks`
 - X `messages` : pour le moment, nous n'avons pas de messagerie, et de toute façon, les messages ont une durée de vie limitée. Prévoir de les envoyer par courriel aux concernés avant suppression.
 - **?** `moderator_last` : à importer pour les stats (savoir qui a été modérateur dans le passé) ?
 - O `news` : il faut bien entendu importer les dépêches. Acceptées/rejetées/etc.
 - O `news_attachements` : les secondes parties des dépêches
 - O `news_author` : les auteurs de dépêches (pas toujours authentifiés)
 - **?** `news_history` : est-ce utile d'importer les modifications sur les dépêches ? Légalement ça pourrait être utile...
 - O `news_moderated` : on importe seulement une modération par dépêche
 - O `news_urls` : les liens des dépêches
 - O `poll_answers` : les propositions de réponse aux sondages
 - X `poll_ip` : cette table est de toute façon vidée une fois par nuit
 - O `poll_questions` : les questions des sondages
 - X `prizes` : ce sont uniquement les prix du mois courant, et il n'y a rien de prévu pour utiliser ces infos sur le nouveau site. Et on n'en sert plus ?
 - O `sections` : les sections des dépêches
 - X `sessions` : on ne pourra rien en faire
 - X `sig` : visiblement, cette table ne sert pas à grand chose. Les signatures ont été transférées dans la table users...
 - X `tag` : cette table ne servait pas
 - X `tagmap` : idem
 - X `tagvote` : idem
 - O `topics` : les sujets des dépêches
 - **?** `tracker` : est-ce que l'on purge le tracker ? Les entrées du suivi ne seront plus valides, mais souhaite-t-on conserver un historique ? Traitement manuel pour garder les bonnées idées ?
 - X `tracker_category` : on va créer des catégories plus en adéquation avec la nouvelle version
 - X `tribune` : quelle différence par rapport à la table `board` ? La tribune d'origine vs les tribunes de modération, d'AMR, etc.
 - X `urls_avgtime` : stats pour les admins uniquement `->` plus valide après la migration
 - O `users` : les utilisateurs ne vont pas être contents si on n'importe pas leurs comptes (attention au mot de passe)
 - X `users_boxes` : ça n'existe plus sur le nouveau site
 - O `users_journal` : les journaux
 - X `users_journal_recent` : ça n'a l'air d'être qu'un extrait de `users_journal`
 - **?** `users_karma` : on n'a pas encore de karma sur le nouveau site
 - **?** `users_karma_archive` : idem
 - X `users_lastseen` : la date de dernière visite d'un contenu `->` on purge
 - **?** `users_personnalboxes` : ça n'existe plus sur le nouveau site, mais il faudrait peut être fournir un moyen aux utilisateurs de récupérer ça
 - X `users_rdf`
 - X `users_tempo` : table temporaire pour les nouveaux inscrits / changement de mot de passe


En pratique
-----------

    $ gem install stephencelis-dots sequel

    $ cp config.yml{.sample,} && vim config.yml

    $ mysql -u root
    mysql> CREATE DATABASE linuxfr_templeet;
    mysql> use linuxfr_templeet;
    mysql> source linuxfr-daily.dump;
    (attendre un bon moment)

    $ ./import.rb

    $ rake friendly_id:make_slugs


Autres / TODO
-------------

 - Penser à créer les redirections pour les pages HTML
 - Importer les anciens mots de passe hashés

