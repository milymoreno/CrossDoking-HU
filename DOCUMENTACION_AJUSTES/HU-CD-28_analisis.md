# Análisis Comparativo: HU-CD-28 (Gestión de Discrepancias)

## ¿Qué tienen en común todas las versiones?
1. **Objetivo Principal**: Todas definen claramente que el propósito es comparar facturas Z95 (Caterpillar) contra INV PSC (Panamerican) para evitar nacionalizar de más o de menos.
2. **Criterios de Comparación**: Coinciden en validar cantidades, valores, referencias, compañía, y la asociación entre Z95 e INV.
3. **Flujo de Bloqueo**: Todas establecen que no se puede generar una guía de transporte si existen discrepancias activas.
4. **Reporte y Notificación**: Todas requieren la generación de un reporte en formato Excel con el detalle de las diferencias, el cual debe ser enviado por correo electrónico al equipo de Operaciones.
5. **Revalidación**: Todas contemplan que, tras la corrección en origen (Dynamics o SII), se debe poder volver a ejecutar el cruce.
6. **Escenarios de Prueba**: Los Criterios de Aceptación (Gherkin) en las tres versiones cubren los mismos escenarios básicos (Cantidades diferentes, Z95 sin INV, Sin discrepancias, Corrección posterior).

## Diferencias Principales (Aportes de cada versión)

### Versión de Oscar C:
- **Enfoque en Flujo Auxiliar y Auditoría**: Aporta reglas de negocio adicionales sobre trazabilidad, ejecución parcial de la validación y almacenamiento de históricos de los reportes.
- **Detalle de Campos del Reporte**: Especifica exactamente qué columnas debe llevar el Excel resultante.
*(Nota: Originalmente esta versión incluía diseño de Base de Datos y APIs, pero se definió excluir estos apartados por ser documentación estrictamente funcional).*

### Versión de Oscar E:
- **Enfoque Práctico y Sintético**: Es la versión más directa, centrada puramente en el flujo operativo.
- **Limpieza de UI**: Aporta la regla operativa de que una factura procesada debe desaparecer automáticamente de la vista general para no generar ruido.

### Versión Final (Mily):
- **Enfoque de Negocio y Funcionalidad**: Conecta la HU con otros procesos (Tesorería - Z giros) y define roles específicos (Auditor Commex).
- **Contexto Avanzado (As-Is)**: Detalla explícitamente los 5 campos editables en el SII para la INV (Subpartida, Unidad, Peso, Origen, Descripción), lo cual es crucial para saber qué se corrige en el SII y qué en Dynamics.
- **Mayor Rigor Funcional**: Incluye reglas de negocio muy bien pulidas (ej. validación por compañía).
- **Deuda Técnica / Compromisos**: Deja plasmada una lista de "Pendientes" post-sesión de refinamiento.

---

## Estrategia para la Versión Consolidada
Para el archivo consolidado tomamos **la robustez de negocio de la versión Final (Mily)** como base principal. La hemos enriquecido integrando las **reglas de trazabilidad y campos del reporte** aportadas por Oscar C, y la **limpieza visual del flujo** aportada por Oscar E. 

*(Bajo la nueva directriz, **se excluyeron totalmente los componentes de software como tablas SQL y endpoints API**, y se anonimizaron los nombres propios en favor de roles funcionales).*
