# Initiation bases de données : Les personnes célèbres

## Connexion au serveur

- URL : http://dbintro.fabricatic.fr
- Serveur : dbintro
- utilisateur : myuser
- mot de passe : pwd_myuser
- Base de données : dbintro

## Requête 1 :
Trouver la nationalité des personnes célèbres.
```sql
SELECT * 
FROM personne
JOIN pays ON personne.pays_id = pays.pays_id 



| personne_id | prenom   | nom      | genre_id | pays_id | pays_id | pays            |
--------------|----------|----------|----------|---------|---------|-----------------|
| 1           | Léonard  | De Vinci | 1        | 2       | 2       | Italie          |
| 2           | Ken      | Thompson | 1        | 4       | 4       | USA             |
| 3           | Brigitte | Bardot   | 2        | 1       | 1       | France          |
| 4           | James    | Bond     | 1        | 3       | 3       | Grande Bretagne |
| 6           | Johnny   | Depp     | 1        | 4       | 4       | USA             |
| 7           | Mark     | Twain    | 1        | 4       | 4       | USA             |

```


## Requête 2 :
Afficher le métier des personnes célèbres.

```sql
SELECT personne.personne_id, personne.prenom, personne.nom, personne_metier.*, metier.* 
FROM personne
JOIN personne_metier ON personne.personne_id = personne_metier.personne_id
JOIN metier ON personne_metier.metier_id = metier.metier_id
ORDER BY personne.personne_id

personne_id | prenom   | nom       | personne_id | metier_id | metier_id | metier 
1           | Léonard  | De Vinci  | 1           | 4         | 4         | Inventeur
1           | Léonard  | De Vinci  | 1           | 5         | 5         | Peintre
2           | Ken      | Thompson  | 2           | 1         | 1         | Informaticien
2           | Ken      | Thompson  | 2           | 4         | 4         | Inventeur
3           | Brigitte | Bardot    | 3           | 2         | 2         | Actrice / Acteur
4           | James    | Bond      | 4           | 3         | 3         | Espion
7           | Mark     | Twain     | 7           | 6         | 6         | Ecrivain
```

## Exercice 1 
Trouver les requêtes pour répondre aux questions suivantes

- Afficher le genre et la nationalité des personnes célèbres ?
```sql
SELECT personne.nom, personne.prenom, pays.pays, genre.genre
FROM personne
JOIN pays ON personne.pays_id = pays.pays_id
JOIN genre ON personne.genre_id = genre.genre_id
```

- Afficher les métiers de Léonard de vinci ?

- Quelle est la nationalité des inventeurs ?

- Quel est le métier des femmes ?

- Quel est le genre des italiens ?

## Exercice 2
Mise à jour des informations de la base. Vous devrez créer une nouvelle personne célèbre et mettre à jour ses informations.

- Choisissez une personne célèbre exerçant un metier inexistant dans la base de donnée
- Créer le nouveau métier

INSERT INTO `metier` (`metier_id`, `metier`) VALUES (NULL, 'Chevalier'); 

- Créer le nouveau pays si besoin
INSERT INTO pays(pays_id,pays) VALUES (NULL,'Espagne') 

- Créer la personne dans la table personne
- Créer le lien dans la table personne_metier
- Afficher les informations pour vérifier les données
- Modifier le libellé du métier
- Effaccer le métier. Est-ce possible de réaliser cette action ? Est-ce un problème ?
- Déterminer les liens dont la cohérence doit être conservée dans la base de donnée
