# Pràctica Git: Control de Versions i Gestió de Branques

## Introducció
En aquesta pràctica treballarem en profunditat el control de versions amb Git, centrant-nos en la gestió de branques, la resolució de conflictes i el flux de treball Git Flow. Analitzarem com es comporten els fitxers en moure'ns entre branques i simularem un entorn col·laboratiu utilitzant GitHub.

## Part 1: Treball amb Branques

### 1. Creació de branques i persistència de fitxers

#### Accions realitzades:
- Creació del repositori i branca master amb README.md.
- Creació de fitxerMain1.txt i fitxerMain2.txt.
- Creació de la branca develop abans de fer el commit.

#### Estat inicial a master:
```bash
$ git init
$ touch README.md
$ git add README.md
$ git commit -m "Commit inicial main"
```

**Nota:** El repositori s'ha inicialitzat amb la branca `master` per defecte (no `main`).

#### Creació de fitxers i canvi a develop:
Creem els fitxers sense fer commit i canviem de branca.
```bash
$ touch fitxerMain1.txt fitxerMain2.txt
$ git branch develop
$ git checkout develop
```

#### Comprovació:
```bash
$ ls
README.md  fitxerMain1.txt  fitxerMain2.txt

$ git status -s
?? fitxerMain1.txt
?? fitxerMain2.txt
```

**Preguntes:**
- **Apareixen els fitxers a la branca develop?** Sí, apareixen.
- **Podem identificar si han estat creats des de la branca master?** No directament des de l'historial de Git en aquest punt. Com que els fitxers encara no estan "trackejats" (estan en estat Untracked), no pertanyen a cap branca específica encara; resideixen al directori de treball (Working Directory). Per tant, quan canviem de branca, "viatgen" amb nosaltres perquè Git no els ha de netejar per canviar de context.

#### Commit a develop i tornada a master:
Fem `git add .` i `git commit` a la branca develop. Després tornem a master.
```bash
$ git add .
$ git commit -m "Afegits fitxers txt a develop"
$ git checkout master

$ ls
README.md
# Els fitxers fitxerMain1.txt i fitxerMain2.txt han desaparegut
```

**Preguntes:**
- **Apareixen els fitxers a master?** No, no apareixen.
- **Què ha passat?** Un cop hem fet commit a develop, els fitxers han passat a formar part de la història d'aquesta branca. Com que la branca master no té aquests canvis registrats en el seu historial, quan fem checkout master, Git restaura el directori de treball a l'estat de l'últim commit de master, eliminant els fitxers que ara només existeixen a develop.

### 2. Desfer commit i commit a master

#### Accions realitzades:
- Desfer l'últim commit de develop per treure els fitxers del commit.
- Fer commit dels fitxers a master.
- Tornar a develop i verificar si apareixen els fitxers.

#### Desfer commit a develop:
```bash
$ git checkout develop
$ git log --oneline
22bb25d Afegits fitxers txt a develop
abfb694 Commit inicial main

$ git reset abfb694
$ git status -s
 M README.md
?? fitxerMain1.txt
?? fitxerMain2.txt

$ git log --oneline
abfb694 Commit inicial main
```

El commit s'ha desfet, però els fitxers romanen al directori de treball en estat "untracked" (??).

#### Commit a master:
```bash
$ git checkout master
$ git add fitxerMain1.txt fitxerMain2.txt
$ git commit -m "Afegits fitxers Main a master"
$ git log --oneline
86e2ba1 Afegits fitxers Main a master
abfb694 Commit inicial main

$ ls
README.md  fitxerMain1.txt  fitxerMain2.txt
```

#### Tornada a develop:
```bash
$ git checkout develop
$ ls
README.md

$ git status -s
 M README.md

$ git log --oneline
abfb694 Commit inicial main
```

**Preguntes:**
- **Apareixen ara aquests fitxers a develop?** No, no apareixen.
- **Què ha passat?** Quan hem fet commit dels fitxers a master, aquests han passat a formar part de la història de master. La branca develop, que només té el commit inicial, no conté aquests fitxers al seu historial. Quan fem checkout a develop, Git restaura el directori de treball a l'estat del darrer commit de develop (el commit inicial), que no inclou aquests fitxers. Això demostra que cada branca manté el seu propi historial independent i que els fitxers només apareixen en les branques on s'han fet commit.

### 3. Eliminar i recrear la branca develop

#### Accions realitzades:
- Eliminar la branca develop.
- Recrear la branca develop des de master.
- Verificar si apareixen els fitxers a la nova branca develop.

#### Eliminació de develop:
```bash
$ git checkout master
$ git branch
* master
  develop

$ git branch -d develop
Deleted branch develop (was abfb694).
```

#### Recreació de develop:
```bash
$ git branch develop
$ git checkout develop
$ ls
README.md  fitxerMain1.txt  fitxerMain2.txt

$ git status -s
 M README.md

$ git log --oneline
86e2ba1 Afegits fitxers Main a master
abfb694 Commit inicial main
```

**Preguntes:**
- **Hi apareixen ara els fitxers?** Sí, apareixen.
- **Per què?** Quan eliminem i recreem la branca develop des de master, la nova branca develop es crea des del punt actual de master. Com que master ja conté els fitxers fitxerMain1.txt i fitxerMain2.txt en el seu historial (commit 86e2ba1), la nova branca develop hereta aquest historial complet. Per tant, develop ara comparteix el mateix historial que master fins al punt on es va crear, incloent tots els commits i fitxers que hi ha en master. Això demostra que quan crees una branca nova, aquesta comença des del commit actual de la branca des de la qual la crees.

