# Análisis Comparativo: HU-CD-29 (Sincronización Automática e Interfaces)

## ¿Qué tienen en común todas las versiones?
1. **El Concepto de la Clave de Unicidad**: En absoluto consenso, Mily, Oscar C y Oscar E establecen que el documento debe ser único usando el trinomio: `Número de Factura + Fecha (Año) + Compañía`.
2. **La Inmutabilidad del Registro**: Es mandatorio que una factura que ya inició un ciclo operativo (ej: Discrepancias o Guía), no sea objeto de "UPDATES" por nuevas sincronizaciones automatizadas provenientes de D365.
3. **Manejo de Lotes (Sincronización Parcial)**: Si un envío de D365 trae errores puntuales (missing data), esto no debe causar rollback total. Se acepta "Guardado Parcial" y registro de fallos individuales en log.

## Diferencias Principales (Aportes de cada versión)

### Versión final de Mily:
- **Claridad Normativa**: Posee escenarios en Gherkin excepcionalmente definidos enfocados en el valor del negocio, aportando el contexto macro de cómo CrossDocking depende de estas tres interfaces (Z95, INV, O.C.).
- **Comportamiento As-Is**: Aclara el tema de facturas "antiguas y reprocesos". Explicando que la sincronización omite automáticamente ciclos anteriores a menos de que un usuario invoque datos preexistentes en historial.

### Versión de Oscar C:
- **Detalle Estructural Profundo**: Expone la existencia real y necesaria de un modelo de base de datos intermedio para auditar. Sugiere la tabla `CD_SYNC_LOG` con todos sus fields (skipped_records, error_details, etc).
- **Enfoque de Arquitectura Cloud**: Menciona que el método real tras bambalinas es "Azure Service Bus (recepción de eventos) y Workers", además de plantear las rutas REST internas para invocar forzados manuales.
(*Nota: Todas estas especificaciones se removieron de la HU Final para preservar el enfoque 100% Funcional/Negocio requerido para este documento*).

### Versión de Oscar E:
- **Síntesis y Resumen**: Consolida de manera veloz las reglas RN1, 2, 3 y 4 de forma muy legible, lo que aportó a estructurar la redacción de reglas de negocio en la versión consolidada de una manera más asertiva.

---

## Estrategia para la Versión Consolidada
Para el archivo consolidado (`HU-CD-29.md`), el esqueleto directriz es la versión final (Mily) debido al nivel de madurez funcional.

Se incorporaron a la tabla de monitoreo todas las columnas matemáticas planteadas por Oscar C (*"Nuevos registros, Registros omitidos, y Errores"*), ya que aportan mucha más solidez al requerimiento visual del analista en pantalla de log. Se eliminaron menciones literales al bus de integración o la BD `CD_SYNC_LOG` convirtiendo dichos requerimientos puramente técnicos en obligaciones funcionales visuales (qué debe haber en el UI). Por último, se anonimizaron los responsables dentro de la tabla de compromisos, delegándolos por células y áreas ("Equipo TI, Líder de Dominio, Commex").
