# README - Paso a paso AS400 / TN5250 (avance hasta el punto 3)

Este README resume el proceso que llevamos hasta ahora en el taller de **archivos físicos y lógicos en AS400**, usando **TN5250**.

La idea es dejar documentado:
- qué hice,
- por qué lo hice,
- cómo entrar a cada opción,
- y qué detalles corregir antes de seguir.

---

## 1. Crear la librería

Primero se creó la librería de trabajo:

```txt
CRTLIB LIB(PRIMERA061)
```

Con esto quedó creada la biblioteca donde vamos a guardar:
- el archivo fuente,
- el miembro DDS,
- el archivo de referencia,
- y luego los archivos físicos y lógicos.

---

## 2. Crear el archivo fuente `QDDSSRC`

Después se creó el archivo fuente donde se guardan los miembros DDS:

```txt
CRTSRCPF FILE(PRIMERA061/QDDSSRC) RCDLEN(112) TEXT('ARCHIVO FUENTE DDS')
```

Este archivo fuente es el contenedor donde después se crea el miembro `REFER`.

---

## 3. Entrar a PDM para trabajar con miembros

Se ingresó por:

```txt
STRPDM
```

Luego:
- opción **3 = Trabajar con miembros**
- biblioteca: `PRIMERA061`
- archivo fuente: `QDDSSRC`

---

## 4. Crear el miembro `REFER`

Dentro de PDM:
- se presionó **F6 = Crear**
- nombre del miembro: `REFER`
- tipo: `PF`
- texto descriptivo: `ARCHIVO DE REFERENCIA DE CAMPOS`

Ese miembro es donde se está escribiendo el **diccionario de datos** del punto 3.

---

## 5. Editar el miembro `REFER`

En la lista de miembros:
- se colocó la opción **2 = Editar** sobre `REFER`

Esto abrió el editor SEU.

### Nota importante sobre TN5250
En TN5250 no conviene trabajar mucho con el mouse, porque varias zonas de la pantalla están protegidas.

Cuando el cursor queda en un área protegida:
- usar **Tab** para moverse al siguiente campo editable
- usar **Shift + Tab** para volver al campo anterior
- usar **F5** para renovar la pantalla si se queda rara
- si no funciona ninguna de las anteriores, cerrar el **TN5250**

---

## 6. Estructura que debe llevar el punto 3

La estructura del archivo de referencia de campos debe quedar así:
- una línea con el campo,
- luego líneas separadas para las palabras clave:
  - `TEXT(...)`
  - `COLHDG(...)`
  - `ALIAS(...)`
  - `VALUES(...)` o `COMP(...)` según la validación.

### Formato general

```txt
A          R REFER
A            NOMBRECAMPO    longitud tipo
A                                      TEXT('...')
A                                      COLHDG('...' '...')
A                                      ALIAS(...)
A                                      VALUES(...)
```

---

## 7. Campos que ya se empezaron a construir

Hasta el momento se empezó a montar el archivo de referencia con esta estructura base:

```txt
A          R REFER
A            CODPRES        2S 0
A                                      TEXT('CODIGO PRESTAMO')
A                                      COLHDG('CODIGO' 'PRESTAMO')
A                                      ALIAS(CODPREST)
A                                      VALUES(10 11 12 14 15 16 30)

A            NOMPRES       40A
A                                      TEXT('NOMBRE PRESTAMO')
A                                      COLHDG('NOMBRE' 'PRESTAMO')
A                                      ALIAS(NOMPREST)

A            CEDULA        14S 0
A                                      TEXT('CEDULA')
A                                      COLHDG('CEDULA')
A                                      ALIAS(CEDULA)

A            NUMQUIN        3S 0
A                                      TEXT('NUMERO DE QUINCENAS')
A                                      COLHDG('NUMERO' 'QUINCENAS')
A                                      ALIAS(NUMQUIN)
A                                      COMP(LE 360)

A            CODPROD        8S 0
A                                      TEXT('CODIGO DEL PRODUCTO')
A                                      COLHDG('CODIGO' 'PRODUCTO')
A                                      ALIAS(CODPROD)

A            CORREO        25A
A                                      TEXT('CORREO ELECTRONICO')
A                                      COLHDG('CORREO' 'ELECTRONICO')
A                                      ALIAS(CORREO)

A            DIRECC        40A
A                                      TEXT('DIRECCION')
A                                      COLHDG('DIRECCION')
A                                      ALIAS(DIRECC)

A            EDAD           3S 0
A                                      TEXT('EDAD')
A                                      COLHDG('EDAD')
A                                      ALIAS(EDAD)
A                                      COMP(GT 18)

A            ESTCIV         1A
A                                      TEXT('ESTADO CIVIL')
A                                      COLHDG('ESTADO' 'CIVIL')
A                                      ALIAS(ESTCIV)
A                                      VALUES('C' 'S' 'V' 'U')

A            INTERES        2S 0
A                                      TEXT('INTERES')
A                                      COLHDG('INTERES')
A                                      ALIAS(INTERES)
A                                      COMP(LE 19)

A            ESTATURA       3S 2
A                                      TEXT('ESTATURA')
A                                      COLHDG('ESTATURA')
A                                      ALIAS(ESTATURA)
A                                      COMP(LT 1.90)

A            ESTRSOC        1S 0
A                                      TEXT('ESTRATO SOCIAL')
A                                      COLHDG('ESTRATO' 'SOCIAL')
A                                      ALIAS(ESTRSOC)
A                                      VALUES(1 2 3 4 5 6)

A            NOMPER        40A
A                                      TEXT('NOMBRE DE LA PERSONA')
A                                      COLHDG('NOMBRE' 'PERSONA')
A                                      ALIAS(NOMPER)

A            NOMCARR       40A
A                                      TEXT('NOMBRE DE LA CARRERA')
A                                      COLHDG('NOMBRE' 'CARRERA')
A                                      ALIAS(NOMCARR)

A            NOMPROV       40A
A                                      TEXT('NOMBRE DEL PROVEEDOR')
A                                      COLHDG('NOMBRE' 'PROVEEDOR')
A                                      ALIAS(NOMPROV)

A            TELEFONO      20A
A                                      TEXT('TELEFONO')
A                                      COLHDG('TELEFONO')
A                                      ALIAS(TELEFONO)

A            SEXO           1A
A                                      TEXT('SEXO')
A                                      COLHDG('SEXO')
A                                      ALIAS(SEXO)
A                                      VALUES('M' 'F')

A            VALPRES        9S 2
A                                      TEXT('VALOR PRESTAMO')
A                                      COLHDG('VALOR' 'PRESTAMO')
A                                      ALIAS(VALPRES)
A                                      COMP(LE 500000000)
```

---

## 8. Validaciones: cuándo usar `VALUES` y cuándo `COMP`

Durante el proceso vimos que las reglas no siempre van con `VALUES`.

### Se usa `VALUES(...)`
Cuando el campo solo puede tomar ciertos valores fijos.

Ejemplos:

```txt
VALUES(10 11 12 14 15 16 30)
VALUES('M' 'F')
VALUES(1 2 3 4 5 6)
```

### Se usa `COMP(...)`
Cuando la regla es de comparación.

Ejemplos:

```txt
COMP(GT 18)
COMP(LE 19)
COMP(LT 1.90)
COMP(LE 500000000)
COMP(LE 360)
```

### Significado de algunos operadores
- `GT` = mayor que
- `LT` = menor que
- `GE` = mayor o igual
- `LE` = menor o igual
- `EQ` = igual

---

## 9. Cómo ver más líneas dentro del editor

Cuando el editor solo muestra una parte del miembro:
- usar **Page Down / AvPág** para bajar
- usar **Page Up / RePág** para subir
- en algunos entornos también puede funcionar con **F8** y **F7**

Esto sirve para revisar los campos que quedan más abajo y confirmar que todos sí fueron insertados.

---

## 10. Compilación del miembro `REFER`

Cuando se terminó una parte del trabajo, se compiló desde PDM con:
- opción **14 = Compilar** sobre el miembro `REFER`

El sistema mostró el mensaje:

> La compilación del miembro REFER se ha sometido por lotes.

Eso significa que la compilación fue enviada, pero no garantiza todavía que haya salido sin errores.

---

## 11. Cómo revisar el resultado de la compilación

Se ingresó a:

```txt
WRKSPLF
```

Ahí aparecen los archivos en spool.

Para revisar uno:
- poner opción **5 = Visualizar**
- buscar el spool que tenga relación con la compilación de `REFER`
- revisar si hay errores de DDS, palabras clave o sintaxis.

---

## 12. Estado actual del trabajo

Hasta este momento:
- ya está creada la librería `PRIMERA061`
- ya está creado el archivo fuente `QDDSSRC`
- ya está creado el miembro `REFER`
- ya se empezó a construir el archivo de referencia del punto 3
- ya se identificó que las validaciones pueden ir con `VALUES` o `COMP`
- ya se envió una compilación a lote
- falta revisar completamente el spool y corregir los posibles errores finales

---

## 13. Pendientes antes de pasar al punto 4

Antes de seguir con los archivos físicos de personales y académicos, conviene verificar que el `REFER` quede totalmente correcto:

1. corregir `RREFER` por `REFER`
2. confirmar que todas las líneas de palabras clave estén bien escritas
3. confirmar cuáles campos llevan `VALUES` y cuáles llevan `COMP`
4. compilar nuevamente
5. revisar el spool de compilación sin errores

---

## 14. Próximo paso

Cuando el archivo `REFER` compile bien, el siguiente paso será:
- crear el archivo físico de **datos personales**
- luego el archivo físico de **datos académicos**
- y después el archivo lógico dependiente del físico de personales.

