# GLOSARIO UNIFICADO — Proceso CrossDocking SII 2.0

**Aplica a:** HU-CD-26 a HU-CD-41 | **Versión:** 1.0 | **Fecha:** 2026-03-04

---

## A

**Aceptación (de la DIM):** Primera respuesta de la DIAN confirmando que revisó la DIM sin errores. No libera la mercancía — permite proceder al pago de tributos.

**Acuerdo comercial:** Tratado entre Colombia y otro país con preferencias arancelarias (descuentos en impuestos). Ej: TLC Colombia–USA. Debe declararse en la DIM si aplica.

**Agente de Carga:** Empresa que mueve la mercancía entre países. En CrossDocking: **DHL**. Envía el consolidado del embarque (guías, pesos, dealers) al SII.

**Alerta de validación:** Mensaje del sistema cuando detecta un problema que impide continuar (ej. reman sin licencia). No es error técnico — es problema de negocio que el analista debe resolver.

**API:** "Mesero" tecnológico entre sistemas. Permite que Dynamics pase datos al SII sin acceder directamente a su BD. Preferido sobre acceso directo en todas las integraciones.

**Arancel:** Impuesto calculado como % sobre el FOB del producto, según su BTN. Se paga a la DIAN antes del levante.

**Archivo Plano:** Archivo `.txt` o `.csv` con datos en columnas. Dynamics lo usa para enviar Z95 e INV al SII en las ventanas de tiempo.

**Autoadhesivo:** Comprobante electrónico de pago de tributos de importación. Sin él, la DIAN no autoriza el levante.

---

## B

**Bot (DHL):** Programa automatizado de DHL para enviar el consolidado al SII. En fase de pruebas; formato no definitivo.

**BTN (Subpartida Arancelaria):** Código que clasifica un producto en la nomenclatura aduanera (DIAN). Determina arancel y requisitos especiales. En CrossDocking, lo asigna la agencia de aduanas (SIACO) y lo retorna al SII vía DO. ⚠️ *Pendiente confirmar si en REQ-34 viene de Dynamics o paramétrico SII.*

**Bultos:** Número de empaques del embarque (cajas, pallets). La DIAN los cruza con la DIM para verificar lo recibido físicamente.

---

## C

**CAT / Caterpillar:** Fabricante y proveedor origen. Emite la factura **Z95** a Panamerican. "Compañía 500" en Dynamics.

**Caja (Número de Caja):** Identificador del empaque específico donde está una referencia con problema (usado en alertas de reman).

**Cierre financiero del embarque:** Registro contable en D365 de todos los costos (FOB + fletes + tributos + 2%) una vez obtenido el levante.

**Clave Compuesta:** Combinación de campos que hace único un registro. Para Z95: **Número + Año + Compañía**. Necesaria porque CAT reutiliza números cada ~2 años.

**Colisión de registros:** Error cuando dos registros distintos tienen la misma clave, causando sobrescritura o confusión.

**Commex:** Área de Comercio Exterior. Usuario principal del SII. Mantiene tablas de dealers, licencias y parámetros normativos.

**Consecutivo:** Número de secuencia asignado por CAT a cada Z95. Problema: CAT los reutiliza cada ~2 años → requiere clave compuesta.

**Consolidación de embarque:** Agrupación de varias facturas Z95 en una misma guía para que viajen juntas.

**Consolidado (DHL):** Resumen del agente de carga con guías, facturas, pesos y dealers del embarque.

**Costo estimado 2%:** % adicional sobre el total del embarque para gastos imprevistos. ⚠️ *Pendiente validación con área de Costos.*

**Costeo por línea:** Asignación proporcional de costos totales (flete + tributos + 2%) a cada referencia según su FOB.

**CrossDocking:** Proceso donde la mercancía de CAT (USA) llega, se valida y se distribuye directo a dealers **sin bodega intermedia**.

**Cruce interanual:** Error de asociar datos de dos años distintos (ej. Z95 2023 + INV 2025). El sistema debe bloquearlo.

---

## D

**D365 / Dynamics 365:** ERP de Microsoft del grupo corporativo. Sistema origen del que el SII recibe facturas vía interfaces automáticas (Z95, INV, OC).

**Dealer:** Destinatario logístico final del embarque (empresa, sucursal o punto de recepción). No es bodega de almacenamiento — es el punto de entrega. Exclusivo de una compañía (Gecolsa O Relianz).

**Desacoplamiento:** Principio donde dos sistemas funcionan de forma independiente. Si falla uno, el otro sigue operando.

**DIAN:** Dirección de Impuestos y Aduanas Nacionales de Colombia. Controla las importaciones, recibe la DIM, aprueba levante, liquida tributos.

**DIM (Declaración de Importación):** Documento oficial ante la DIAN para declarar qué se importa, en qué cantidad y valor. Sin DIM aprobada, la mercancía no sale de la zona aduanera.

**Discrepancia:** Diferencia significativa entre Z95 (CAT) e INV PSC (Panamerican) en cantidades, referencias o valores FOB que impide avanzar en el proceso.

**DO (Documento Operativo):** Documento que consolida info del embarque (INV, referencias, FOB, datos logísticos) para transmitirlo a la agencia de aduanas (SIACO). Prerrequisito para la DIM.

**DRS (Documento de Recepción de Servicio):** Documento contable que D365 genera para cerrar una OC. Autoriza el pago al proveedor. Cierre formal del proceso CrossDocking.

**Duplicado:** Registro que ya existe y llega de nuevo con los mismos datos clave. Debe rechazarse y registrarse en el log.

---

## E

**Embarcadora:** Empresa que carga físicamente la mercancía en el medio de transporte.

**Exportador:** Empresa que envía la mercancía desde origen. Generalmente CAT o subsidiaria.

---

## F

**FileZilla:** Programa de transferencia de archivos de SIACO. El SII deposita documentos en carpeta compartida (no es API — es entrega de archivos en buzón).

**FOB (Free On Board):** Precio de la mercancía puesta en el punto de embarque, sin flete ni seguro. Valor declarado a la DIAN para calcular tributos.

---

## G

**Gecolsa:** Compañía destinataria de la mercancía en Colombia. Opera con dealers propios, independientes de Relianz.

**Generación Masiva de Guías:** Creación automática de múltiples guías en un solo proceso para embarques con muchas facturas.

**Guía (Documento de Transporte):** "Pasaporte" de la mercancía en el transporte internacional. Agrupa múltiples Z95. Contiene datos del embarque, ruta y aduana.

---

## H

**Histórico:** Registros de años anteriores conservados para consulta pero no operables en procesos activos del año en curso.

---

## I

**"Impulsar" la DIM:** Marcar la DIM lista para transmitir a la DIAN. Genera el archivo oficial para envío electrónico.

**Incoterm:** Reglas internacionales de quién paga el transporte y quién asume el riesgo. El más común en CrossDocking: **FOB**.

**INV / INV PSC:** Factura que **Panamerican emite a Gecolsa o Relianz** (intercompañía). Sigla correcta: **INV**. Siempre ligada a una Z95.

**Interfaz (de sincronización):** Proceso automático que transfiere datos de Dynamics al SII. Hay 3 independientes: Z95, INV y OC.

**IVA de importación:** IVA calculado sobre (FOB + Arancel + Flete). Parte de los tributos que se pagan a la DIAN.

---

## L

**Levante:** Autorización final de la DIAN para sacar la mercancía de la zona aduanera. Automático (sin inspección) o Físico (con inspección). Habilita el costeo en D365.

**Licencia de Importación (L):** Autorización del Ministerio de Comercio para importar mercancía específica. Tiene vigencia y saldo. Se descuenta definitivamente al transmitir la DIM.

**Log de sincronización:** Registro histórico de cada ejecución de las interfaces (fecha, hora, registros procesados/fallidos y causa).

---

## M

**Manifiesto de carga:** Documento del transportador (aerolínea/naviera) listando la mercancía en el vuelo/barco. La DIAN lo cruza con la DIM.

**Mercancía Nueva:** Producto de primera fabricación. `Reman Indicator = 0`.

**Mezcla indebida:** Incluir en la misma guía mercancía nueva (Indicator=0) con remanufacturada (Indicator=1). Prohibido — la DIAN las trata con distintos requisitos.

**Modalidad de importación:** Régimen legal bajo el que entra la mercancía: Consumo (definitiva), Transformación (para reexportar), Temporal (con plazo de salida).

---

## N

**No parametrizada (reman):** La referencia reman no tiene configuración sobre si requiere licencia. El sistema genera alerta para que Commex defina el criterio.

---

## O

**OC (Orden de Compra):** Documento formal del pedido de mercancía: Gecolsa→Panamerican y Panamerican→CAT.

---

## P

**País de compra / origen / procedencia:** Tres datos distintos que exige la DIAN: dónde se hizo el negocio / dónde se fabricó / desde dónde se embarcó.

**Panamerican (PSC):** Empresa intermediaria del grupo. Compra a CAT (recibe Z95) y vende a Gecolsa/Relianz (emite INV).

**Parcialización:** Generar una guía con solo una parte de las facturas o referencias disponibles, para embarcar de forma flexible.

**Parametrización normativa reman:** Tabla de configuración (mantenida por Commex) que define qué referencias reman requieren licencia.

---

## R

**Reman / Remanufacturado:** Producto restaurado y puesto en venta nuevamente. Mismo código que uno nuevo pero requiere tratamiento aduanero especial. `Reman Indicator = 1`.

**Reman Indicator:** Campo de D365 que indica si una referencia es nueva (=0) o remanufacturada (=1). **Única fuente oficial** para determinar tipo de mercancía.

**Registro de Importación (R):** Similar a la Licencia pero con alcance diferente. Commex determina cuándo usar R vs L.

**Reporte de Discrepancias:** Excel automático con diferencias entre Z95 e INV, enviado por correo a Operaciones para corrección.

**Reproceso:** Reenvío de datos ya procesados. El sistema distingue reproceso legítimo (dato actualizado) de duplicado (mismo dato = rechazar).

**Reutilización de consecutivos:** Práctica de CAT de rehusar los mismos números Z95 cada ~2 años. El SII lo maneja con clave compuesta.

---

## S

**Saldo (de licencia/registro):** Unidades o valor disponible en una licencia. Se consume al transmitir la DIM. Debe ser suficiente antes de asignar.

**SIACO:** Sistema de la agencia de aduanas. Recibe el DO del SII vía FileZilla, asigna BTN, gestiona VoBo y prepara la DIM. Sin acceso directo a pantallas Z95/INV del SII.

**SII:** Sistema de Información Interno. Gestiona el proceso CrossDocking: recibe datos de Dynamics, DHL y SIACO.

**Sincronización:** Proceso automático (interfaces) que actualiza el SII con datos de Dynamics, sin duplicados.

**Sub-nacionalización:** Importar menos de lo facturado. Riesgo de incumplimiento de pedidos y problemas con aduana.

**Sobre-nacionalización:** Importar más de lo permitido. Riesgo de multas y problemas legales con la DIAN.

---

## T

**Tabla 1 de Dealers:** Catálogo oficial de dealers válidos con asignación a compañía. ⚠️ *Pendiente entrega por Commex.*

**Tesorería / Z Giros:** Área financiera de pagos al exterior. Usa estados de facturas del SII para determinar cuándo girar divisas a CAT.

**TRM:** Precio oficial del dólar en Colombia (Banco de la República). Convierte FOB en USD a COP para calcular tributos. Inmutable en la DIM.

**Tributos de importación:** Arancel + IVA de importación. Se pagan a la DIAN antes del levante. El comprobante es el autoadhesivo.

---

## V

**Validación Visual:** Pantalla obligatoria que compara cantidades y FOB de Z95, INV y guía antes de continuar al proceso aduanero. Solo lee datos — no modifica nada.

**Valor Unitario:** Precio de una sola unidad de la referencia (USD). FOB línea = Valor Unitario × Cantidad.

**Ventana de Tiempo:** Horario programado de ejecución de interfaces (aprox. 9 AM y 1 PM). Facturas emitidas fuera de esa ventana llegan en la próxima ejecución.

**Vía de transporte:** Tipo de transporte: Aérea, Marítima o Terrestre.

**Vigencia (licencia/registro):** Fecha límite de validez de la licencia. Si vencida, el sistema bloquea su uso aunque tenga saldo.

**VoBo (Visto Bueno):** Autorización de entidad de control (ICA, INVIMA) para importar un producto específico. Requerida por la DIAN en la DIM cuando aplica.

---

## Z

**Z95 (Factura Caterpillar):** Factura que CAT emite a Panamerican. Origen de toda la cadena CrossDocking.

---

## Pendientes Transversales

| # | Pendiente | Responsable |
|---|-----------|-------------|
| 1 | Formato archivo FileZilla/SIACO para el DO | Commex / Agencia |
| 2 | Tabla 1 de Dealers (Gecolsa/Relianz) | Commex |
| 3 | Validación % costo estimado (¿2%?) y base de cálculo | Costos |
| 4 | Composición exacta del BTN (¿Dynamics o paramétrico?) | Commex |
| 5 | Fuente oficial de la TRM (¿API o manual?) | TI / Financiero |
| 6 | Recepción hitos aduaneros: ¿automática (push) o manual (pull)? | Commex / TI |

---

## Módulos y Roles (SII 2.0 - Crossdocking)

**Analista Commex CD:** Rol de usuario con permisos para validar facturación Z95/INV, gestionar discrepancias y exportar reportes de control.

**Monitor de Facturación CD:** Módulo del SII donde se visualiza la relación Z95 ↔ INV ↔ OC. Permite la validación visual y exportación a Excel.

**Gestor de Guías DHL:** Módulo encargado de la recepción de archivos del agente de carga (automático vía Bot o manual). Cruza guías con facturas Z95.

**Mesa de Discrepancias:** Funcionalidad para gestionar diferencias de valor, cantidad o referencia entre lo facturado por CAT y lo facturado por PSC.

**Centro de Despacho (DO):** Módulo de salida que genera el Documento Operativo para la agencia de aduanas (SIACO).
