# Sistema de advertencias en BDFD
Antes de empezar...:
Este código esta inspirado en este repositorio.
No voy a mentir, vibecodeé un poco para añadir el `/unwarn`, añadir el mensaje de 0 advertencias y arreglar unos errores, porque soy medio tonto en esto.

- [/warn](#warn)
  - [Variables de /warn](#warn-variables)
  - [Código de /warn](#warn-codigo)
- [/checkwarns](#checkwarns)
  - [Variables de /checkwarns](#checkwarns-variables)
  - [Código de /checkwarns](#checkwarns-codigo)
- [/unwarn](#unwarn)
- [Resultado](#resultado)

### /warn
El primer paso para crear una lista con JSON es crear una variable para esta, vamos a llamar esta variable `warnings` y establecer el valor en `{"warns":"0"}`. Ahora que ya lo tienes resuelto, vamos a hacer un comando para advertir a un usuario. <br>
La forma en que funciona una lista JSON es que cada advertencia está marcada con un número:`1, 2, 3, 4, etc..` y el valor `{"warns":"0"}` que añadimos antes de contar cuantas advertencia hay. 
Haremos un comando de barra diagonal llamado `/checkwarns`. Este comando será capaz de mostrar una advertencia en cada página junto a información de la advertencia. Lo explicaré después de terminar con el `/warn`.
Ahora vamos a crear este sencillo comando que añadirá una advertencia al usuario utilizando nuestra lista JSON.
### Variables de /warn
`Name: warnings` <br>
`Value: {"warns":"0"}`
### Código de /warn
Primero, necesitaremos crear un comando de barra diagonal llamado `warn` y añadir a las opciones `requeridas` una opción que será `usuario`, establecida como una opción para escoger usuario, y nuestra segunda opción requerida es `motivo`, establecida como opción de texto. <br>
Ahora vamos a crear este comando que añadirá una advertencia al usuario utilizando nuestra lista JSON.
```
$nomention
$jsonParse[$getVar[warnings;$message[usuario]]]
$jsonSetString[warns;$calculate[$json[warns]+1]]
$var[warning;$json[warns]]
$jsonSetString[w-$var[warning];R;$message[motivo]]
$jsonSetString[w-$var[warning];I;$authorID]
$jsonSetString[w-$var[warning];T;$getTimestamp]
$setVar[warnings;$jsonStringify;$message[usuario]]

$description[### ⚠️ Advertencia aplicada
>>> #️⃣ Advertencia: **$var[warning]**
📝 Motivo: $message[motivo]
⏰ Fecha: <t:$getTimestamp:d> a las <t:$getTimestamp:T>]
$thumbnail[https://em-content.zobj.net/source/google/439/warning_26a0-fe0f.png]
$footer[Usa /checkwarns para revisar el número de advertencias aplicadas.]
$color[FFCC32]
```
## /checkwarns
Ahora haremos un comando para revisar las advertencias de un usuario. Haremos un comando de barra diagonal `checkwarns` con una opción requerida llamada `usuario` establecida como una opción para escoger usuario.
### Variables de /checkwarns
Necesitamos una nueva variable JSON, así que creala con estos valores: <br>
`Nombre: warnPage` <br>
`Valor: {}`
### Código de /checkwarns
Vamos a añadir el código de `/checkwarns [usuario]` a nuestro comando.
```
$nomention

$jsonParse[$getVar[warnings;$message[usuario]]]

$if[$json[warns]<=0]

$color[7DB343]
$description[### ✅ Sin advertencias
> <@$message[usuario]> **no tiene advertencias.** Ojalá siga así.
> ⚠️ Advertencias: **0**]
$thumbnail[https://em-content.zobj.net/source/google/439/check-mark-button_2705.png]
$footer[Usa /checkwarns para revisar el número de advertencias aplicadas.]


$else


$jsonParse[$getVar[warnings;$message[usuario]]]
$var[pages;$json[warns]]
$var[page;1]
$var[motivo;$json[w-$var[page];R]]
$var[moderator;$json[w-$var[page];I]]
$var[time;$json[w-$var[page];T]]
$jsonParse[$getVar[warnPage;$message[usuario]]]
$jsonSetString[page;1]
$jsonSetString[pages;$var[pages]]
$jsonSetString[user;$message[usuario]]
$setVar[warnPage;$jsonStringify;$authorID]



$color[FFCC32]
$description[### 🗃️ Historial de advertencias de <@$message[usuario]>
>>> ⚠️ Advertencia: **$var[page]**
🚨 Moderador responsable: <@$var[moderator]> (`$var[moderator]`)
📝 Motivo de la advertencia: $var[motivo]
⏰ Aplicada: <t:$var[time]:d> a las <t:$var[time]:T>]


$thumbnail[https://em-content.zobj.net/source/google/439/card-file-box_1f5c3-fe0f.png]
$footer[Una sanción por página. Página $var[page] de $var[pages]]


$addButton[no;prev-$authorID;<;danger;yes;]
$if[$var[pages]>1] $addButton[no;next-$authorID;>;danger;no;]
$else $addButton[no;next-$authorID;>;danger;yes;]
$endif
```
### Interacción de /checkwarns
Ahora, para que las páginas funcionen, vamos a agregar esta interacción que hará que la variable de página cambie de `1, 2, 3, 4... etc.` o hacia atrás si se regresa a las páginas anteriores. <br>
Ejecutador: `$onInteraction`

```
$nomention

$jsonParse[$getVar[warnings;$message[usuario]]]

$if[$json[warns]<=0]

$color[7DB343]
$description[### ✅ Sin advertencias
> <@$message[usuario]> **no tiene advertencias.** Ojalá siga así.
> ⚠️ Advertencias: **0**]
$thumbnail[https://em-content.zobj.net/source/google/439/check-mark-button_2705.png]
$footer[Usa /checkwarns para revisar el número de advertencias aplicadas.]


$else


$jsonParse[$getVar[warnings;$message[usuario]]]
$var[pages;$json[warns]]
$var[page;1]
$var[motivo;$json[w-$var[page];R]]
$var[moderator;$json[w-$var[page];I]]
$var[time;$json[w-$var[page];T]]
$jsonParse[$getVar[warnPage;$message[usuario]]]
$jsonSetString[page;1]
$jsonSetString[pages;$var[pages]]
$jsonSetString[user;$message[usuario]]
$setVar[warnPage;$jsonStringify;$authorID]



$color[FFCC32]
$description[### 🗃️ Historial de advertencias de <@$message[usuario]>
>>> ⚠️ Advertencia: **$var[page]**
🚨 Moderador responsable: <@$var[moderator]> (`$var[moderator]`)
📝 Motivo de la advertencia: $var[motivo]
⏰ Aplicada: <t:$var[time]:d> a las <t:$var[time]:T>]


$thumbnail[https://em-content.zobj.net/source/google/439/card-file-box_1f5c3-fe0f.png]
$footer[Una sanción por página. Página $var[page] de $var[pages]]


$addButton[no;prev-$authorID;<;danger;yes;]
$if[$var[pages]>1] $addButton[no;next-$authorID;>;danger;no;]
$else $addButton[no;next-$authorID;>;danger;yes;]
$endif

```

### /unwarn
Crearemos un comando de barra diagonal llamado `unwarn`, y le añadiremos la opción requerida `usuario` como opción para escoger un usuario. <br>
Código:
```
$nomention
$jsonParse[$getVar[warnings;$message[usuario]]]

$if[$json[warns]<=0]

$color[7DB343]
$description[### ✅ Sin advertencias
> <@$message[usuario]> **no tiene advertencias** para eliminar. Ojalá siga así.]
$thumbnail[https://em-content.zobj.net/source/google/439/check-mark-button_2705.png]
$footer[Usa /checkwarns para revisar el número de advertencias aplicadas.]

$else
$var[removeIndex;$json[warns]]
$var[newWarns;$sub[$json[warns];1]]

$jsonUnset[w-$var[removeIndex]]
$jsonSetString[warns;$var[newWarns]]
$setVar[warnings;$jsonStringify;$message[usuario]]

$jsonParse[$getVar[warnPage;$authorID]]
$if[$json[user]==$message[usuario]]
$if[$var[newWarns]<=0]
$jsonSetString[page;1]
$else
$if[$json[page]>$var[newWarns]]
$jsonSetString[page;$var[newWarns]]
$endif
$if[$json[page]<1]
$jsonSetString[page;1]
$endif
$endif
$jsonSetString[pages;$var[newWarns]]
$jsonSetString[user;$message[usuario]]
$setVar[warnPage;$jsonStringify;$authorID]
$endif

$color[FFCC32]
$description[### 📂 Advertencia removida
>>> Se eliminó 1 advertencia de <@$message[usuario]>. 
📂 Advertencias anteriores: **$sum[$var[newWarns];1]**
⚠️ Advertencias actuales: **$var[newWarns]**
🚨 Moderador responsable: <@$authorID> (`$authorID`)
⏰ Removida: <t:$getTimestamp:d> a las <t:$getTimestamp:T>]
$thumbnail[https://em-content.zobj.net/source/google/439/open-file-folder_1f4c2.png]
$footer[Usa /checkwarns para revisar el número de advertencias aplicadas.]
$endif
```
# Resultados
![image](cerowarns)<br>
![image](warns)<br>
![image](quitawarns)
