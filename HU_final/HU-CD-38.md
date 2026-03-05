# HU-CD-38: Reporte de Facturas PSC Asociadas a Documento de Transporte

**Proceso:** CROSSDOCKING
**Subproceso:** REPORTE FACTURAS POR GUÍA
**Requerimiento Original:** REQ-38 — El programa debe contar con una opción que permita generar un reporte de facturas PSC creadas por documento de transporte.
**Versión:** 2.0
**Fecha de revisión:** 2026-03-04
**Estado:** Revisada con base en sesiones de levantamiento (25-Feb, 27-Feb y 02-Mar-2026)

---

## Historia de Usuario

### Como:
Analista de Comercio Exterior o del Área Financiera (Commex / Tesorería)

### Quiero:
Generar un reporte consolidado de todas las facturas PSC (INV) asociadas a un documento de transporte (Guía), con posibilidad de exportarlo en Excel o PDF.

### Para:
Tener trazabilidad completa del embarque, facilitar conciliaciones financieras, soportar auditorías contables y servir como insumo para los procesos aduaneros posteriores (DIM, conciliación con agencia, costeo en D365).

---

## Descripción Funcional

El sistema debe permitir:

1. **Seleccionar** el número de guía a consultar (búsqueda por filtros).
2. **Generar** el reporte con todas las facturas PSC asociadas a esa guía.
3. **Exportar o visualizar** el resultado en:

| Formato | Descripción |
|---------|-------------|
| Vista en pantalla | Tabla paginada, consultable en el sistema |
| Excel (.xlsx) | Descarga para análisis o envío (Formato propuesto por Fabián Barragán) |
| PDF | Descarga para archivo formal o presentación |

---

## Campos del Reporte

| # | Campo | Descripción |
|---|-------|-------------|
| 1 | Número de Guía | Identificador del documento de transporte |
| 2 | Compañía | Gecolsa / Relianz |
| 3 | Dealer | Destinatario logístico (HU-CD-35) |
| 4 | Fecha Guía | Fecha de generación del transport document |
| 5 | Orden de Compra | OC asociada a la línea (Interfaz 3) |
| 6 | Landed Cost | Costo puesto en destino (si aplica) |
| 7 | Factura Z95 | Factura de Caterpillar relacionada |
| 8 | Factura INV PSC | Factura de Panamerican asociada |
| 9 | Serial | Número de serie del equipo/componente |
| 10 | Referencia | Código de la pieza o repuesto |
| 11 | País de Origen | Origen por ítem (DIAN) |
| 12 | Suplidor | Proveedor de la mercancía |
| 13 | Marca | Marca comercial del producto |
| 14 | Cantidad | Unidades incluidas en la guía |
| 15 | Valor Unitario | Precio por unidad (USD) |
| 16 | Valor FOB | Valor FOB total de la línea |
| 17 | Tipo de mercancía | Nueva / Remanufacturada |
| 18 | Año Proceso | Año de proceso activo (HU-CD-30) |
| 19 | Estado del DO | Estado en Centro de Despacho (HU-CD-37) |
| 20 | Estado del DIM | Estado en Declaración (HU-CD-39) |

> [!TIP]
> El reporte debe incluir dinámicamente las casillas de la **página 3 de la cartilla de la DIAN** para la declaración de importación (Formulario 500) según la necesidad de la consulta.

---

## Filtros de Consulta

El sistema debe permitir filtrar por:

| Filtro | Tipo |
|--------|------|
| Número de Guía | Texto libre / búsqueda exacta |
| Año Proceso | Lista desplegable (HU-CD-30) |
| Compañía | Lista desplegable (Gecolsa / Relianz) |
| Dealer | Lista desplegable |
| Fecha de guía | Rango de fechas |
| Estado | Lista (Generada / En DO / Con DIM) |

---

## Consideraciones sobre Remanufacturados

- Las referencias remanufacturadas se presentan en el reporte con su valor total consolidado (Premium + Core = Valor total único), de forma consistente con la presentación en el SII (ver HU-CD-27).
- El campo "Tipo de mercancía" debe indicar claramente "Remanufacturada" para estas referencias.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Reporte de guía completa
```gherkin
Dado que existe una guía G001 con 3 facturas INV asociadas
Cuando el analista genere el reporte para G001
Entonces el sistema debe listar las 3 facturas con todos los campos requeridos.
```

### Escenario 2 – Reporte de guía parcial
```gherkin
Dado que la guía fue generada con cantidades parciales de una Z95
Cuando se genere el reporte
Entonces debe mostrar únicamente las cantidades efectivamente incluidas en la guía
  Y no mostrar el saldo restante de la Z95.
```

### Escenario 3 – Exportación a Excel
```gherkin
Dado que el reporte está generado en pantalla
Cuando el usuario haga clic en "Exportar Excel"
Entonces el sistema debe descargar el archivo .xlsx con todos los datos del reporte.
```

### Escenario 4 – Guía inexistente
```gherkin
Dado que el usuario consulta el número de guía G999 que no existe
Cuando ejecute la búsqueda
Entonces el sistema debe mostrar el mensaje: "No se encontró ninguna guía con ese número"
  Y no mostrar tabla vacía sin contexto.
```

### Escenario 5 – Filtro por estado
```gherkin
Dado que existen múltiples guías en estado "En DO" y "Con DIM"
Cuando el usuario filtre por estado = "En DO"
Entonces el sistema debe mostrar solo las guías en ese estado.
```

---

## Reglas de Negocio

| ID | Regla |
|----|-------|
| RN-01 | El reporte solo incluye facturas realmente asociadas a la guía seleccionada. |
| RN-02 | No se incluyen facturas pendientes o no asociadas a ninguna guía. |
| RN-03 | El reporte no modifica ningún dato del sistema. |
| RN-04 | Cada generación del reporte queda registrada en el log de auditoría. |
| RN-05 | El sistema debe soportar grandes volúmenes sin degradar el rendimiento. |
| RN-06 | El reporte de guía parcial solo muestra las cantidades efectivamente incluidas. |

---

## Integración con Procesos Posteriores

Este reporte es insumo para:

| Proceso | HU | Uso |
|---------|----|-----|
| Generación de DIM | HU-CD-39 | Verificación final antes de declarar |
| Conciliación aduanera | HU-CD-40 | Cruce con información de la agencia |
| Migración a D365 | HU-CD-41 | Costeo e integración contable |

---

## Pendientes / Compromisos de Sesión

| # | Compromiso | Responsable |
|---|-----------|-------------|
| 1 | Confirmar si el reporte puede generarse también por **rango de fechas** (sin especificar guía específica). | Commex / Financiero |
| 2 | Definir si el formato PDF debe seguir una plantilla corporativa específica. | Commex / TI |
