# HU-CD-42: Consulta de Información Migrada a D365

**Proceso:** CROSSDOCKING  
**Subproceso:** CONSULTA POST MIGRACIÓN A D365  
**Relacionado con:** HU-CD-40 y HU-CD-41  

---

### Como:
Analista de Comercio Exterior / Área Financiera

### Quiero:
Consultar la información enviada a D365 después del proceso de costeo

### Para:
Verificar que la información fue recibida correctamente y tener trazabilidad financiera del embarque.

---

## Descripción Funcional

El sistema debe permitir:

1️⃣ Consultar embarques enviados a D365.  
2️⃣ Visualizar información transmitida:
   - Número de Guía
   - Número DIM
   - Valor FOB
   - Fletes
   - Tributos
   - Valor total costeo
   - 2% aplicado
3️⃣ Ver respuesta de D365:
   - Número DRS generado
   - Estado de procesamiento
   - Fecha recepción
   - Mensaje técnico

---

## Filtros Disponibles

- Número Guía
- Número DIM
- Fecha envío
- Estado (Exitoso / Error / Pendiente)
- Dealer
- Compañía

---

## Estados del Registro

- Enviado
- Recibido
- Procesado
- DRS Generado
- Error Técnico

---

## Validaciones

- No se permite reenviar sin control.
- No se permite modificar valores enviados.
- Debe conservar histórico completo.

---

## Criterios de Aceptación

Escenario 1 – Consulta exitosa  
Dado que un embarque fue enviado a D365  
Cuando el usuario lo consulte  
Entonces debe visualizar la información transmitida y el estado actual.

Escenario 2 – Error de migración  
Dado que el envío falló  
Cuando el usuario consulte  
Entonces debe visualizar el mensaje técnico de error.

Escenario 3 – Auditoría  
Dado que se consulte un envío antiguo  
Entonces debe conservar histórico.