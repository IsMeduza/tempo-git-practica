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

## Part 2: Funcionament Git Flow

### 1. Afegir fitxers a develop i crear branca feature

#### Accions realitzades:
- Afegir fitxers fitDev1.txt i fitDev2.txt a develop.
- Crear branca de funcionalitat feature-MenuLanding.
- Afegir index.html a la feature i fer commit.

#### Afegir fitxers a develop:
```bash
$ git checkout develop
$ git add README.md
$ git commit -m "Documentació Part 1 completada"

$ touch fitDev1.txt fitDev2.txt
$ git add fitDev1.txt fitDev2.txt
$ git commit -m "Afegits fitxers de desenvolupament"
```

#### Crear branca feature i afegir index.html:
```bash
$ git checkout -b feature-MenuLanding
$ touch index.html
$ git add index.html
$ git commit -m "Afegit index.html a feature MenuLanding"
```

#### Comprovació:
```bash
$ git log --oneline
f735b75 Afegit index.html a feature MenuLanding
7ea98cb Afegits fitxers de desenvolupament
824eb0c Documentació Part 1 completada
86e2ba1 Afegits fitxers Main a master
abfb694 Commit inicial main

$ git status -s
# (sense canvis pendents)

$ ls
README.md  fitDev1.txt  fitDev2.txt  fitxerMain1.txt  fitxerMain2.txt  index.html

$ git branch
  develop
* feature-MenuLanding
  master
```

La branca feature-MenuLanding conté tots els fitxers de develop més el nou index.html. Aquesta és la branca on es desenvoluparà la funcionalitat del menú de landing.

### 2. Crear repositori a GitHub i associar-lo

#### Accions realitzades:
- Creació de repositori buit a GitHub: `tempo-git-practica`
- Associació del repositori local amb GitHub
- Push de totes les branques al remot

#### Associació i push de branques:
```bash
# Associar repositori local amb GitHub
$ git remote add origin https://github.com/IsMeduza/tempo-git-practica.git

# Verificar configuració
$ git remote -v
origin  https://github.com/IsMeduza/tempo-git-practica.git (fetch)
origin  https://github.com/IsMeduza/tempo-git-practica.git (push)

# Pujar totes les branques
$ git push -u origin feature-MenuLanding
$ git push -u origin master
$ git push -u origin develop

# Verificar branques remotes
$ git branch -r
  origin/develop
  origin/feature-MenuLanding
  origin/master
```

#### Comprovació:
```bash
$ git log --oneline --all --graph --decorate
* f735b75 (origin/feature-MenuLanding, feature-MenuLanding) Afegit index.html a feature MenuLanding
* 7ea98cb (HEAD -> develop, origin/develop) Afegits fitxers de desenvolupament
* 824eb0c Documentació Part 1 completada
* 86e2ba1 (origin/master, master) Afegits fitxers Main a master
* abfb694 Commit inicial main
```

**Resultat:**
- Totes les branques (master, develop, feature-MenuLanding) estan disponibles a GitHub
- Cada branca manté el seu propi historial de commits
- El repositori remot està sincronitzat amb el local

**Preguntes:**
- **Què apareix al repositori de GitHub?** Les tres branques: master, develop i feature-MenuLanding
- **Quines branques existeixen?** master (amb fitxerMain1.txt i fitxerMain2.txt), develop (amb fitDev1.txt i fitDev2.txt), i feature-MenuLanding (amb tots els anteriors més index.html)
- **Quina informació conté cadascuna?** Cada branca conté els commits i fitxers propis del seu historial, seguint el flux de treball Git Flow.

### 3. Pujar totes les branques i crear Pull Request

#### Accions realitzades:
- Pujar totes les branques a GitHub
- Crear Pull Request de feature-MenuLanding cap a develop
- Capturar les opcions disponibles en crear la PR

#### Crear Pull Request:
1. Anar al repositori a GitHub: https://github.com/IsMeduza/tempo-git-practica
2. Clicar "Pull requests" > "New pull request"
3. Configurar:
   - **Base:** `develop` (branca de destinació)
   - **Compare:** `feature-MenuLanding` (branca font)
4. Clicar "Create pull request"
5. Afegir títol i descripció (opcional)
6. Clicar "Create pull request"

#### Opcions disponibles en la Pull Request:
- **Merge pull request:** Fusiona tots els commits de la feature a develop
- **Squash and merge:** Combina tots els commits en un sol commit
- **Rebase and merge:** Aplica els commits de la feature sobre develop sense crear un commit de merge
- **Revert:** Permet desfer el merge si cal

**Resultat:**
- La PR mostra 1 commit pendent de fusionar
- No hi ha conflictes amb la branca base
- El merge es pot realitzar automàticament

### 4. Resoldre PR i eliminar branca feature

#### Accions realitzades:
- Resoldre la Pull Request fent merge
- Eliminar la branca feature a GitHub (automàtic després del merge)
- Actualitzar develop local amb els canvis del merge
- Eliminar la branca feature localment

#### Resoldre Pull Request:
1. A GitHub, clicar "Merge pull request"
2. Confirmar el merge
3. La branca feature s'elimina automàticament a GitHub

#### Actualitzar develop local i eliminar branca feature:
```bash
$ git checkout develop
$ git pull origin develop
From https://github.com/IsMeduza/tempo-git-practica
 * branch            develop    -> FETCH_HEAD
   7ea98cb..8e4eb65  develop    -> origin/develop
Updating 7ea98cb..8e4eb65
Fast-forward
 index.html | 0
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 index.html

$ git branch -d feature-MenuLanding
Deleted branch feature-MenuLanding (was f735b75)

$ git branch
* develop
  master
```

#### Comprovació final:
```bash
$ git log --oneline --graph --all
*   8e4eb65 Merge pull request #1 from IsMeduza/feature-MenuLanding
|\  
| * f735b75 Afegit index.html a feature MenuLanding
|/  
* 7ea98cb Afegits fitxers de desenvolupament
* 824eb0c Documentació Part 1 completada
* 86e2ba1 Afegits fitxers Main a master
* abfb694 Commit inicial main

$ ls
README.md  fitDev1.txt  fitDev2.txt  fitxerMain1.txt  fitxerMain2.txt  index.html
```

**Preguntes:**
- **Què ha passat amb les branques feature i develop?** La branca feature s'ha fusionat a develop mitjançant un merge commit (8e4eb65). Ara develop conté tots els fitxers de la feature, incloent index.html. La branca feature s'ha eliminat tant a GitHub com localment, ja que la seva funcionalitat ja està integrada a develop.
- **Conceptualment, quan crees una PR és perquè has acabat la teua tasca a la branca feature.** Sí, exactament. Una vegada que la feature està completada i revisada, es fusiona a develop i s'elimina, mantenint el repositori net i organitzat segons el flux de treball Git Flow.

---

## Hotfix aplicat

**Nota:** Aquest hotfix s'ha aplicat per correcció urgent a la branca master i develop.

