# HU-CD-37 — Glosario de Términos de Negocio (Estilo Dummies)

**Aplica a:** HU-CD-37 – Creación DO – Integración con Agencia de Aduanas
**Fecha:** 2026-03-04

---

## B

**BTN (revisado y completado aquí)**
El **BTN** es la **subpartida arancelaria** asignada a un producto dentro de la nomenclatura de comercio exterior. Esta nomenclatura la define la Organización Mundial de Aduanas y cada país la adapta. En Colombia la administra la DIAN.

**¿Quién asigna el BTN en CrossDocking?**
La **agencia de aduanas (SIACO)** es quien clasifica cada referencia con su BTN durante el proceso de creación del DO. Una vez asignado, SIACO lo retorna al SII y queda registrado en el DO.

**¿Por qué aparece en HU-CD-34 (alertas de reman)?**
Porque las referencias remanufacturadas ya deben tener el BTN parametrizado en el sistema (asignado en importaciones anteriores). Si hay algún problema de licencia, la alerta muestra el BTN para que el analista identifique exactamente qué producto tiene el problema.

---

## D

**DO (Documento Operativo)**
Es el documento interno que consolida toda la información del embarque (facturas INV, referencias, cantidades, valor FOB, datos logísticos) para entregársela a la agencia de aduanas. Es el "expediente" que la agencia necesita para iniciar el proceso oficial de importación.

---

## F

**FileZilla**
Es el programa de transferencia de archivos que usa SIACO (la agencia de aduanas). El SII deposita los documentos en una carpeta compartida usando este protocolo, y SIACO los recoge desde ahí para procesarlos. **No es una API directa** — funciona más como dejar un sobre en un buzón compartido que la agencia revisa periódicamente.

---

## S

**SIACO**
Es el sistema informático de la agencia de aduanas. Es el "otro lado" del proceso CrossDocking: cuando el SII termina de generar la guía y la valida, SIACO toma el DO y lo procesa para:
1. Asignar la clasificación arancelaria (BTN)
2. Gestionar certificados VoBo de entidades de control
3. Preparar la DIM (Declaración de Importación)

> ⚠️ **Importante:** SIACO **no** tiene acceso a la pantalla de visualización Z95/INV del SII. Su punto de contacto único con el SII es a través del DO enviado via FileZilla.

---

## V

**VoBo (Visto Bueno)**
Es la aprobación de una entidad gubernamental de control que certifica que un producto puede ser importado. Dependiendo del tipo de mercancía, pueden requerirse aprobaciones de entidades como ICA (productos agrícolas), INVIMA (medicamentos y alimentos), entre otras. El proceso de obtener los VoBo es parte del trámite que gestiona la agencia de aduanas con el DO.
