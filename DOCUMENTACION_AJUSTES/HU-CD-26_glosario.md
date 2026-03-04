# HU-CD-26 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-26 – Recepción de Información Consolidada del Agente de Carga
**Fecha:** 2026-03-04

> Este glosario explica cada término de negocio en lenguaje sencillo para que cualquier persona (sin importar su área) pueda entender de qué se habla en la historia de usuario.

---

## A

**Agente de Carga**
Es la empresa intermediaria que se encarga de mover físicamente la mercancía de un país a otro. En este proceso, el agente de carga principal es **DHL**. Su rol es recoger los productos en origen (en este caso, de Caterpillar en EE.UU.) y entregarlos en destino (Colombia). También nos envía un archivo con el detalle de todo lo que viene en el embarque.

---

**Archivo Plano**
Es un archivo de texto simple (como un `.txt` o `.csv`) donde la información está organizada en columnas separadas por comas, punto y coma o espacios. No tiene fórmulas ni formatos complejos como un Excel. Dynamics usa archivos planos para enviar datos al SII (el sistema interno). Piénsalo como un "mensaje de texto con datos estructurados".

---

## B

**Bot (de DHL)**
Es un programa automatizado que DHL tiene configurado para enviar el archivo del consolidado al sistema de manera automática, sin que nadie lo haga manualmente. Es como un robot que envía el correo solo. En este proceso aún está en fase de pruebas y su formato de archivo no está finalizado.

---

## C

**CAT / Caterpillar**
Es el fabricante y proveedor de la maquinaria y partes. En el mundo de CrossDocking, CAT es quien genera la **factura Z95**, que es el documento que acredita que la mercancía fue vendida a Panamerican. CAT opera como "Compañía 500" dentro de Dynamics.

---

**Compañía 500**
Es el código interno en Dynamics que identifica a **Caterpillar / Panamerican** como entidad vendedora. Cuando ves "compañía 500" en el sistema, estás viendo operaciones de la empresa que vende la mercancía antes de que llegue a Gecolsa o Relianz.

---

**Consolidado**
Es el resumen de todo lo que viene en un embarque. Contiene guías, facturas, pesos y datos de dealers. El agente de carga (DHL) genera este consolidado y lo envía al sistema para que el equipo de operaciones lo valide. Es como el "manifiesto" del embarque.

---

**CrossDocking**
Proceso logístico en el que la mercancía que llega de Caterpillar (USA) se recibe, valida y distribuye directamente a los dealers (puntos de venta) **sin almacenarse** en una bodega intermedia. El objetivo es reducir tiempos y costos de almacenamiento.

---

## D

**D365 / Dynamics 365**
Es el sistema ERP (Enterprise Resource Planning) de Microsoft que usan las empresas del grupo (Panamerican, Gecolsa, Relianz, etc.) para gestionar sus operaciones financieras, de compras y ventas. Es el sistema "mayor" del que el SII recibe información. Cuando se dice "viene de Dynamics", significa que el dato fue creado o procesado en este sistema.

---

**Dealer**
En el proceso CrossDocking, el Dealer es el **destinatario logístico del embarque**: la empresa, sucursal o bodega regional a la que se debe entregar la mercancía una vez que pasa por aduana. Pueden ser sucursales de Gecolsa o Relianz en distintas ciudades o regiones (ej. Barranquilla, Bogotá).

> **Aclaración importante:** Un Dealer en este contexto **no es** el espacio físico de almacenamiento dentro de un centro de distribución, sino el **punto de destino final** a quien van dirigidas las facturas y la guía. El objetivo del CrossDocking es justamente entregar directo al Dealer **sin pasar por una bodega intermedia**.
>
> Cada Dealer tiene un código único en el sistema y está asignado exclusivamente a una compañía (Gecolsa o Relianz).

---

**DIM (Declaración de Importación)**
Es el documento oficial que se presenta ante la aduana colombiana para legalizar la entrada de la mercancía al país. Sin una DIM aprobada, la mercancía no puede salir de la zona aduanera.

---

## G

**Gecolsa**
Una de las dos compañías destinatarias de la mercancía en Colombia. Es la empresa del grupo que distribuye los productos CAT al mercado colombiano. Opera de manera independiente a Relianz dentro del sistema.

---

**Guía (documento de transporte)**
Es el número único que identifica un embarque o parte de él durante su transporte internacional. Es como el "número de seguimiento" de un paquete de mensajería, pero a nivel de comercio internacional. Una guía puede contener múltiples facturas y referencias.

---

## I

**INB / INR (Intercompany Invoice)**
Es la factura que **Panamerican le emite a Gecolsa o Relianz** por los productos que les vendió. La INB es la factura de la venta entre compañías del mismo grupo (intercompañía). Siempre está relacionada con una Z95, ya que Panamerican primero le compra a CAT (Z95) y luego le vende a Gecolsa/Relianz (INB).

**En resumen:** Z95 = CAT vende a Panamerican. INB = Panamerican vende a Gecolsa/Relianz.

---

**Interfaz**
En este contexto, es un proceso automatizado que transfiere datos de un sistema a otro. Dynamics genera interfaces (archivos planos) que el SII recibe automáticamente en ventanas de tiempo definidas. No es una pantalla de usuario; es una transferencia de datos entre sistemas.

---

## O

**Orden de Compra (OC)**
Es el documento que formaliza el pedido de mercancía. Funciona así:
1. Gecolsa emite una OC a Panamerican (pidiéndole productos).
2. Panamerican emite una OC a Caterpillar (comprándole los productos).
Cada OC tiene un número único y detalla qué se pide, en qué cantidad y a qué precio.

---

## P

**Panamerican**
Es la empresa intermediaria del grupo que compra a Caterpillar y vende a Gecolsa y Relianz. Actúa como el "distribuidor mayorista" interno del grupo. Opera en Dynamics y es quien genera las INBs a las compañías del grupo.

---

## R

**Relianz**
La segunda compañía destinataria de la mercancía en Colombia. Similar a Gecolsa en su operación, pero son entidades distintas con dealers y operaciones independientes en el sistema.

---

## S

**SII (Sistema de Información Interno)**
Es el sistema propio de la empresa que gestiona el proceso de CrossDocking de manera centralizada. Es el sistema que se está actualizando/desarrollando. Recibe información de Dynamics, del agente de carga y de la agencia de aduanas para coordinar todo el proceso de importación y distribución.

**Versión anterior:** S400 / Versión 1 (sistema legado que se está reemplazando).

---

## V

**Ventana de Tiempo (de interfaces)**
Las interfaces de Dynamics (que envían Z95 e INB al SII) no corren todo el tiempo; se ejecutan en horarios programados, aproximadamente a las **9:00 AM y 1:00 PM**. Si una factura se emite después de esa ventana, no llegará al SII hasta la siguiente ejecución. Por eso a veces hay facturas "que no aparecen" aunque ya existen en Dynamics.

---

## Z

**Z95 (Factura Caterpillar)**
Es la factura que **Caterpillar le emite a Panamerican** por los productos vendidos. El código "Z95" es el prefijo que identifica este tipo de factura dentro del sistema. Es el documento de origen de toda la cadena del CrossDocking. Sin una Z95, no puede existir una INB ni puede avanzar ningún proceso de importación.

**Dato clave:** El nombre "Z95" viene de la nomenclatura interna de Caterpillar para sus facturas de exportación a distribuidores en Latinoamérica.

---

## Relación entre los Términos Principales

```
[CAT/Caterpillar] ---(Z95)---> [Panamerican] ---(INB)---> [Gecolsa / Relianz]
                                     |
                               [Interfaces Dynamics]
                                     |
                                   [SII]
                                     |
                            [Agente de Carga / DHL]
                          (Guías + Pesos + Dealers)
```

El SII recibe información **de dos fuentes** para el proceso de CrossDocking:
1. **Dynamics:** Z95 + INB (vía archivos planos automáticos)
2. **DHL:** Consolidado del embarque (guías, pesos, dealers)

Ambas fuentes deben cruzarse y validarse antes de poder avanzar al proceso de discrepancias.
