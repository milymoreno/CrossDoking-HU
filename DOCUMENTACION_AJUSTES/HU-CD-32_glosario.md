# HU-CD-32 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-32 – Asignación de Registro y/o Licencia de Importación en CrossDocking
**Fecha:** 2026-03-04

---

## A

**Agotada (estado de licencia)**
Estado que adquiere una licencia de importación cuando su saldo llega a cero. Una licencia agotada **no puede usarse** para nuevas operaciones, aunque su fecha de vigencia todavía no haya vencido.

---

## D

**DIAN**
Dirección de Impuestos y Aduanas Nacionales de Colombia. Es la entidad gubernamental que regula y controla las importaciones. Todo documento de importación (registro, licencia) lo expide o regula la DIAN.

**DIM (Declaración de Importación)**
Es el formulario oficial que se presenta ante la DIAN para declarar que se va a importar determinada mercancía. Es el documento legal más importante del proceso. El consumo definitivo de la licencia ocurre al generar la DIM, porque ahí es cuando la operación queda comprometida ante el estado.

---

## L

**Licencia de Importación (tipo L)**
Es un permiso que la DIAN otorga para importar una **cantidad específica** de un producto determinado. Tiene dos restricciones:
1. **Saldo:** Si tienes licencia para 100 unidades, solo puedes importar 100 en total (el sistema descuenta cada vez).
2. **Vigencia:** Tiene una fecha de vencimiento; después de esa fecha no puede usarse.

> Analogía: es como un voucher de descuento que tiene fecha de expiración y solo puede canjearse cierto número de veces.

---

## P

**Parametrización normativa**
Es la configuración que define, para cada modelo o referencia de producto, si necesita Registro (R), Licencia (L) o ninguno de los dos. Esta configuración la mantiene el área de Commex y debe poderse modificar sin necesidad de pedirle a TI un desarrollo.

---

## R

**Registro de Importación (tipo R)**
Es un permiso que habilita la importación de un **tipo de producto** en general, sin límite de cantidad. Solo tiene restricción de **vigencia** (fecha de vencimiento). A diferencia de la licencia, no tiene saldo — se puede usar todas las veces que sea necesario mientras esté vigente.

> Analogía: es como una membresía anual de un club — mientras no venza, puedes entrar las veces que quieras.

---

## S

**Saldo de licencia**
Es la cantidad pendiente disponible en una licencia de importación tipo L. Por ejemplo, si la licencia fue otorgada para 1.000 unidades y ya se han importado 600, el saldo es 400. El sistema descuenta automáticamente cada vez que se genera una DIM.

---

## V

**Vencida (estado de licencia)**
Estado que adquiere un documento de importación cuando su fecha de vigencia ha superado la fecha actual. Una licencia vencida **no puede usarse**, aunque todavía tenga saldo disponible. El sistema debe bloquear automáticamente cualquier intento de usarla.

**Vigencia**
Es el período durante el cual un registro o licencia de importación es válido. Está definido por una **fecha de inicio** y una **fecha de vencimiento**. El sistema debe validar esta fecha en tiempo real cada vez que el analista intenta usar un documento.

---

## Diferencia clave Registro vs Licencia

| | Registro (R) | Licencia (L) |
|-|-------------|-------------|
| ¿Tiene saldo limitado? | No | Sí |
| ¿Tiene vigencia? | Sí | Sí |
| ¿Qué valida el sistema? | Solo fecha de vencimiento | Fecha de vencimiento + Saldo disponible |
| ¿Cuántas veces se usa? | Ilimitado (mientras vigente) | Hasta agotar el saldo |
