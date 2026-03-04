# HU-CD-40: Recepción de Información Aduanera – Aceptación, Pago y Levante

**Proceso:** CROSSDOCKING  
**Subproceso:** RECEPCIÓN DE INFORMACIÓN ADUANERA  
**Requerimiento Original:** 40 - El sistema debe tener la capacidad de recibir la información de Aceptación, pago (Autoadhesivo) y levante.

---

### Como:
Sistema SII 2.0

### Quiero:
Recibir y actualizar automáticamente la información aduanera posterior a la transmisión de la DIM

### Para:
Mantener actualizado el estado del proceso de importación y permitir continuar con costeo y cierre.

---

## Descripción Funcional

El sistema debe permitir recibir desde la autoridad aduanera o agencia:

1️⃣ Información de Aceptación de la DIM  
2️⃣ Información de Pago (Autoadhesivo)  
3️⃣ Información de Levante  

Estas actualizaciones deben reflejarse automáticamente en:

- La DIM
- El DO
- La Guía
- El estado general del embarque

---

## Flujo de Estados

Una DIM transmitida debe poder evolucionar de:

- Transmitida
→ Aceptada
→ Pagada
→ Con Levante
→ Cerrada

El sistema debe impedir saltos de estado inconsistentes.

---

## Información a Recibir

### Aceptación

- Número aceptación
- Fecha aceptación
- Estado validación DIAN

### Pago (Autoadhesivo)

- Número autoadhesivo
- Fecha pago
- Valor pagado
- Confirmación bancaria

### Levante

- Fecha levante
- Tipo levante (automático / físico)
- Observaciones

---

## Validaciones Obligatorias

- No permitir marcar DIM como pagada sin aceptación previa.
- No permitir marcar con levante sin pago confirmado.
- No permitir modificar valores declarados tras aceptación.

---

## Integración con Costeo (Req 41)

Cuando la DIM llegue a estado:

- Con Levante

El sistema debe:

- Marcar embarque como listo para migración a D365.
- Habilitar proceso de costeo.

---

## Visualización Requerida

El sistema debe mostrar:

- Estado actual DIM.
- Historial de cambios.
- Usuario sistema (interfaz).
- Fecha y hora de cada evento.

---

## Criterios de Aceptación (Gherkin)

### Escenario 1 – Recepción aceptación
Dado que la DIM fue transmitida  
Cuando se reciba aceptación  
Entonces el sistema debe cambiar estado a Aceptada.

---

### Escenario 2 – Recepción pago
Dado que la DIM está aceptada  
Cuando se reciba información de pago  
Entonces el sistema debe cambiar estado a Pagada.

---

### Escenario 3 – Recepción levante
Dado que la DIM está pagada  
Cuando se reciba levante  
Entonces el sistema debe cambiar estado a Con Levante.

---

### Escenario 4 – Orden incorrecto
Dado que no existe aceptación  
Cuando se intente registrar pago  
Entonces el sistema debe bloquear la operación.

---

### Escenario 5 – Auditoría
Dado que se actualice estado aduanero  
Cuando se consulte historial  
Entonces debe existir registro completo.

---

## Reglas de Negocio

- RN-01: Los estados deben respetar orden lógico.
- RN-02: La información aduanera solo puede provenir de fuente oficial.
- RN-03: No se permite alteración manual de estados finales.
- RN-04: El sistema debe conservar histórico.
- RN-05: El levante habilita el proceso de costeo.
- RN-06: No se permite cerrar embarque sin levante.