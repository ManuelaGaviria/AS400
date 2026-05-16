# Trabajo AS/400 - Programación académica
 
**Archivo físico principal:** PROGACA  
**Fuente DDS:** QDDSSRC  
**Fuente de pantallas y menú:** QMNUSRC  
**Fuente RPG:** QRPGLESRC  

> Nota importante: conservé los nombres técnicos de campos y objetos que se usan en el sistema, porque en AS/400 esos nombres deben coincidir entre DDS, pantallas y programas RPG para que compile y funcione. Sí cambié nombres auxiliares de explicación y evité usar nombres genéricos cuando no eran necesarios.  
> Los programas finales quedaron en RPG IV de formato fijo, sin `/FREE`, porque fue la forma que compiló correctamente en TN5250.

---

## Convenciones usadas en TN5250

### Teclas útiles

| Tecla | Uso |
|---|---|
| `Enter` | Ejecutar o confirmar |
| `F3` | Salir |
| `F4` | Solicitud / ayuda de parámetros |
| `F5` | Renovar pantalla |
| `F6` | Crear miembro nuevo en PDM |
| `F10` | Cursor / opción de algunas pantallas |
| `F11` | Cambiar vista / conmutar |
| `F12` | Cancelar |
| `F24` | Más teclas |

### Opciones de PDM usadas

| Opción | Uso |
|---|---|
| `2` | Editar miembro |
| `4` | Suprimir miembro |
| `5` | Visualizar miembro |
| `14` | Compilar miembro |
| `15` | Crear módulo / según menú |
| `D` | Borrar línea en SEU |
| `D2`, `D5`, etc. | Borrar varias líneas desde esa línea |
| `I` | Insertar una línea |
| `I5` | Insertar cinco líneas |
| `IP` | Insertar pantalla / bloque de líneas, según contexto |
| `A` | Agregar después de una línea |
| `C` | Copiar línea o bloque |
| `M` | Mover línea o bloque |

---

# Punto 1. Crear biblioteca del trabajo

## Objetivo

Crear la biblioteca donde se almacenarán los archivos fuente, archivos físicos, pantallas, menú y programas RPG del trabajo.

## Comando

En la línea de comandos de AS/400 escribir:

```txt
CRTLIB LIB(SEGUNDA061) TEXT('SEGUNDA EVALUACION AS400')
```

Presionar `Enter`.

## Verificación

Ejecutar:

```txt
WRKLIB LIB(SEGUNDA061)
```

Si aparece la biblioteca, quedó creada correctamente.

También se puede revisar con:

```txt
DSPLIB LIB(SEGUNDA061)
```

---

# Punto 2. Crear archivos fuente

## Objetivo

Crear los archivos fuente donde se guardarán los DDS, pantallas, menú y programas RPG.

## 2.1 Crear fuente DDS

```txt
CRTSRCPF FILE(SEGUNDA061/QDDSSRC) RCDLEN(112) TEXT('ARCHIVO FUENTE DDS')
```

## 2.2 Crear fuente para pantallas y menú

```txt
CRTSRCPF FILE(SEGUNDA061/QMNUSRC) RCDLEN(112) TEXT('ARCHIVO FUENTE PANTALLAS Y MENU')
```

## 2.3 Crear fuente RPG

```txt
CRTSRCPF FILE(SEGUNDA061/QRPGLESRC) RCDLEN(112) TEXT('ARCHIVO FUENTE RPGLE')
```

## Verificación

```txt
WRKOBJ OBJ(SEGUNDA061/QDDSSRC) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/QMNUSRC) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/QRPGLESRC) OBJTYPE(*FILE)
```

---

# Punto 3. Crear archivo de referencia

## Objetivo

Crear un miembro DDS llamado `REFER`, que servirá como diccionario de campos.

## Entrar al archivo fuente

```txt
WRKMBRPDM FILE(SEGUNDA061/QDDSSRC)
```

Presionar `Enter`.

Presionar `F6` para crear un miembro nuevo.

Llenar:

```txt
Miembro fuente . . . . . . . . . . : REFER
Tipo fuente . . . . . . . . . . . : PF
Texto . . . . . . . . . . . . . . : ARCHIVO DE REFERENCIA
```

Presionar `Enter`.

## Código DDS del archivo `REFER`

```rpgle
A          R REFER
A            CODASI         8A
A                                      TEXT('CODIGO ASIGNATURA')
A                                      COLHDG('CODIGO' 'ASIGNATURA')
A                                      ALIAS(CODASIG)

A            NOMCIU         4S 0
A                                      TEXT('NOMBRE O CODIGO CIUDAD')
A                                      COLHDG('NOMBRE' 'CIUDAD')
A                                      ALIAS(NOMCIUD)

A            CODSED         2S 0
A                                      TEXT('CODIGO SEDE')
A                                      COLHDG('CODIGO' 'SEDE')
A                                      ALIAS(CODSEDE)
A                                      VALUES(10 30 40 52)

A            COMVEN        10S 0
A                                      TEXT('COMISION DE VENTAS')
A                                      COLHDG('COMISION' 'VENTAS')
A                                      ALIAS(COMISION)

A            CODGRU         2S 0
A                                      TEXT('CODIGO GRUPO')
A                                      COLHDG('CODIGO' 'GRUPO')
A                                      ALIAS(CODGRUP)
A                                      COMP(GE 1)

A            CUPPRO         2S 0
A                                      TEXT('CUPO PROGRAMADO')
A                                      COLHDG('CUPO' 'PROGRAMADO')
A                                      ALIAS(CUPPROG)
A                                      COMP(LE 40)

A            IDPROV         8S 0
A                                      TEXT('IDENTIFICACION PROVEEDOR')
A                                      COLHDG('IDENTIFICACION' 'PROVEEDOR')
A                                      ALIAS(IDPROVE)

A            CUPDIS         2S 0
A                                      TEXT('CUPO DISPONIBLE')
A                                      COLHDG('CUPO' 'DISPONIBLE')
A                                      ALIAS(CUPDISP)
A                                      COMP(LE 40)

A            SEMACA         1S 0
A                                      TEXT('SEMESTRE ACADEMICO')
A                                      COLHDG('SEMESTRE' 'ACADEMICO')
A                                      ALIAS(SEMEST)
A                                      VALUES(1 2)

A            ANOACA         4S 0
A                                      TEXT('ANO ACADEMICO')
A                                      COLHDG('ANO' 'ACADEMICO')
A                                      ALIAS(ANOACAD)
A                                      COMP(GE 1964)
```

## Guardar

Presionar `F3`.

Cuando pregunte si desea guardar, responder:

```txt
S
```

o:

```txt
Y
```

según el idioma configurado.

## Compilar

En PDM, sobre el miembro `REFER`, escribir opción:

```txt
14
```

Presionar `Enter`.

## Verificación

Revisar el spool. Debe salir gravedad `00` o máximo `10`.

También se puede verificar con:

```txt
WRKOBJ OBJ(SEGUNDA061/REFER) OBJTYPE(*FILE)
```

---

# Punto 4. Crear menú principal

## Objetivo

Crear un menú llamado `MENU01` con opciones para ingresar, consultar, modificar y retirar registros.

## Comando para trabajar con menús

En línea de comandos:

```txt
GO SEU
```

o ingresar por el menú de programación, según el ambiente.

También se puede trabajar con:

```txt
STRSDA
```

Luego elegir trabajo con menús.

## Crear menú

Nombre del menú:

```txt
MENU01
```

Título sugerido:

```txt
MENU PROGR ACADEMICA
```

Texto del cuerpo del menú:

```txt
Seleccione una de las siguientes opciones

1. Ingreso de asignaturas para programacion academica
2. Consulta de asignaturas en programacion academica
3. Modificacion de asignaturas en programacion academica
4. Retiro de asignaturas de programacion academica
5. Salir de la transaccion
```

## Definir mandatos del menú

En la opción de mandatos, configurar:

```txt
01    CALL PGM(SEGUNDA061/INGPROG)
02    CALL PGM(SEGUNDA061/CONPROG)
03    CALL PGM(SEGUNDA061/MODPROG)
04    CALL PGM(SEGUNDA061/RETPROG)
05    GO MAIN
```

## Agregar ayudas

Desde el trabajo con funciones de menú:

1. Entrar a trabajar con ayuda de menú.
2. Para cada opción crear un registro de ayuda.
3. Usar rango individual por opción.

Textos sugeridos:

```txt
01 - 01   Ayuda opcion 1 - Ingreso de asignaturas
02 - 02   Ayuda opcion 2 - Consulta de asignaturas
03 - 03   Ayuda opcion 3 - Modificacion de asignaturas
04 - 04   Ayuda opcion 4 - Retiro de asignaturas
05 - 05   Ayuda opcion 5 - Salir de la transaccion
```

## Verificación

Ejecutar:

```txt
GO MENU01
```

o situarse en el menú desde las opciones del sistema.

---

# Punto 5. Crear pantalla de ingreso

## Objetivo

Crear una pantalla que permita ingresar los datos de programación académica.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QMNUSRC)
```

Presionar `F6`.

Llenar:

```txt
Miembro fuente . . . . . . . . . . : INGDSPF
Tipo fuente . . . . . . . . . . . : DSPF
Texto . . . . . . . . . . . . . . : PANTALLA INGRESO PROGRAMACION
```

## DDS de la pantalla `INGDSPF`

```rpgle
A                                      DSPSIZ(24 80 *DS3)
A                                      CA03(03 'SALIR')
A                                      CA12(12 'CANCELAR')
A                                      CF10(10 'GRABAR')
A          R INGREG
A                                      TEXT('PANTALLA INGRESO PROGRAMACION')
A                                  1  2'INGRESO PROGRAMACION ACADEMICA'
A                                      DSPATR(HI)
A                                  2  2'ARCHIVO: PROGACA'
A                                      DSPATR(HI)
A                                  3  2'F3=SALIR'
A                                  3 20'F10=GRABAR'
A                                  3 40'F12=CANCELAR'

A                                  5  2'CODIGO ASIGNATURA . . . . . . .'
A            CODASI         8A  B  5 45

A                                  6  2'NOMBRE/CODIGO CIUDAD . . . . .'
A            NOMCIU         4S 0B  6 45

A                                  7  2'CODIGO SEDE 10 30 40 52 . . .'
A            CODSED         2S 0B  7 45

A                                  8  2'COMISION DE VENTAS . . . . . .'
A            COMVEN        10S 0B  8 45

A                                  9  2'CODIGO GRUPO 01 A 99 . . . . .'
A            CODGRU         2S 0B  9 45

A                                 10  2'CUPO PROGRAMADO MAXIMO 40 . .'
A            CUPPRO         2S 0B 10 45

A                                 11  2'IDENTIFICACION PROVEEDOR . . .'
A            IDPROV         8S 0B 11 45

A                                 12  2'CUPO DISPONIBLE MAXIMO 40 . .'
A            CUPDIS         2S 0B 12 45

A                                 13  2'SEMESTRE ACADEMICO 1 O 2 . .'
A            SEMACA         1S 0B 13 45

A                                 14  2'ANO ACADEMICO DESDE 1964 . .'
A            ANOACA         4S 0B 14 45

A                                 16  2'PRESIONE F10 PARA GRABAR EL REGISTRO.'
A                                      DSPATR(HI)
A                                 17  2'PRESIONE F3 PARA SALIR DE LA PANTALLA.'
A                                      DSPATR(HI)

A            MSG           70A  O 23  2
```

## Guardar y compilar

Guardar con `F3`, responder `S`.

Compilar desde PDM con opción:

```txt
14
```

o por comando:

```txt
CRTDSPF FILE(SEGUNDA061/INGDSPF) SRCFILE(SEGUNDA061/QMNUSRC) SRCMBR(INGDSPF)
```

## Probar pantalla

```txt
STRSDA
```

O usando la opción de probar archivo de pantalla si está disponible.

---

# Punto 6. Crear pantalla de consulta

## Objetivo

Crear una pantalla que permita visualizar datos del archivo de programación académica.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QMNUSRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : CONDSPF
Tipo fuente . . . . . . . . . . . : DSPF
Texto . . . . . . . . . . . . . . : PANTALLA CONSULTA PROGRAMACION
```

## DDS de la pantalla `CONDSPF`

```rpgle
A                                      DSPSIZ(24 80 *DS3)
A                                      CA03(03 'SALIR')
A                                      CA12(12 'CANCELAR')
A                                      CF10(10 'CONSULTAR')
A          R CONREG
A                                      TEXT('PANTALLA CONSULTA PROGRAMACION')
A                                  1  2'CONSULTA PROGRAMACION ACADEMICA'
A                                  2  2'ARCHIVO: PROGACA'
A                                  3  2'F3=SALIR'
A                                  3 20'F10=CONSULTAR'
A                                  3 45'F12=CANCELAR'

A                                  5  2'DIGITE LOS DATOS CLAVE PARA CONSULTAR:'

A                                  7  2'ANO ACADEMICO . . . . . . . .'
A            ANOACA         4S 0B  7 45

A                                  8  2'SEMESTRE ACADEMICO 1 O 2 . .'
A            SEMACA         1S 0B  8 45

A                                  9  2'CODIGO SEDE 10 30 40 52 . .'
A            CODSED         2S 0B  9 45

A                                 10  2'CODIGO ASIGNATURA . . . . . .'
A            CODASI         8A  B 10 45

A                                 11  2'CODIGO GRUPO 01 A 99 . . . .'
A            CODGRU         2S 0B 11 45

A                                 13  2'INFORMACION DEL REGISTRO ENCONTRADO:'

A                                 15  2'NOMBRE/CODIGO CIUDAD . . . .'
A            NOMCIU         4S 0O 15 45

A                                 16  2'COMISION DE VENTAS . . . . .'
A            COMVEN        10S 0O 16 45

A                                 17  2'CUPO PROGRAMADO . . . . . . .'
A            CUPPRO         2S 0O 17 45

A                                 18  2'IDENTIFICACION PROVEEDOR . .'
A            IDPROV         8S 0O 18 45

A                                 19  2'CUPO DISPONIBLE . . . . . . .'
A            CUPDIS         2S 0O 19 45

A                                 21  2'PRESIONE F10 PARA CONSULTAR EL REGISTRO.'
A                                 22  2'PRESIONE F3 PARA SALIR DE LA PANTALLA.'

A            MSG           70A  O 23  2
```

## Compilar

```txt
CRTDSPF FILE(SEGUNDA061/CONDSPF) SRCFILE(SEGUNDA061/QMNUSRC) SRCMBR(CONDSPF)
```

---

# Punto 7. Crear pantalla de modificación

## Objetivo

Crear una pantalla que permita modificar datos de programación académica.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QMNUSRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : MODDSPF
Tipo fuente . . . . . . . . . . . : DSPF
Texto . . . . . . . . . . . . . . : PANTALLA MODIFICACION PROGRAMACION
```

## DDS de la pantalla `MODDSPF`

```rpgle
A                                      DSPSIZ(24 80 *DS3)
A                                      CA03(03 'SALIR')
A                                      CA12(12 'CANCELAR')
A                                      CF10(10 'BUSCAR')
A                                      CF11(11 'MODIFICAR')
A          R MODREG
A                                      TEXT('PANTALLA MODIFICACION PROGRAMACION')
A                                  1  2'MODIFICACION PROGRAMACION ACADEMICA'
A                                  2  2'ARCHIVO: PROGACA'
A                                  3  2'F3=SALIR'
A                                  3 20'F10=BUSCAR'
A                                  3 40'F11=MODIFICAR'
A                                  3 62'F12=CANCELAR'

A                                  5  2'DIGITE LOS DATOS CLAVE DEL REGISTRO:'

A                                  7  2'ANO ACADEMICO . . . . . . . .'
A            ANOACA         4S 0B  7 45

A                                  8  2'SEMESTRE ACADEMICO 1 O 2 . .'
A            SEMACA         1S 0B  8 45

A                                  9  2'CODIGO SEDE 10 30 40 52 . .'
A            CODSED         2S 0B  9 45

A                                 10  2'CODIGO ASIGNATURA . . . . . .'
A            CODASI         8A  B 10 45

A                                 11  2'CODIGO GRUPO 01 A 99 . . . .'
A            CODGRU         2S 0B 11 45

A                                 13  2'DATOS QUE SE PUEDEN MODIFICAR:'

A                                 15  2'NOMBRE/CODIGO CIUDAD . . . .'
A            NOMCIU         4S 0B 15 45

A                                 16  2'COMISION DE VENTAS . . . . .'
A            COMVEN        10S 0B 16 45

A                                 17  2'CUPO PROGRAMADO MAXIMO 40 .'
A            CUPPRO         2S 0B 17 45

A                                 18  2'IDENTIFICACION PROVEEDOR . .'
A            IDPROV         8S 0B 18 45

A                                 19  2'CUPO DISPONIBLE MAXIMO 40 .'
A            CUPDIS         2S 0B 19 45

A                                 21  2'F10 BUSCA EL REGISTRO. F11 GUARDA LA MODIFICACION.'
A                                 22  2'PRESIONE F3 PARA SALIR DE LA PANTALLA.'

A            MSG           70A  O 23  2
```

## Compilar

```txt
CRTDSPF FILE(SEGUNDA061/MODDSPF) SRCFILE(SEGUNDA061/QMNUSRC) SRCMBR(MODDSPF)
```

---

# Punto 8. Crear pantalla de retiro

## Objetivo

Crear una pantalla que permita retirar registros del archivo de programación académica.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QMNUSRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : RETDSPF
Tipo fuente . . . . . . . . . . . : DSPF
Texto . . . . . . . . . . . . . . : PANTALLA RETIRO PROGRAMACION
```

## DDS de la pantalla `RETDSPF`

```rpgle
A                                      DSPSIZ(24 80 *DS3)
A                                      CA03(03 'SALIR')
A                                      CA12(12 'CANCELAR')
A                                      CF10(10 'BUSCAR')
A                                      CF11(11 'RETIRAR')
A          R RETREG
A                                      TEXT('PANTALLA RETIRO PROGRAMACION')
A                                  1  2'RETIRO PROGRAMACION ACADEMICA'
A                                  2  2'ARCHIVO: PROGACA'
A                                  3  2'F3=SALIR'
A                                  3 20'F10=BUSCAR'
A                                  3 40'F11=RETIRAR'
A                                  3 62'F12=CANCELAR'

A                                  5  2'DATOS DEL REGISTRO A RETIRAR:'

A                                  7  2'ANO ACADEMICO . . . . . . . .'
A            ANOACA         4S 0O  7 45

A                                  8  2'SEMESTRE ACADEMICO . . . . .'
A            SEMACA         1S 0O  8 45

A                                  9  2'CODIGO SEDE . . . . . . . . .'
A            CODSED         2S 0O  9 45

A                                 10  2'CODIGO ASIGNATURA . . . . . .'
A            CODASI         8A  O 10 45

A                                 11  2'CODIGO GRUPO . . . . . . . .'
A            CODGRU         2S 0O 11 45

A                                 13  2'NOMBRE/CODIGO CIUDAD . . . .'
A            NOMCIU         4S 0O 13 45

A                                 14  2'COMISION DE VENTAS . . . . .'
A            COMVEN        10S 0O 14 45

A                                 15  2'CUPO PROGRAMADO . . . . . . .'
A            CUPPRO         2S 0O 15 45

A                                 16  2'IDENTIFICACION PROVEEDOR . .'
A            IDPROV         8S 0O 16 45

A                                 17  2'CUPO DISPONIBLE . . . . . . .'
A            CUPDIS         2S 0O 17 45

A                                 20  2'PRESIONE F11 PARA RETIRAR EL REGISTRO.'
A                                 21  2'PRESIONE F3 PARA SALIR DE LA PANTALLA.'

A            MSG           70A  O 23  2
```

## Compilar

```txt
CRTDSPF FILE(SEGUNDA061/RETDSPF) SRCFILE(SEGUNDA061/QMNUSRC) SRCMBR(RETDSPF)
```

---

# Punto 9. Crear programa RPG IV para ingresar registros

## Objetivo

Crear un programa RPG IV que permita ingresar registros al archivo `PROGACA`.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QRPGLESRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : INGPROG
Tipo fuente . . . . . . . . . . . : RPGLE
Texto . . . . . . . . . . . . . . : PROGRAMA INGRESO PROGRAMACION ACADEMICA
```

## Código final que funcionó

```rpgle
FPROGACA   UF A E           K DISK
FINGDSPF   CF   E             WORKSTN
C                   DOW       *IN03 = *OFF AND *IN12 = *OFF
C                   EXFMT     INGREG
C                   CLEAR                   MSG
C                   IF        *IN10 = *ON
C                   IF        CODASI = *BLANKS
C                   EVAL      MSG = 'Debe digitar codigo asignatura.'
C                   ELSE
C                   WRITE     PROGACAR
C                   LEAVE
C                   ENDIF
C                   ENDIF
C                   ENDDO
C                   EVAL      *INLR = *ON
C                   RETURN
```

## Explicación

- `FPROGACA UF A E K DISK`: abre el archivo físico para actualización y adición.
- `FINGDSPF CF E WORKSTN`: abre la pantalla de ingreso.
- `EXFMT INGREG`: muestra la pantalla y espera datos.
- `WRITE PROGACAR`: graba el registro.
- `LEAVE`: sale del ciclo para evitar que se grabe duplicado.
- `*INLR = *ON`: finaliza el programa.

## Compilar

Opción `14` sobre `INGPROG`.

O por comando:

```txt
CRTBNDRPG PGM(SEGUNDA061/INGPROG) SRCFILE(SEGUNDA061/QRPGLESRC) SRCMBR(INGPROG) DBGVIEW(*SOURCE)
```

## Probar

```txt
CALL PGM(SEGUNDA061/INGPROG)
```

Ingresar datos de prueba:

```txt
CODASI: MAT001
NOMCIU: 0501
CODSED: 10
COMVEN: 150000
CODGRU: 01
CUPPRO: 30
IDPROV: 12345678
CUPDIS: 25
SEMACA: 1
ANOACA: 2026
```

Presionar `F10`.

## Verificar

```txt
DSPPFM FILE(SEGUNDA061/PROGACA)
```

Debe aparecer un registro.

---

# Punto 10. Crear programa RPG IV para consultar registros

## Objetivo

Crear un programa RPG IV que consulte registros del archivo `PROGACA`.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QRPGLESRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : CONPROG
Tipo fuente . . . . . . . . . . . : RPGLE
Texto . . . . . . . . . . . . . . : PROGRAMA CONSULTA PROGRAMACION ACADEMICA
```

## Código final que funcionó

```rpgle
FPROGACA   IF   E           K DISK    EXTFILE('SEGUNDA061/PROGACA')
FCONDSPF   CF   E             WORKSTN EXTFILE('SEGUNDA061/CONDSPF')
C                   DOW       *IN03 = *OFF AND *IN12 = *OFF
C                   READ      PROGACAR                               90
C                   IF        *IN90 = *ON
C                   EVAL      MSG = 'No hay registros para consultar.'
C                   EXFMT     CONREG
C                   LEAVE
C                   ELSE
C                   EVAL      MSG = 'Registro encontrado.'
C                   EXFMT     CONREG
C                   ENDIF
C                   ENDDO
C                   EVAL      *INLR = *ON
C                   RETURN
```

## Explicación

- `READ PROGACAR 90`: lee secuencialmente el archivo.
- `*IN90 = *ON`: indica que no hay más registros.
- `EXFMT CONREG`: muestra la pantalla de consulta.
- Esta versión consulta registros de forma secuencial. Si hay varios, se pueden ir mostrando uno por uno.

## Compilar

```txt
CRTBNDRPG PGM(SEGUNDA061/CONPROG) SRCFILE(SEGUNDA061/QRPGLESRC) SRCMBR(CONPROG) DBGVIEW(*SOURCE)
```

o opción `14`.

## Probar

```txt
CALL PGM(SEGUNDA061/CONPROG)
```

Si hay registros en `PROGACA`, se muestra el registro en pantalla.

---

# Punto 11. Crear programa RPG IV para modificar registros

## Objetivo

Crear un programa RPG IV que permita modificar registros del archivo `PROGACA`.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QRPGLESRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : MODPROG
Tipo fuente . . . . . . . . . . . : RPGLE
Texto . . . . . . . . . . . . . . : PROGRAMA MODIFICA PROGRAMACION ACADEMICA
```

## Código final

```rpgle
FPROGACA   UF   E           K DISK    EXTFILE('SEGUNDA061/PROGACA')
FMODDSPF   CF   E             WORKSTN EXTFILE('SEGUNDA061/MODDSPF')
C                   DOW       *IN03 = *OFF AND *IN12 = *OFF
C                   READ      PROGACAR                               90
C                   IF        *IN90 = *ON
C                   EVAL      MSG = 'No hay registros para modificar.'
C                   EXFMT     MODREG
C                   LEAVE
C                   ELSE
C                   EVAL      MSG = 'Modifique datos y presione F11.'
C                   EXFMT     MODREG
C                   IF        *IN11 = *ON
C                   UPDATE    PROGACAR
C                   LEAVE
C                   ENDIF
C                   ENDIF
C                   ENDDO
C                   EVAL      *INLR = *ON
C                   RETURN
```

## Explicación

- `READ PROGACAR`: lee el registro.
- `EXFMT MODREG`: muestra la pantalla con los datos.
- La pantalla usa `F11=MODIFICAR`.
- `IF *IN11 = *ON`: valida que se haya presionado F11.
- `UPDATE PROGACAR`: actualiza el registro leído.
- `LEAVE`: sale para evitar repetir el ciclo.

## Compilar

```txt
CRTBNDRPG PGM(SEGUNDA061/MODPROG) SRCFILE(SEGUNDA061/QRPGLESRC) SRCMBR(MODPROG) DBGVIEW(*SOURCE)
```

o usar opción `14`.

## Probar

```txt
CALL PGM(SEGUNDA061/MODPROG)
```

Cambiar un dato modificable, por ejemplo:

```txt
CUPDIS: 20
```

Presionar `F11`.

Verificar:

```txt
DSPPFM FILE(SEGUNDA061/PROGACA)
```

El registro debe mostrar el dato actualizado.

---

# Punto 12. Crear programa RPG IV para retirar registros

## Objetivo

Crear un programa RPG IV que permita retirar, es decir, eliminar registros del archivo `PROGACA`.

## Crear miembro

```txt
WRKMBRPDM FILE(SEGUNDA061/QRPGLESRC)
```

Presionar `F6`.

```txt
Miembro fuente . . . . . . . . . . : RETPROG
Tipo fuente . . . . . . . . . . . : RPGLE
Texto . . . . . . . . . . . . . . : PROGRAMA RETIRO PROGRAMACION ACADEMICA
```

## Código final

```rpgle
FPROGACA   UF   E           K DISK    EXTFILE('SEGUNDA061/PROGACA')
FRETDSPF   CF   E             WORKSTN EXTFILE('SEGUNDA061/RETDSPF')
C                   DOW       *IN03 = *OFF AND *IN12 = *OFF
C                   READ      PROGACAR                               90
C                   IF        *IN90 = *ON
C                   EVAL      MSG = 'No hay registros para retirar.'
C                   EXFMT     RETREG
C                   LEAVE
C                   ELSE
C                   EVAL      MSG = 'Revise datos y presione F11.'
C                   EXFMT     RETREG
C                   IF        *IN11 = *ON
C                   DELETE    PROGACAR
C                   LEAVE
C                   ENDIF
C                   ENDIF
C                   ENDDO
C                   EVAL      *INLR = *ON
C                   RETURN
```

## Explicación

- `READ PROGACAR`: lee el registro.
- `EXFMT RETREG`: muestra el registro en la pantalla de retiro.
- `IF *IN11 = *ON`: valida que se haya presionado F11.
- `DELETE PROGACAR`: elimina el registro leído.
- `LEAVE`: sale del ciclo.

## Compilar

```txt
CRTBNDRPG PGM(SEGUNDA061/RETPROG) SRCFILE(SEGUNDA061/QRPGLESRC) SRCMBR(RETPROG) DBGVIEW(*SOURCE)
```

o usar opción `14`.

## Probar

Antes de retirar, revisar:

```txt
DSPPFM FILE(SEGUNDA061/PROGACA)
```

Ejecutar:

```txt
CALL PGM(SEGUNDA061/RETPROG)
```

Cuando muestre el registro, presionar `F11`.

Luego verificar:

```txt
DSPPFM FILE(SEGUNDA061/PROGACA)
```

Si solo había un registro, debe salir:

```txt
***** FIN DE DATOS *****
```

---

# Comandos generales de verificación

## Ver archivos fuente

```txt
WRKMBRPDM FILE(SEGUNDA061/QDDSSRC)
WRKMBRPDM FILE(SEGUNDA061/QMNUSRC)
WRKMBRPDM FILE(SEGUNDA061/QRPGLESRC)
```

## Ver objetos compilados

```txt
WRKOBJ OBJ(SEGUNDA061/PROGACA) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/INGDSPF) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/CONDSPF) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/MODDSPF) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/RETDSPF) OBJTYPE(*FILE)
WRKOBJ OBJ(SEGUNDA061/INGPROG) OBJTYPE(*PGM)
WRKOBJ OBJ(SEGUNDA061/CONPROG) OBJTYPE(*PGM)
WRKOBJ OBJ(SEGUNDA061/MODPROG) OBJTYPE(*PGM)
WRKOBJ OBJ(SEGUNDA061/RETPROG) OBJTYPE(*PGM)
```

## Ver datos del archivo físico

```txt
DSPPFM FILE(SEGUNDA061/PROGACA)
```

## Limpiar datos de prueba

```txt
CLRPFM FILE(SEGUNDA061/PROGACA)
```

## Ejecutar programas

```txt
CALL PGM(SEGUNDA061/INGPROG)
CALL PGM(SEGUNDA061/CONPROG)
CALL PGM(SEGUNDA061/MODPROG)
CALL PGM(SEGUNDA061/RETPROG)
```

---

# Evidencias sugeridas para entregar

Para cada punto se recomienda tomar capturas de:

1. Código fuente del miembro.
2. Compilación con gravedad `00`.
3. Ejecución del programa o prueba de pantalla.
4. Resultado en `DSPPFM` cuando aplique.
5. Menú funcionando con las opciones configuradas.

---

# Resumen de objetos creados

| Punto | Objeto / miembro | Tipo | Descripción |
|---|---|---|---|
| 1 | SEGUNDA061 | Biblioteca | Biblioteca del trabajo |
| 2 | QDDSSRC | Source file | Fuente DDS |
| 2 | QMNUSRC | Source file | Fuente pantallas y menú |
| 2 | QRPGLESRC | Source file | Fuente RPG |
| 3 | REFER | PF | Archivo de referencia |
| 4 | MENU01 | Menú | Menú principal |
| 5 | INGDSPF | DSPF | Pantalla de ingreso |
| 6 | CONDSPF | DSPF | Pantalla de consulta |
| 7 | MODDSPF | DSPF | Pantalla de modificación |
| 8 | RETDSPF | DSPF | Pantalla de retiro |
| 9 | INGPROG | RPGLE | Programa de ingreso |
| 10 | CONPROG | RPGLE | Programa de consulta |
| 11 | MODPROG | RPGLE | Programa de modificación |
| 12 | RETPROG | RPGLE | Programa de retiro |

---

# Observaciones finales

- Los programas finales se realizaron en RPG IV formato fijo, porque fue el formato compatible con el entorno TN5250 usado.
- En los programas de consulta, modificación y retiro se utilizó lectura secuencial (`READ`) para asegurar compilación y funcionamiento.
- En ingreso, modificación y retiro se usó `LEAVE` después de ejecutar la acción para evitar duplicados o repeticiones.
- La consulta muestra registros de forma secuencial. Si se requiere consulta exacta por clave, se puede ajustar posteriormente usando `CHAIN`.
