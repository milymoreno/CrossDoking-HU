# HU-CD-39: Generación de Declaración de Importación (DIM)

**Proceso:** CROSSDOCKING  
**Subproceso:** GENERAR DIM  
**Requerimiento Original:** 39 - El sistema debe contar con todos los módulos que tiene la agencia de aduanas para generación de embarques, declaraciones de importación y sus respectivos archivos, asegurando el proceso ante fallas. Debe permitir impulsar declaraciones y habilitar campos como modalidad, acuerdo, forma de pago, manifiesto de carga, fecha, tipo impo, cod depósito, TRM, fletes, bultos, embalaje, peso bruto. Debe permitir cualquier modalidad y tipo de declaración.

---

### Como:
Analista de Comercio Exterior

### Quiero:
Generar la Declaración de Importación (DIM) directamente desde el sistema SII

### Para:
Garantizar continuidad operativa aun ante fallas externas y asegurar cumplimiento normativo completo.

---

## Prerrequisitos

Antes de generar DIM:

- Guía generada (HU-CD-33).
- Validación visual correcta (HU-CD-36).
- DO creado y actualizado (HU-CD-37).
- Licencias asignadas cuando aplique (HU-CD-32).
- Sin discrepancias activas (HU-CD-28).

---

## Descripción Funcional

El sistema debe permitir:

1️⃣ Crear una nueva Declaración de Importación asociada a un DO y guía.

2️⃣ Habilitar dinámicamente los siguientes campos:

- Tipo DIM
- Modalidad de importación
- Acuerdo
- Forma de pago
- Manifiesto de carga
- Fecha declaración
- Tipo importación
- Código depósito
- TRM
- Fletes
- Bultos
- Tipo de embalaje
- Peso bruto
- País origen
- País procedencia

3️⃣ Permitir seleccionar:

- Cualquier modalidad de importación.
- Cualquier tipo de declaración soportado por normativa vigente.

---

## Funcionalidad “Impulsar Declaración”

El sistema debe permitir:

- Marcar declaración como lista para transmisión.
- Generar archivos estructurados requeridos.
- Permitir envío electrónico.
- Validar consistencia antes del envío.

---

## Control de Integridad

El sistema debe validar:

- Que el valor FOB coincida con la guía.
- Que las cantidades coincidan.
- Que las subpartidas estén clasificadas.
- Que las licencias estén correctamente asociadas.
- Que la TRM corresponda a la fecha aplicable.

---

## Autonomía Operativa

El sistema debe:

- Permitir generación de DIM aun si:
  - Existe falla temporal en la agencia externa.
- Conservar respaldo completo de:
  - Archivos generados.
  - Versiones.
  - Estados.

---

## Estados de DIM

La DIM debe tener estados controlados:

- Borrador
- Validada
- Impulsada
- Transmitida
- Aceptada
- Pagada
- Con levante
- Rechazada
- Anulada

---

## Control de Versiones

Si se requiere corrección:

- El sistema debe permitir generar versión nueva.
- Mantener histórico de versiones anteriores.
- Registrar usuario y fecha.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Generación exitosa
Dado que todos los prerrequisitos están cumplidos  
Cuando el usuario genere DIM  
Entonces el sistema debe crear la declaración en estado Borrador.

---

### Escenario 2 – Impulsar declaración
Dado que la DIM está validada  
Cuando el usuario la impulse  
Entonces el sistema debe generar archivo estructurado y cambiar estado.

---

### Escenario 3 – Validación incompleta
Dado que falta licencia o clasificación  
Cuando se intente generar DIM  
Entonces el sistema debe bloquear la operación.

---

### Escenario 4 – Múltiples modalidades
Dado que el usuario seleccione modalidad diferente  
Cuando genere DIM  
Entonces el sistema debe habilitar campos correspondientes dinámicamente.

---

### Escenario 5 – Control de versiones
Dado que se requiera corrección  
Cuando el usuario genere nueva versión  
Entonces el sistema debe conservar histórico.

---

## Reglas de Negocio

- RN-01: No se puede generar DIM sin guía válida.
- RN-02: No se puede generar DIM con discrepancias activas.
- RN-03: Debe soportar cualquier modalidad permitida por normativa.
- RN-04: La DIM debe ser auditable en todas sus etapas.
- RN-05: Debe permitir operación autónoma ante fallas externas.
- RN-06: No se permite modificación posterior a aceptación sin control de versión.
- RN-07: La información debe estar lista para migración a D365 (Req 41).