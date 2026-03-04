# HU-CD-27 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-27 – Recepción y Visualización Facturación Z95 vs INV PSC
**Fecha:** 2026-03-04

> Este glosario explica en lenguaje sencillo los términos de negocio específicos de esta historia de usuario. Los términos comunes ya definidos en el glosario de HU-CD-26 (Z95, DHL, Dealer, Dynamics, SII, etc.) no se repiten aquí.

---

## C

**Cargo Core**
Es un cobro adicional que aplica Caterpillar a los productos remanufacturados. El "core" es la pieza usada que el cliente debe devolver a CAT cuando recibe la nueva. Este cargo es como una garantía del retorno de la pieza. En Dynamics, este cargo aparece como una **línea separada** de la factura, pero en el SII debe sumarse al valor principal (Premium) para mostrarse como un único valor.

---

**Commex (Comercio Exterior)**
Es el área o equipo encargado de gestionar todos los trámites relacionados con la importación de mercancía: documentos, aranceles, aduanas, etc. En el proceso CrossDocking, el equipo de Commex es quien consulta la pantalla de relación Z95 ↔ INV para verificar que todo esté en orden antes de generar las guías.

---

## I

**INV (Invoice / Factura Intercompañía)**
Es la factura que **Panamerican (PSC) le emite a Gecolsa o Relianz** por los productos que vendió. "INV" viene de la palabra inglesa *Invoice* (factura). Es importante notar que en versiones anteriores de documentos y conversaciones se usaba el término **"INB"**, que es incorrecto. El término oficial y correcto es **INV**.

**Ejemplo:**
- CAT vende a Panamerican → Z95
- Panamerican vende a Gecolsa → **INV**

---

**Interfaz (en este contexto)**
No es una pantalla, sino un **proceso automático de transferencia de datos** entre Dynamics y el SII. Para esta HU, hay **3 interfaces**:

| Interfaz | Qué transfiere | De quién | A quién |
|----------|---------------|----------|---------|
| Interfaz 1 | Facturas Z95 | CAT | SII |
| Interfaz 2 | Facturas INV con Z95 asociada | Panamerican | SII |
| Interfaz 3 | Órdenes de Compra | Gecolsa/Relianz/Panamerican | SII |

Piénsalas como tres "mensajeros" que llevan información de Dynamics al SII en horarios programados.

---

## J

**JOIN (técnico / base de datos)**
En términos de base de datos, un JOIN es una operación que **combina filas de dos o más tablas** usando un campo común. En esta HU, el sistema hace un JOIN entre la tabla de Z95, la tabla de INV y la tabla de OC, usando el número de Z95 como llave, para mostrarlo todo en una sola pantalla. Para los no técnicos: imagina que tienes tres listas (Z95, INV, OC) y las pegas en una sola tabla alineando los que comparten el mismo número de factura Z95.

---

## O

**OC (Orden de Compra)**
Documento formal que se emite para iniciar una compra. En CrossDocking hay dos niveles:
- Gecolsa/Relianz → Panamerican (OC intercompañía)
- Panamerican → Caterpillar (OC externa)

La Interfaz 3 trae las OC al SII para usarlas en la validación de discrepancias.

---

## P

**Panamerican (PSC)**
Es la empresa intermediaria del grupo que actúa como distribuidor entre Caterpillar y las compañías locales (Gecolsa y Relianz). "PSC" son las siglas de Panamerican y es el emisor de las facturas **INV**. También se le llama "compañía 500" dentro de Dynamics.

**Premium (Product Price)**
Es el valor base del producto remanufacturado. En Dynamics aparece como "Product Price" y representa el precio principal del artículo remanufacturado, excluyendo el Cargo Core. En el SII, este valor más el Cargo Core forman el **valor único total** del producto.

---

## R

**Reman / Remanufacturado**
Un producto **remanufacturado** es una pieza que fue usada, devuelta a Caterpillar, reconstruida completamente y vendida de nuevo con garantía. A diferencia de las piezas nuevas, los remanufacturados tienen un proceso de precio distinto (Premium + Core) y una identificación especial en el sistema.

**¿Cómo se identifica?**
En Dynamics, cada línea de factura tiene un campo llamado **Reman Indicator**:
- `Reman Indicator = 1` → el producto ES remanufacturado
- `Reman Indicator = 0` → el producto NO es remanufacturado

> ⚠️ **Importante:** No se debe usar la terminación del código de referencia (como "R" al final o "CC") para identificar remanufacturados, porque **no es un criterio confiable**. El único criterio válido es el Reman Indicator.

---

**Reman Indicator**
Es un campo numérico que Dynamics incluye en los archivos de interfaz de las facturas. Vale `1` si el producto es remanufacturado y `0` si no lo es. Es la **única fuente oficial** de identificación de remanufacturado en el proceso CrossDocking. El sistema SII debe leer este campo y actuar en consecuencia.

---

## S

**SIACO**
Es el sistema de la **agencia de aduanas** que procesa las declaraciones de importación. En el contexto de esta HU, se aclara que SIACO **no interactúa** con la pantalla de visualización Z95 ↔ INV. La integración con SIACO corresponde a una etapa posterior del proceso (HU de creación de DO).

**Simultaneidad de Usuarios**
Capacidad del sistema para que **varias personas trabajen al mismo tiempo** sin interferirse. En CrossDocking, esto es crítico porque el equipo de Commex procesa Gecolsa y Relianz de forma paralela. El sistema debe garantizar que las operaciones de un usuario no bloqueen ni afecten las de otro.

---

## V

**Valor FOB**
Es el precio de la mercancía puesto en el punto de embarque (Free On Board), sin incluir flete ni seguro internacional. Es uno de los valores clave que se valida en la pantalla de relación Z95 ↔ INV, ya que los valores de las facturas CAT (Z95) y PSC (INV) deben ser consistentes.

---

## Fórmula Clave del Proceso

```
Valor SII (remanufacturado) = Product Price (Premium) + Cargo Core
Cantidad SII (remanufacturado) = 1 unidad (sin duplicar)
```

**Ejemplo:**
- Dynamics Z95: Premium = $100 / Cargo Core = $20 / Cantidad = 1
- SII debe registrar: Valor = $120 / Cantidad = 1
- ❌ ERROR si SII registra: Valor = $100 y $20 por separado, o Cantidad = 2
