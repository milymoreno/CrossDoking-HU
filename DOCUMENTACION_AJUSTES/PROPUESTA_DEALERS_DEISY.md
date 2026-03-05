# Propuesta de Ajuste Técnico: Validación de Dealer (RQ 35)

Basado en la nota técnica de **Deisy Rincón** y la imagen del mapeo CAT vs Gecolsa/Relianz, se proponen los siguientes ajustes:

---

## 1. Evolución del Modelo de Datos (Dealers)

Actualmente tenemos una tabla simple `cat_dealer`. Proponemos desglosarla para soportar el mapeo solicitado:

### [NUEVA TABLA] `cat_dealer_catalogo`
Esta tabla contendrá los Dealers "Logísticos" (los nuestros, ej: RAPI, R460, RSOR).
- `id`, `codigo_logistico`, `nombre`, `direccion`, `compania_id` (Gecolsa/Relianz).

### [NUEVA TABLA] `cat_dealer_mapeo_cat`
Esta tabla mapea qué códigos de CAT (Dynamics) corresponden a nuestros Dealers Logísticos.
- `id`, `codigo_dealer_cat` (ej: R15P, R15Q), `id_dealer_logistico` (FK a `cat_dealer_catalogo`), `via_transporte` (Aéreo/Marítimo), `tipo_inventario` (Stock/Backorder/Reman).

---

## 2. Reglas de Negocio (Validaciones)

Se incorporarán las siguientes reglas en el proceso de generación de guías:

| ID | Regla | Descripción |
|----|-------|-------------|
| **RN-V-01** | **No Mezcla REMAN** | El sistema BLOQUEARÁ la generación de una guía si el Dealer Logístico contiene simultáneamente referencias con `reman_indicator = 0` (Nueva) y `reman_indicator = 1` (Reman). |
| **RN-V-02** | **Validación de Compañía** | Al recibir el Dealer de Dynamics, el sistema verificará si pertenece a Gecolsa o Relianz según el mapeo paramétrico. |
| **RN-V-03** | **Relación Dinámica** | La información de Dynamics se cruzará contra la tabla de mapeo para determinar el "Punto de Entrega/Depósito" final del embarque. |

---

## 3. Roles y Parametrización

**¿Quién parametriza?**
- El **Analista Commex CD** será el encargado de crear y actualizar este mapeo (Tabla 1 de Dealers).
- Se habilitará una opción en el menú de "Configuración de Maestros" para este fin.

---

**¿Estás de acuerdo con este enfoque para la HU-CD-35?**
Este cambio nos permitirá cumplir con la tabla de "Casos Especiales" mostrada en la imagen (donde, por ejemplo, los dealers R15Q a R173 de CAT consolidan en el dealer logístico R460 de Gecolsa para Aéreo).
