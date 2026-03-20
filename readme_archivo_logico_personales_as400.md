# README – Creación de archivo lógico dependiente del archivo físico de datos personales en AS400 / TN5250

Este documento explica paso a paso cómo se realizó el punto del taller en el que se debía:

> **Crear un archivo lógico dependiente del archivo físico de datos personales clasificado por nombre y dentro de nombre por estatura en orden ascendente, seleccionando aquellos registros de las mujeres que tengan más de 30 años, que sean solteras, de estrato social mayor de 4 y que además midan más de 1 metro con 80 centímetros.**

---

## 1. Objetivo del punto

Construir un **archivo lógico (LF)** que dependa del archivo físico de **datos personales** y que permita:

- ordenar los registros por **nombre**
- dentro del nombre, ordenar por **estatura** en **orden ascendente**
- mostrar únicamente los registros que cumplan ciertas condiciones

Las condiciones pedidas en el enunciado son:

- sexo femenino
- edad mayor a 30
- estado civil soltera
- estrato social mayor a 4
- estatura mayor a 1.80

---

## 2. Qué es un archivo lógico

Un archivo lógico en AS400:

- **no almacena datos nuevos**
- depende de un archivo físico
- permite **ordenar**
- permite **filtrar registros**
- permite ver la información según ciertas claves y condiciones

En este caso, el archivo lógico depende del archivo físico `PERSONALES`.

---

## 3. Archivo físico del cual depende

El lógico se construyó sobre el archivo físico de datos personales, que contiene estos campos:

- `CEDPER`
- `CORELE`
- `DIRPER`
- `EDAPER`
- `ECIVIL`
- `ESTPER`
- `ESTRAT`
- `NOMALU`
- `TELCON`
- `SEXPER`

De esos campos, para este punto interesan sobre todo:

- `NOMALU` → nombre
- `ESTPER` → estatura
- `SEXPER` → sexo
- `EDAPER` → edad
- `ECIVIL` → estado civil
- `ESTRAT` → estrato social

---

## 4. Condiciones del enunciado traducidas al DDS

El enunciado pide seleccionar registros que cumplan esto:

- mujeres → `SEXPER = 'F'`
- mayores de 30 años → `EDAPER > 30`
- solteras → `ECIVIL = 'S'`
- estrato social mayor de 4 → `ESTRAT > 4`
- estatura mayor a 1.80 → `ESTPER > 1.80`

Además, el orden solicitado es:

1. por `NOMALU`
2. y dentro de `NOMALU`, por `ESTPER`

---

## 5. Estructura DDS final del archivo lógico

La estructura que finalmente quedó correcta fue esta:

```txt
A          R RLOGPER                   PFILE(PRIMERA061/PERSONALES)
A            CEDPER
A            CORELE
A            DIRPER
A            EDAPER
A            ECIVIL
A            ESTPER
A            ESTRAT
A            NOMALU
A            TELCON
A            SEXPER
A          K NOMALU
A          K ESTPER
A          S SEXPER                    COMP(EQ 'F')
A          S EDAPER                    COMP(GT 30)
A          S ECIVIL                    COMP(EQ 'S')
A          S ESTRAT                    COMP(GT 4)
A          S ESTPER                    COMP(GT 1.80)
```

---

## 6. Explicación de la estructura

### `R RLOGPER`
Es el nombre del formato de registro del archivo lógico.

### `PFILE(PRIMERA061/PERSONALES)`
Indica que este lógico depende del archivo físico `PERSONALES` que está en la biblioteca `PRIMERA061`.

### Campos listados
Estas líneas indican los campos que el archivo lógico va a manejar:

- `CEDPER`
- `CORELE`
- `DIRPER`
- `EDAPER`
- `ECIVIL`
- `ESTPER`
- `ESTRAT`
- `NOMALU`
- `TELCON`
- `SEXPER`

### Claves
```txt
A          K NOMALU
A          K ESTPER
```

Estas líneas indican el orden del archivo lógico:

- primero por `NOMALU`
- luego por `ESTPER`

Como no se especificó descendente, el orden queda **ascendente por defecto**.

### Selección
```txt
A          S SEXPER                    COMP(EQ 'F')
A          S EDAPER                    COMP(GT 30)
A          S ECIVIL                    COMP(EQ 'S')
A          S ESTRAT                    COMP(GT 4)
A          S ESTPER                    COMP(GT 1.80)
```

Estas líneas filtran los registros según las condiciones del ejercicio.

---

## 7. Paso a paso para crear el archivo lógico en TN5250

## Paso 1. Entrar a PDM
Se ingresa al administrador PDM con:

```txt
STRPDM
```

Luego se selecciona:

- **3 = Trabajar con miembros**
- biblioteca: `PRIMERA061`
- archivo fuente: `QDDSSRC`

---

## Paso 2. Crear el miembro del archivo lógico
Dentro de PDM se presiona:

- **F6 = Crear**

Y se diligencia por ejemplo:

- nombre del miembro: `LOGPER`
- tipo: `LF`
- texto descriptivo: `ARCHIVO LOGICO PERSONALES`

---

## Paso 3. Editar el miembro
En la lista de miembros:

- se coloca **2 = Editar** sobre el miembro `LOGPER`

Luego se escribe la estructura DDS del lógico.

---

## Paso 4. Guardar el miembro
Después de escribir el DDS:

- se guardan los cambios
- se sale del editor

---

## Paso 5. Compilar el archivo lógico
En PDM:

- se coloca **14 = Compilar** sobre el miembro `LOGPER`

Como se trata de un archivo lógico, debe compilarse como tipo `LF`.

---

## Paso 6. Revisar el resultado de la compilación
Después se consulta el spool con:

```txt
WRKSPLF
```

Luego:

- se busca el spool relacionado con `LOGPER`
- se pone **5 = Visualizar**
- se revisa si existen errores, avisos o mensajes graves

Si el resumen muestra cero errores y mensaje de archivo creado, entonces la compilación fue correcta.

---

## 8. Error encontrado durante el proceso

Durante la primera compilación del archivo lógico sí se presentó error.

El problema fue el **orden de las especificaciones** dentro del DDS.

Inicialmente se habían colocado primero las líneas de selección `S` y después las líneas de clave `K`, lo cual provocó mensajes de compilación y evitó que el archivo fuera creado.

La corrección consistió en reorganizar el DDS así:

1. campos
2. claves `K`
3. selección `S`

Ese cambio permitió que la compilación se realizara correctamente.

---

## 9. Forma correcta de ordenar las líneas del DDS en este caso

La estructura correcta quedó en este orden:

1. línea del formato con `R` y `PFILE`
2. lista de campos
3. claves `K`
4. condiciones de selección `S`

Eso fue lo que solucionó el error de compilación del archivo lógico.

---

## 10. Cómo moverse a la derecha dentro del spool

En la visualización del spool, varios mensajes largos pueden quedar cortados.

Para moverse horizontalmente se usan estas teclas:

- **F19 = Izquierda**
- **F20 = Derecha**

Esto permite leer los mensajes completos de compilación.

---

## 11. Resultado final del punto

Con la corrección realizada, el archivo lógico quedó construido de forma que:

- depende del archivo físico `PERSONALES`
- organiza por nombre
- dentro del nombre organiza por estatura ascendente
- selecciona únicamente mujeres
- mayores de 30 años
- solteras
- de estrato mayor a 4
- y con estatura superior a 1.80

---

## 12. Resumen corto

Se creó un archivo lógico llamado `LOGPER`, dependiente del archivo físico `PERSONALES`, utilizando `PFILE`.  
Se incluyeron los campos necesarios del archivo físico, se definieron como claves `NOMALU` y `ESTPER`, y se agregaron condiciones de selección con `S` y `COMP` para filtrar únicamente los registros solicitados por el enunciado.  
Después de corregir el orden de las claves y las selecciones, el archivo compiló correctamente.

---

## 13. Observación final

Si en otro entorno la biblioteca no es `PRIMERA061`, entonces debe reemplazarse en esta línea:

```txt
PFILE(PRIMERA061/PERSONALES)
```

por la biblioteca real correspondiente.

Ejemplo:

```txt
PFILE(FISIC0016/PERSONALES)
```
