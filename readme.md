# 2. Git & GitHub

## Objetos

Git en su core es como un sistema de almacenamiento de clave-valor.

### Clave 

Es un hash sha1

### Valor

Es la data comprimida en diferentes objetos:

### blob

Pieza minima de contenido almacenado en Git.

Metadatos:

- Identificador del tipo de objeto
- Lenght del la data
- Delimitador
- Data

__12ef3__
| blob      | 11  |
| --------- | --- |
| \\0       |     |
| TheBridge |     |


- `git init` inicializa un repo y nos da el path a `.git`

Algunos comandos que git utiliza a bajo nivel:
`git hash-object` genera el sha1 de la data con los metadatos

`echo 'TheBridge' | git hash-object --stdin` 

`echo 'blob 10\0TheBridge' | openssl sha1` 

Con el flag `-w` indicamos a a git que escriba el blob en el repo  
`echo 'TheBridge' | git hash-object -w --stdin

Output: `13c5cebc7f2333d5eab2acb9ed1f7fe25d16dc9a`

¿Cómo sabe git el nombre del archivo y el arbol de directorios al que corresponde el blob?

### tree 

Un tree contiene punteros usando sha1 a blobs o a otros trees

Metadatos:

- tipo de puntero (blob o tree)
- nombre del archivo o directorio
- modo (ejecutable o enlace simbolico)

__31cb3__
| tree | length |            |
| ---- | ------ | ---------- |
| \\0  |        |            |
| blob | 13c5ce | file.md    |
| tree | 3d51c1 | foldername |


**Git apunta el mismo blob para mismas piezas de contenido**

[Grafico](https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell)

### Commit

Los commits son otros tipos de objetos como blob y tree

Un commit contiene un puntero a un directorio o tree

Metadatos:
- author 
- commiter
- date
- message
- parent commit (uno o varios (en el caso de fusiones)) 

El sha1 del commit es el hash de toda esta información 

__4dcf13__
| commit  | size         |
| ------- | ------------ |
| tree    | 12d121       |
| parent  | 5d12d        |
| author  | Ana      |
| message | "add navbar" |

El primer commit "first commit" no tiene un parent commit 


El contenido de los objetos binarios que almacena git dentro de `.git/object` está comprimido 

`cat .git/objects/40/e8fe6ebfca692b9f216122e1d7a173c8bce29d`

x��A
� @Ѯ=��
���~�c׾�ϕ6zÍ~��w���V^2�p�a����\�����W�dj3_�A�


Descomprimir este contenido:
`git cat-file -t 40e8fe6 
`git cat-file -p 40e8fe6 

En git no se puede cambiar un commit, no podemos editarlo o corromperlo porque cambiaría el sha1. Incluso si copias todo el contenido tendrá una fecha diferente. Es un sistema de seguridad, si tienes un hash de un commit puedes estar seguro que el contenido no ha cambiado. 

La eficiencia con la que git almacena los datos y sus algoritmos de compresión permiten alamacenar toda la historia del repositorio en nuestras máquinas locales. Es muy rápido consultar sucursales o desplegar cambios.

[Object](https://git-scm.com/book/en/v2/Git-Internals-Git-Objects)






## Areas

3 areas donde vive el código

### Working Area

También llamada working tree

Son los archivos untracked o nuevos cambios que no están en el staging area, que no están bajo el control de git, los que acaban de llegar a tu directorio local. Los archivos sin seguimiento. 

Es el espacio cero donde puede agregar nuevo contenido, modificar, eliminar.

### Staging Area 

También llamada cache o index.

Son los archivos que serán parte de tu próximo commit. 

Es el mecanismo que usa git para saber lo que va a cambiar entre el commit actual y el siguiente. 

un staging area limpia no está vacía aunque lo parece. Contiene los mismos archivos que estaban en el último commit. 

`git ls-files -s`

El index o staging area es realmente un binario dentro de .git. Git detecta cuando cambia un archivo porque su sha1 cambia.

`git add` añade archivo al siguiente commit 
`git rm` Elimina archivo del siguiente commit 
`git mv` para cambiar el nombre o mover archivos en el propio commit 


Cuando quitas un archivo del staging area no lo eliminas, estás remplazandolo con una copia del ultimo commit del repo. El staging area no está vacio.

### Repo

Contiene todos tus commits. 

Un commits es una instantanea del staging area en el momento del commit 

El repositorio se encuentra en el directorio `.git` y almacena el estado de estos archivos de forma segura para poder cambiarlos con garantía de poder volver a ellos si se necesita. 



Preparamos un nuevo cambio para hacer commit:
`git add readme.md`

Hacemos el commit
`git commit`


##### Mensaje de commit 

Los commits conservan la historia de la base de código.

Ayudan a:
- Depurar 
- Solucionar problemas
- Informar de qué se introdujo en un punto de la historia si los mensajes de los commits osn buenos.
- Revisar pull request cuando son largos. 
- Identificar en qué punto interesa revertir un proyecto. 
- También ayudan a asociar el código con la numeración del sistemas de tickets externos usados por la organización. 

Buen commit: 
- Buen mensaje de commit
- Encapsulan una idea lógica
- No dejan el código roto. 

[git-style-guide.md](https://gist.github.com/allamasln/f0f5f1d5c7903e4994218232d63b1314)

(Ejercicio 1)

## Ejercicio 1

1.  Crea una nueva carpeta e inicializa en ella un repositorio de git
2.  Crea un archivo `file.txt` con el contenido 'Hello world', añadelo al Staging Area y crea un commit.
3. Crea una carpeta dentro del repositorio con el nombre `copia` y haz una copia del archivo creado en el paso 2 con el nombre `copia-file.txt`
4.  Insepecciona los objetos creados dentro del directorio `.git`.
5. Usa `git cat-file` y localiza cada objeto (blob, tree y commit).

## Ejercicio 2

1.  Crea una nueva rama con el nombre `ejercicio2` en el repositorio del ejercicio uno
2.  Cambia la referencia de HEAD para que apunte a la nueva rama
3. Mira los archivos `.git/HEAD` y `.git/refs/heads/*`  e inspecciona a donde apuntan esas referencias (HEAD, nueva rama) 
4. Agrega una linea de contenido al fichero file.txt. Añade los cambios al staging area
5. Crea un Stash los cambios. 
6. Elimina el directorio copia del staging area y registra un nuevo commit
7. Aplica el alijo y registra otro commit
8. Comprueba con `git log` los commits registrados. 


1- DEVELOPER1: Crea un repositorio de GitHub llamado `practice-git`. 

2- DEVELOPER1: 
	1. Clona el repositorio 
	2. Crea una rama para modificar el archivo README.md. Si no existe crealo.
	4. Haz cambios en dos commits sobre el README.md.  Comete un error ortográfico en alguna palabra.
	5. Modifica el mensaje del último commit
	6. Haz push de la rama y fusiona la rama a main con la estrategia fast-forward 

   DEVELOPER2:
1. Haz un fork del repositorio del DEVELOPER1 y clona tu fork a local
2. Configura el repositorio remoto upstream
3. Crea una rama para añadir un archivo CHANGELOG.md  con el contenido. 

```
# Changelog

## v1.0.0
-   Added this changelog
```
3. Crea un commit, 
4. Trae los cambios nuevos que haya en la rama principal del repositorio upstream y fusionalos a tu feature branch. 
5. Haz push de la rama a tu fork y abre un pull request contra la rama principal del upstream

3- DEVELOPER 2. 

1. Actualiza la rama principal y crea una nueva feature branch
2. Haz los cambios necesario para poner un enlace arriba del README.md para ir a CHANGELOG.md
3. Echa una mano si es necesario a tu compañero hasta que termine con el último paso de este apartado.
4. Ahora con tu compañero en pair programming actualiza tu rama feature con los nuevos cambios del upstream. Resuelve los conflictos si es necesario 
5. Haz un push de los cambios y abre un pull request. 

  DEVELOPER 1. 
  1. Revisa y fusiona el primer pull request del DEVELOPER2. Asegurate de hacerlo creando un merge commit. 
  2. Pregunta a tu compañero si ya ha creado la feature branch que se le pide en el apartado 3. Esperalo y echale una mano si lo necesita.
  3. Una vez haya creado la rama, mientras tu compañero trabaja en los cambios en README.md, actualiza tu repositorio local y crea una rama para corregir el error ortografico en el README.md
  4. Haz el commit, pushea la rama y fusiona los cambios en la rama principal
  5. Vuelvo con tu compañero para echarle una mano hasta que haga el pull request.
