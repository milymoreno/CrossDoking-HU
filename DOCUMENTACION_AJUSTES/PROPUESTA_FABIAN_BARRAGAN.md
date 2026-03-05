# Propuesta de Ajuste Técnico: Compromisos Fabián Barragán

Basado en la nota de reunión (Responsable: Fabián Barragán), se proponen los siguientes ajustes técnicos para **HU-CD-26** y **HU-CD-27**:

---

## 1. Formato Excel Resultado (Validación RQ 26/27)

El sistema permitirá exportar la vista de relación. Los datos se toman de las 3 interfaces cargadas en SII.

**Filtros de Exportación:**
- Número Z95
- Número INV
- Año (Cruzado con Año Proceso)

**Columnas del Excel:**
| Columna | Origen | Descripción |
|---------|--------|-------------|
| Compañía | Interfaz INV | Gecolsa / Relianz |
| Factura Z95 | Interfaz Z95 | Número de factura CAT |
| Año Z95 | Interfaz Z95 | Año de emisión CAT |
| Factura INV | Interfaz INV | Número de factura Panamerican |
| Orden de Compra | Interfaz OC | OC asociada a la línea |
| Referencia | Interfaz Z95/INV | Código de parte (200 chars) |
| Cantidad Facturada | Interfaz Z95/INV | Unidades (1 para REMAN) |
| Valor FOB USD | Interfaz Z95 | Valor CAT |
| Valor SII (Total) | Interfaz INV | Valor PSC (Premium + Core para REMAN) |
| Estado Relación | Sistema | ✅ Relacionada / ⚠️ Pendiente INV / ❌ Inconsistente |

---

## 2. Estructura de Módulos y Roles (Crossdocking)

Basado en el sistema actual, se propone el siguiente mapeo para el nuevo SII 2.0:

| Módulo Actual | Opción | Nuevo Nombre | Nuevo Rol | Datos Relevantes |
|---------------|--------|--------------|-----------|------------------|
| Facturación | Relación Z95-INV | **Monitor de Facturación CD** | Analista Commex CD | Cruce 3 interfaces, Alerta REMAN |
| Consolidación | Carga DHL | **Gestor de Guías DHL** | Operador Logístico CD | Bot DHL, Carga Manual |
| Discrepancias | Gestión Diferencias | **Mesa de Discrepancias** | Auditor Commex | Reporte Excel, Ajuste INV |
| Nacionalización| Trámite DIM | **Centro de Despacho (DO)** | Coordinador Aduanero | Envío a SIACO (FileZilla) |

---

## 3. Capacidad y Concurrencia

Se ajustará la arquitectura del SII 2.0 para soportar:
- **Usuarios Totales:** ~500 usuarios/roles configurados.
- **Concurrencia Pico:** **150 usuarios simultáneos** operando en los monitores de facturación y carga de guías.
- **Escalabilidad:** El modelo PostgreSQL (22 tablas) y las interfaces Z95/INV están diseñados para no generar bloqueos de tabla (*row-level locking*) incluso con esta volumetría.

---

**¿Estás de acuerdo con este detalle técnico para proceder a los ajustes de las HUs y el Modelo SQL?**
Puntos clave para tu revisión: filtros de año, nombres de módulos y niveles de concurrencia.
