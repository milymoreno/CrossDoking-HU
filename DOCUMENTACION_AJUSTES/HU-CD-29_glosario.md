# HU-CD-29 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-29 – Sincronización de Facturas PSC y Z95 desde D365
**Fecha:** 2026-03-04

---

## A

**API (Application Programming Interface)**
Es como un "mesero" entre dos sistemas. Dynamics quiere pasarle datos al SII, pero no puede entrar a la base de datos del SII directamente (por seguridad). Entonces le habla al API del SII, que recibe la información, la valida y la guarda correctamente. Así los sistemas se comunican sin que uno "entre a la cocina del otro".

---

## C

**Clave única**
Es la combinación de datos que hace que un registro sea irrepetible en el sistema. Para las facturas Z95 en este contexto, la clave única es: **Número Z95 + Año + Compañía**. Si el sistema recibe dos registros con la misma clave única, el segundo es un duplicado y debe rechazarse.

---

## D

**Desacoplamiento (arquitectura)**
Es el principio de que dos sistemas deben poder funcionar de forma independiente. Si Dynamics falla, el SII sigue funcionando con los datos que ya tiene. Si la interfaz de INV falla, la interfaz de Z95 no debe verse afectada. Es como dos departamentos de una empresa que colaboran pero no dependen el uno del otro para funcionar.

**Duplicado**
Un registro que ya existe en el sistema y que llega nuevamente con los mismos datos identificadores. El sistema debe detectarlo y rechazarlo, registrando el intento en el log.

---

## I

**Interfaz (de sincronización)**
En este contexto, una "interfaz" es un proceso programado que **transfiere datos automáticamente** desde Dynamics al SII. Hay 3:
- Interfaz 1: Z95 (facturas CAT)
- Interfaz 2: INV (facturas Panamerican)
- Interfaz 3: OC (Órdenes de Compra)

Cada una corre de forma independiente. Si una falla, las otras siguen funcionando.

---

## L

**Log de sincronización**
Es el registro histórico de todas las veces que las interfaces han corrido. Para cada ejecución queda registrado: fecha, hora, cuántos registros se procesaron, cuántos fallaron y por qué. Es como la bitácora de un piloto — si algo sale mal, el log permite saber exactamente qué pasó y cuándo.

---

## R

**Reproceso**
Es cuando una interfaz vuelve a enviar datos que ya habían sido procesados anteriormente. El sistema debe identificar si es un reproceso legítimo (misma factura, nueva fecha = nuevo registro válido) o un duplicado (misma factura, misma fecha = rechazar).

---

## S

**Sincronización**
Es el proceso automático que mantiene los datos del SII actualizados con lo que hay en Dynamics. Las interfaces se encargan de esta sincronización, corriendo periódicamente para traer las facturas nuevas. Una sincronización exitosa significa que el SII tiene la misma información que Dynamics (sin duplicados ni errores).
