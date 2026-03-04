# HU-CD-34 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-34 – Validación de Referencias Remanufacturadas sin Licencia o Saldo
**Fecha:** 2026-03-04

> Los términos Reman, Reman Indicator, Licencia, Saldo, Vencida, DIAN ya están definidos en glosarios anteriores (HU-CD-27 y HU-CD-32). Aquí se definen los términos nuevos de esta HU.

---

## A

**Alerta de validación**
Es un mensaje que el sistema muestra en pantalla cuando detecta un problema que impide continuar. En esta HU, la alerta aparece cuando una referencia remanufacturada no tiene licencia válida o suficiente saldo. No es un error del sistema — es una notificación de un problema de negocio que el analista debe resolver.

---

## B

**BTN (Subpartida / Posición Arancelaria)**
Es el código que identifica un producto dentro de la nomenclatura aduanera internacional (basada en el Sistema Armonizado de Designación y Codificación de Mercancías). En Colombia lo administra la DIAN. Este código determina:
- Qué arancel paga el producto al importarse
- Qué trámites o permisos especiales requiere
- Cómo se clasifica en estadísticas de comercio exterior

> ⚠️ **Pendiente confirmar:** el BTN que aparece en el REQ-34 necesita confirmación de Commex sobre cómo está compuesto exactamente y de qué sistema proviene (¿Dynamics? ¿paramétrico del SII?).

---

## C

**Caja (Número de Caja)**
En el contexto del remanufacturado, el número de caja identifica el empaque específico dentro del embarque donde se encuentra la referencia con problema. Es un identificador de trazabilidad física: permite localizar exactamente dónde está el producto dentro del lote de importación.

---

## N

**No parametrizada (tipo de inconsistencia)**
Ocurre cuando el sistema no puede determinar si una referencia remanufacturada requiere o no licencia, porque su configuración no existe en el sistema. En este caso, el sistema genera una alerta de tipo "No parametrizada" para que el área de Commex defina el criterio antes de continuar.

---

## P

**Parametrización normativa de remanufacturados**
Es la tabla de configuración que define, para cada referencia o categoría de producto remanufacturado, si requiere licencia de importación o no. Esta parametrización es mantenida por el área de Commex. Si no está configurada, el sistema no puede validar correctamente.

---

## Flujo de Validación en Lenguaje Simple

```
¿La referencia es Reman? (Reman Indicator = 1)
   └── NO → No aplica esta validación. Continúa.
   └── SÍ → ¿Está parametrizada como "requiere licencia"?
                └── NO → Continúa sin problema.
                └── SÍ → ¿Tiene licencia asignada?
                              └── NO → ⚠️ Alerta: "Sin licencia"
                              └── SÍ → ¿Está vigente?
                                           └── NO → ⚠️ Alerta: "Licencia vencida"
                                           └── SÍ → ¿Tiene saldo suficiente?
                                                         └── NO → ⚠️ Alerta: "Saldo insuficiente"
                                                         └── SÍ → ✅ OK. Continúa.
```
