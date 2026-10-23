[README.md](https://github.com/user-attachments/files/33883797/README.md)
# conciliacion# Concilia: conciliación bancaria

Herramienta interna de **EGCI Formación Fermetal** que compara el **extracto del banco** con el **reporte del sistema contable**, ambos en Excel. Empareja los movimientos automáticamente y separa los que no cuadran para revisarlos.

- **Versión en línea (recomendada):** https://claude.ai/artifact/GpvJAUrwVwTkRSZJHGuQq1
- **Versión independiente:** el archivo `index.html` de esta carpeta.
- **Documento de requisitos:** `prd-conciliacion-bancaria.md`, en el proyecto "APLICACION DE CONCILIACION" de Claude.

---

## 1. Qué problema resuelve

Hoy la conciliación de **16 entidades bancarias** se hace a mano, línea por línea, con entre 500 y 2.000 movimientos al mes por entidad. Eso toma unos **20 días de trabajo al mes**. Concilia automatiza la comparación para que el equipo solo revise las excepciones. La meta es **3 días o menos**.

## 2. Cómo se usa

1. Elige el **Banco** (la primera vez, regístralo con **+ Agregar banco**).
2. Elige el **Mes** que vas a conciliar. Si no lo eliges, se propone según las fechas del extracto.
3. Sube el **Excel del banco** y el **Excel del sistema** (`.xlsx`, `.xls` o `.csv`). Puedes arrastrarlos o hacer clic.
4. Revisa en **Columnas detectadas** que la fecha, la referencia y el monto sean los correctos. El formato queda guardado para ese banco.
5. Pulsa **Conciliar** y revisa las pestañas:
   - **Conciliados:** exactos y agrupados.
   - **Diferencia de monto:** misma referencia y fecha, pero distinto monto.
   - **Solo en banco** y **Solo en sistema:** movimientos sin pareja.
   - **Sugerencias:** posibles parejas para que las confirmes.
6. Marca **Revisado** y escribe comentarios en las excepciones.
7. Pulsa **Descargar Excel**. Al hacerlo, la conciliación queda registrada en el **Historial**.
8. Usa **Nueva conciliación** para pasar al siguiente banco.

## 3. Reglas de conciliación

| Caso | Qué hace la herramienta |
|---|---|
| Misma referencia, fechas a ±4 días (ajustable) y mismo monto | **Conciliado (exacto)** |
| Un movimiento corresponde a varios del otro archivo con la misma referencia, y la suma coincide | **Conciliado (agrupado)** |
| Misma referencia y fecha, pero distinto monto | **Diferencia de monto** |
| Referencias escritas distinto ("TRF-00123" y "123") | Se ignoran guiones, espacios y ceros a la izquierda. Si aun así no coinciden, se **sugiere** la pareja por monto y fecha. |
| Varias facturas con referencias propias cuya suma da el depósito | Se **sugiere** la combinación. |
| Depósito del banco sin conciliar | Etiqueta **"Depósito sin identificar"**. No la llevan los intereses. |
| Referencia repetida o fecha fuera de la tolerancia | Queda pendiente con una nota que explica el motivo. |

Las sugerencias **nunca** se aplican solas: siempre las confirma una persona. También se puede emparejar a mano, marcando movimientos en las pestañas "Solo en banco" y "Solo en sistema", y deshacer cualquier emparejamiento.

**Filtro por mes (opcional):** la casilla *"Conciliar solo los movimientos del mes elegido"* ignora lo que esté fuera del mes. Del sistema se toman también los movimientos de hasta 4 días antes o después del mes, para no perder parejas en el cambio de mes. Si no encuentran pareja, se descartan.

## 4. Botones principales

| Botón | Función |
|---|---|
| **+ Agregar banco** | Registra una entidad (por ejemplo, las 16 del equipo). |
| **Mes** | Define el mes conciliado. Se usa en el Excel, en el nombre del archivo y en el historial. |
| **Nueva conciliación** | Limpia la pantalla. Pide confirmar si hay trabajo en curso. |
| **Historial** | Muestra las conciliaciones registradas, filtrables por mes y banco, y el estado del mes de cada banco ("Conciliado" o "Pendiente"). |
| **Pedir más sugerencias a Claude** | Solo en la versión en línea. Envía los movimientos pendientes a Claude para que proponga parejas. |
| **Descargar Excel** | Genera el archivo con las hojas Resumen, Conciliados, Diferencia de monto, Solo en banco y Solo en sistema. |

## 5. Versión en línea y versión independiente

| | En línea (claude.ai) | `index.html` (independiente) |
|---|---|---|
| Cómo se abre | Con el link. Hay que compartirlo desde el menú *Compartir* para que otra persona lo use. | Doble clic en el archivo, o se publica en un servidor interno. |
| Bancos e historial | Se guardan en el artefacto y los ven todas las personas con acceso. | Se guardan **solo en ese navegador y ese computador** (localStorage). Si se borran los datos del navegador, se pierden. |
| Sugerencias de Claude | Disponibles. | No disponibles. Sí funcionan las sugerencias por monto y por suma. |
| Descarga del Excel | Pide confirmación de Claude. | Directa. |
| Internet | Necesario. | Necesario para cargar la librería de Excel (SheetJS) y las fuentes. |

**Recomendación:** que las 2 usuarias trabajen con la **versión en línea**, porque así comparten la lista de bancos y el historial.

## 6. Privacidad y datos

- Los Excel se procesan **dentro del navegador**. Los movimientos **no se guardan** en ningún lado.
- Solo se guardan el nombre de cada banco, su formato de columnas y un **resumen** de cada conciliación (conteos, totales y % automático).
- A Claude se le envían datos **solo** cuando la persona pulsa "Pedir más sugerencias a Claude". En ese caso se envían los pendientes: fecha, referencia, monto y descripción.
- **Pendiente:** confirmar la política interna de EGCI sobre el uso de IA con datos financieros.

## 7. Detalles técnicos

- Un único archivo HTML con CSS y JavaScript integrados. No necesita instalación ni servidor propio.
- Librería externa: [SheetJS](https://sheetjs.com/) 0.18.5 (cdnjs), para leer y generar los Excel.
- Formatos admitidos:
  - **Fechas:** dd/mm/aaaa, aaaa-mm-dd, fechas de Excel y mm/dd/aaaa (se elige en las reglas).
  - **Montos:** 1.234,56 o 1,234.56, negativos con signo o entre paréntesis, y una columna de monto o dos (débito y crédito).
- Detecta automáticamente la fila de encabezados, aunque el extracto tenga títulos arriba.
- Rendimiento probado: 2.000 movimientos se concilian en menos de 1 segundo.

**Estructura del código**, en el `<script>` de `index.html`:

| Sección | Contenido |
|---|---|
| `ENGINE` | Lectura de fechas y montos, detección de columnas, emparejamiento y sugerencias. |
| `Datos de ejemplo` | Conciliación de prueba con datos inventados que se muestra al abrir. |
| `Archivos`, `Conciliar`, `Render`, `Eventos` | Interfaz. |
| `Mes`, `Nueva conciliación e historial`, `Exportar`, `Bancos` | Funciones de cada botón. |
| `Modo independiente` | Reemplazos locales cuando la página se abre fuera de claude.ai. |

## 8. Problemas frecuentes

| Problema | Solución |
|---|---|
| "No se pudo leer este archivo" | Verifica que sea .xlsx, .xls o .csv y que no esté protegido con contraseña. |
| Falta la fecha, la referencia o el monto | Abre **Columnas detectadas** y elige la columna correcta. Si el archivo tiene varias hojas, elige la hoja. |
| Fechas mal leídas (día y mes invertidos) | Cambia "Fechas escritas como" a mes/día/año. |
| Casi nada se concilia | Revisa que la columna de referencia sea la misma en ambos archivos y prueba con una tolerancia mayor. |
| El historial está vacío | Se registra al **descargar el Excel**, con un banco elegido. La conciliación de ejemplo no se registra. |

## 9. Versiones

| Versión | Fecha | Cambios |
|---|---|---|
| 1 | 22/10/2026 | Conciliación por referencia y fecha (±4 días), diferencias, uno a muchos, sugerencias, formato por banco y exportación a Excel. |
| 2 | 23/10/2026 | Etiqueta "Depósito sin identificar". |
| 3 | 23/10/2026 | Botones "Nueva conciliación" e "Historial". |
| 4 | 23/10/2026 | Selector "Mes", filtro por mes y estado mensual de los bancos. |
| 5 | 23/10/2026 | Modo independiente (`index.html` funciona fuera de claude.ai). |

---

*Preparado por Anais (EGCI Formación Fermetal) con acompañamiento de Claude.*
