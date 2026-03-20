# README – Creación de archivo lógico dependiente del archivo físico de préstamos en AS400 / TN5250

Este documento explica paso a paso cómo se realizó el último punto del taller, en el cual se debía:

> **Crear un archivo lógico dependiente del archivo físico de datos de préstamos, clasificado por valor del préstamo en orden descendente y dentro de valor por número de quincenas en orden ascendente, omitiendo aquellos registros de los préstamos de vivienda que superen los 150 millones de pesos y además las 200 quincenas con interés mayor al 8 por ciento.**

---

## 1. Objetivo del punto

Construir un **archivo lógico (LF)** que dependa del archivo físico de **préstamos** y que permita:

- ordenar los registros por **valor del préstamo** en **orden descendente**
- dentro del valor, ordenar por **número de quincenas** en **orden ascendente**
- omitir ciertos registros de préstamos de vivienda que cumplan condiciones específicas

---

## 2. Interpretación del enunciado

El archivo lógico debía excluir los registros correspondientes a **préstamos de vivienda** cuando al mismo tiempo se cumplieran estas condiciones:

- que el préstamo fuera de **vivienda**
- que el valor del préstamo fuera superior a **150.000.000**
- que el número de quincenas fuera superior a **200**
- que el interés fuera mayor al **8%**

Durante el desarrollo se aclaró que un préstamo de vivienda se identifica con:

```txt
CODPRS = 30
```

Esto se debe a las reglas definidas previamente para el código del préstamo:

- `10` = seguro
- `11` = ordinario
- `12` = social
- `14` = capacitación
- `15` = salud
- `16` = vehículo
- `30` = vivienda

---

## 3. Archivo físico del cual depende

El archivo lógico se construyó sobre el archivo físico de préstamos, que contiene estos campos:

- `CODPRS`
- `NOMPRS`
- `NQUINC`
- `INTANU`
- `VLPRES`
- `CODPRD`
- `NPROVE`

---

## 4. Orden solicitado en el archivo lógico

El enunciado pedía organizar el archivo lógico así:

1. por **valor del préstamo** en **orden descendente**
2. dentro del valor, por **número de quincenas** en **orden ascendente**

En DDS eso se representó con estas claves:

```txt
A          K VLPRES                    DESCEND
A          K NQUINC
```

- `VLPRES` queda descendente
- `NQUINC` queda ascendente por defecto

---

## 5. Estructura DDS final del archivo lógico

La estructura final utilizada fue:

```txt
A          R RLOGPRE                   PFILE(PRIMERA061/PRESTAMOS)
A            CODPRS
A            NOMPRS
A            NQUINC
A            INTANU
A            VLPRES
A            CODPRD
A            NPROVE
A          K VLPRES                    DESCEND
A          K NQUINC
A          O CODPRS                    COMP(EQ 30)
A          O VLPRES                    COMP(GT 150000000)
A          O NQUINC                    COMP(GT 200)
A          O INTANU                    COMP(GT 8)
```

---

## 6. Explicación de la estructura

### `R RLOGPRE`
Es el nombre del formato de registro del archivo lógico.

### `PFILE(PRIMERA061/PRESTAMOS)`
Indica que el lógico depende del archivo físico `PRESTAMOS` ubicado en la biblioteca `PRIMERA061`.

### Campos listados
Se incluyeron los campos del archivo físico que hacen parte de los datos del préstamo:

- `CODPRS`
- `NOMPRS`
- `NQUINC`
- `INTANU`
- `VLPRES`
- `CODPRD`
- `NPROVE`

### Claves
```txt
A          K VLPRES                    DESCEND
A          K NQUINC
```

Estas líneas indican el orden del archivo lógico:

- primero por valor del préstamo, de mayor a menor
- luego por número de quincenas, de menor a mayor

### Omisión de registros
```txt
A          O CODPRS                    COMP(EQ 30)
A          O VLPRES                    COMP(GT 150000000)
A          O NQUINC                    COMP(GT 200)
A          O INTANU                    COMP(GT 8)
```

Estas líneas se usaron para omitir los registros de vivienda que exceden los valores indicados por el enunciado.

---

## 7. Paso a paso para crear el archivo lógico en TN5250

## Paso 1. Entrar a PDM
Se ingresa con:

```txt
STRPDM
```

Luego:

- seleccionar **3 = Trabajar con miembros**
- escribir la biblioteca `PRIMERA061`
- escribir el archivo fuente `QDDSSRC`

---

## Paso 2. Crear el miembro del archivo lógico
Dentro de PDM:

- presionar **F6 = Crear**

Y diligenciar por ejemplo:

- miembro: `LOGPRE`
- tipo: `LF`
- texto descriptivo: `ARCHIVO LOGICO PRESTAMOS`

---

## Paso 3. Editar el miembro
En la lista de miembros:

- colocar **2 = Editar** sobre el miembro `LOGPRE`

Luego se escribe la estructura DDS del archivo lógico.

---

## Paso 4. Escribir el DDS
Se escribe esta estructura:

```txt
A          R RLOGPRE                   PFILE(PRIMERA061/PRESTAMOS)
A            CODPRS
A            NOMPRS
A            NQUINC
A            INTANU
A            VLPRES
A            CODPRD
A            NPROVE
A          K VLPRES                    DESCEND
A          K NQUINC
A          O CODPRS                    COMP(EQ 30)
A          O VLPRES                    COMP(GT 150000000)
A          O NQUINC                    COMP(GT 200)
A          O INTANU                    COMP(GT 8)
```

---

## Paso 5. Guardar
Después de escribir el DDS:

- guardar los cambios
- salir del editor

---

## Paso 6. Compilar el archivo lógico
El archivo lógico puede compilarse de dos maneras.

### Opción A: desde PDM
- colocar **14 = Compilar** sobre el miembro `LOGPRE`

### Opción B: con comando
También se puede compilar con:

```txt
CRTLF FILE(PRIMERA061/LOGPRE) SRCFILE(PRIMERA061/QDDSSRC) SRCMBR(LOGPRE)
```

Si el archivo ya existe y se desea recompilarlo, se puede usar:

```txt
CRTLF FILE(PRIMERA061/LOGPRE) SRCFILE(PRIMERA061/QDDSSRC) SRCMBR(LOGPRE) REPLACE(*YES)
```

---

## Paso 7. Revisar la compilación
Después de compilar se consulta el spool con:

```txt
WRKSPLF
```

Luego:

- buscar el spool del archivo `LOGPRE`
- poner **5 = Visualizar**
- revisar si hay errores, avisos o mensajes graves

Si aparece mensaje de archivo creado y no hay errores graves, la compilación fue correcta.

---

## 8. Error presentado durante el proceso

Durante el desarrollo apareció un error de compilación porque el sistema no encontraba el archivo físico al que debía apuntar el lógico.

El mensaje indicaba que no se había encontrado el archivo especificado en `PFILE(...)`.

Después se identificó que el problema real no estaba en el archivo lógico, sino en que el archivo físico `PRESTAMOS` tenía un error de compilación previo.  
Una vez ese problema fue corregido, el archivo lógico pudo compilar correctamente.

---

## 9. Diferencia entre crear y recompilar

Cuando se ejecuta `CRTLF`, el sistema intenta crear el archivo lógico.

Si el archivo ya existe, el sistema muestra un mensaje indicando que el objeto ya fue creado anteriormente.

En ese caso hay dos alternativas:

### Reemplazar directamente
```txt
CRTLF FILE(PRIMERA061/LOGPRE) SRCFILE(PRIMERA061/QDDSSRC) SRCMBR(LOGPRE) REPLACE(*YES)
```

### O eliminarlo primero y volver a crearlo
```txt
DLTF FILE(PRIMERA061/LOGPRE)
CRTLF FILE(PRIMERA061/LOGPRE) SRCFILE(PRIMERA061/QDDSSRC) SRCMBR(LOGPRE)
```

---

## 10. Resultado final del punto

Con este archivo lógico se logró:

- depender del archivo físico `PRESTAMOS`
- ordenar por valor del préstamo en descendente
- dentro del valor, ordenar por número de quincenas en ascendente
- omitir los préstamos de vivienda con código `30` que exceden el valor, las quincenas y el interés indicados en el enunciado

---

## 11. Resumen corto

Se creó un archivo lógico llamado `LOGPRE`, dependiente del archivo físico `PRESTAMOS`, usando `PFILE`.  
Se incluyeron los campos del préstamo, se definieron las claves `VLPRES` y `NQUINC` para el orden solicitado, y se agregaron condiciones de omisión con `O` y `COMP` para excluir los préstamos de vivienda con código `30` que superaran las restricciones indicadas.  
Después de corregir el problema previo de compilación del archivo físico de préstamos, el lógico compiló correctamente.

---

## 12. Observación final

Si en otro entorno la biblioteca no es `PRIMERA061`, esta línea debe ajustarse:

```txt
PFILE(PRIMERA061/PRESTAMOS)
```

por la biblioteca real correspondiente.

Ejemplo:

```txt
PFILE(FISIC0016/PRESTAMOS)
```
