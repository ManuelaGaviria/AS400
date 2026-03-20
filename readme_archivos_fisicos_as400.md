# README – Creación de archivos físicos en AS400 / TN5250

Este documento resume cómo se crearon los **dos archivos físicos** a partir del archivo de referencia `REFER`:

- **Archivo físico de datos personales**
- **Archivo físico de datos de préstamos**

La idea es dejar claro:
- cómo se crean,
- qué estructura llevan,
- qué campos contiene cada uno,
- y cuál es la clave definida en cada archivo.

---

## 1. Requisito previo

Antes de crear estos archivos físicos, debe existir y estar correctamente compilado el **archivo de referencia de campos** `REFER`, dentro de la librería de trabajo.

Ese archivo de referencia contiene la definición de los campos, por ejemplo:
- tipo de dato,
- longitud,
- decimales,
- texto descriptivo,
- encabezados,
- alias,
- validaciones.

Los archivos físicos no vuelven a definir todo eso, sino que **heredan la definición** desde `REFER`.

---

## 2. Archivo físico de datos personales

## Objetivo
Crear un archivo físico que contenga los datos personales de la persona.

## Estructura DDS

```txt
A          REF(FISIC0016/REFER)
A          UNIQUE
A          R RDTPERS
A                                      TEXT('REGISTRO DE DATOS PERSONALES')
A            CEDPER        R
A            CORELE        R
A            DIRPER        R
A            EDAPER        R
A            ECIVIL        R
A            ESTPER        R
A            ESTRAT        R
A            NOMALU        R
A            TELCON        R
A            SEXPER        R
A          K CEDPER
```

## Explicación de la estructura

### `REF(FISIC0016/REFER)`
Indica que el archivo toma los campos desde el archivo de referencia `REFER`.

### `UNIQUE`
Indica que la clave no se puede repetir.

### `R RDTPERS`
Es el nombre del formato de registro del archivo físico de personales.

### `TEXT('REGISTRO DE DATOS PERSONALES')`
Es la descripción del registro.

### Campos utilizados
Los campos incluidos en este archivo son:

- `CEDPER` → cédula
- `CORELE` → correo electrónico
- `DIRPER` → dirección
- `EDAPER` → edad
- `ECIVIL` → estado civil
- `ESTPER` → estatura
- `ESTRAT` → estrato social
- `NOMALU` → nombre de la persona
- `TELCON` → teléfono
- `SEXPER` → sexo

### Clave del archivo
```txt
A          K CEDPER
```

La clave del archivo es la cédula, por lo que no se pueden repetir registros con la misma identificación.

---

## 3. Archivo físico de datos de préstamos

## Objetivo
Crear un archivo físico que contenga los datos relacionados con los préstamos.

## Estructura DDS

```txt
A          REF(FISIC0016/REFER)
A          UNIQUE
A          R RDPREST
A                                      TEXT('REGISTRO DE DATOS DE PRESTAMOS')
A            CODPRS        R
A            NOMPRS        R
A            NQUINC        R
A            INTANU        R
A            VLPRES        R
A            CODPRD        R
A            NPROVE        R
A          K CODPRS
```

## Explicación de la estructura

### `REF(FISIC0016/REFER)`
Indica que el archivo toma los campos desde el archivo de referencia `REFER`.

### `UNIQUE`
Indica que la clave no se puede repetir.

### `R RDPREST`
Es el nombre del formato de registro del archivo físico de préstamos.

### `TEXT('REGISTRO DE DATOS DE PRESTAMOS')`
Es la descripción del registro.

### Campos utilizados
Los campos incluidos en este archivo son:

- `CODPRS` → código del préstamo
- `NOMPRS` → nombre del préstamo
- `NQUINC` → número de quincenas
- `INTANU` → interés
- `VLPRES` → valor del préstamo
- `CODPRD` → código del producto
- `NPROVE` → nombre del proveedor

### Clave del archivo
```txt
A          K CODPRS
```

La clave del archivo es el código del préstamo, por lo que no se pueden repetir registros con el mismo código.

---

## 4. Cómo crear cada archivo en TN5250

El procedimiento general para ambos archivos es el mismo.

## Paso 1. Entrar a PDM
Ejecutar:

```txt
STRPDM
```

Luego:
- seleccionar **3 = Trabajar con miembros**
- escribir la librería
- escribir el archivo fuente `QDDSSRC`

## Paso 2. Crear el miembro del archivo físico
Dentro de PDM:
- presionar **F6 = Crear**
- escribir el nombre del miembro
- escoger tipo `PF`
- escribir un texto descriptivo

Ejemplos:
- miembro para personales
- miembro para préstamos

## Paso 3. Editar el miembro
En la lista de miembros:
- poner **2 = Editar**
- escribir la estructura DDS correspondiente

## Paso 4. Guardar
Luego de escribir el DDS:
- guardar los cambios
- salir del editor

## Paso 5. Compilar
Desde PDM:
- poner **14 = Compilar** sobre el miembro

O compilar con comando si lo requieren.

## Paso 6. Revisar la compilación
Ejecutar:

```txt
WRKSPLF
```

Luego:
- buscar el spool de compilación
- poner **5 = Visualizar**
- revisar si hay errores o si compiló correctamente

---

## 5. Diferencia entre el archivo de referencia y los archivos físicos

## Archivo de referencia `REFER`
Define:
- nombres de los campos
- tipos
- longitudes
- alias
- encabezados
- validaciones

## Archivos físicos
Solo seleccionan cuáles campos usarán del archivo `REFER`.

Por eso en los archivos físicos cada campo aparece así:

```txt
A            CEDPER        R
```

La `R` significa que el campo se toma por referencia.

---

## 6. Resumen de campos por archivo

## Datos personales
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

## Datos de préstamos
- `CODPRS`
- `NOMPRS`
- `NQUINC`
- `INTANU`
- `VLPRES`
- `CODPRD`
- `NPROVE`

---

## 7. Observación final

Si la librería de trabajo no es `FISIC0016`, entonces en ambas estructuras debe cambiarse la referencia:

```txt
REF(FISIC0016/REFER)
```

por la librería que realmente se esté usando.

Ejemplo:

```txt
REF(PRIMERA061/REFER)
```

---

## 8. Estado del avance

Con estos dos archivos ya queda adelantada la parte de:
- crear un archivo físico de datos personales
- crear un archivo físico de datos de préstamos

a partir del archivo de referencia de campos.

