# HU-CD-36 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-36 – Validación Visual de Guía – Cantidades y Valor FOB
**Fecha:** 2026-03-04

---

## F

**FOB (Free On Board)**
Es el Incoterm que define que el vendedor es responsable hasta que la mercancía sube al medio de transporte en el puerto de origen; a partir de ahí, el comprador asume el riesgo y el costo del transporte. El **valor FOB** es el precio de la mercancía puesto en el punto de embarque, sin incluir flete ni seguro.

En CrossDocking, el valor FOB es el que se declara a la DIAN para calcular los tributos de importación. Por eso es crítico que sea exactamente igual en Z95, INV y guía.

---

## I

**Intercompañías**
Son las empresas del mismo grupo corporativo que se facturan entre sí. En CrossDocking:
- **CAT (Caterpillar)** le factura a Panamerican con la **Z95**
- **Panamerican (PSC)** le factura a Gecolsa/Relianz con la **INV**

Estas son transacciones intercompañías porque el dinero circula dentro del mismo grupo corporativo. La validación visual compara que ambas facturas (Z95 e INV) sean consistentes entre sí y con la guía.

---

## V

**Validación Visual (pantalla de confirmación)**
Es una pantalla obligatoria del sistema que muestra, lado a lado, los valores de las facturas Z95, las facturas INV y la guía generada, para que el analista pueda confirmar visualmente que todo suma igual antes de continuar. No es una pantalla de edición — solo muestra información y da o niega el paso al siguiente proceso.

> Analogía: es como hacer el cuadre de caja al final del día. Uno no cierra el cajero hasta que el conteo físico coincida con el sistema. En CrossDocking, la validación visual es ese "cuadre" antes de abrirle la puerta al proceso aduanero.
