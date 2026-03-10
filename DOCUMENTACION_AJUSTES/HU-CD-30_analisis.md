# Análisis Comparativo: HU-CD-30 (Control de Consecutivos Repetidos Z95 por Año)

## ¿Qué tienen en común todas las versiones?
1. **La Naturaleza Operativa del Negocio:** En todas las entrevistas de recolección previas, el equipo detectó que "Caterpillar reutiliza" los números de folio. Es indiscutible de que la historia gira en torno a resolver esta limitación de proveedor origen (CAT).
2. **La Fórmula Correctiva Tridimensional:** Mily, Oscar C y Oscar E dictaminan, cada cual a su estilo, que la solución recae en cambiar la matriz de unicidad. Pasando de un `ID_Z95` a un `ID_Z95 + Año_Factura + Compañia_Dueña`.
3. **Comportamiento As-Is por defecto:** Todas las versiones son tajantes en que la tabla visual y los módulos vivos de la aplicación van siempre a pre-filtrar o arrancar su carga con base en "El Año En Curso" o "Año Vigente".

## Diferencias Principales (Aportes de cada versión)

### Versión final de Mily:
- **Claridad de Alcance y Ejemplo:** Su enfoque plantea un ejemplo literal (`Z95123456 - 2023-01-06 / Z95123456 - 2025-01-06`) sumamente pedagógico.
- **Detalle Multimodal:** Especifica puntualmente cómo este comportamiento afecta otros frentes como "Generación de Guía", "Discrepancias" o "Cruce con INV". En esencia, el mapeo integral de las reglas de uso.

### Versión de Oscar C:
- **Estructura Arquitectónica y DDL (Retirada en el consolidado):** Documentaba reglas literales de base de datos como la instrucción en SQL puro para obligar integridad referencial (`CREATE UNIQUE INDEX uq_z95_company_year ON cd_invoices_z95...`). Este conocimiento no se desecha para el líder técnico/DBA, pero por políticas de Historias Funcionales, se excluyen sentencias técnicas del Markdown.
- **Grillas UI:** Proporciona un esquema directo de las columnas requeridas (Número, Fecha, Año, Estado).

### Versión de Oscar E:
- **Pragmatismo Funcional y BDD:** Estableció escenarios base bastante sólidos e impulsó la justificación fundamental: "No borrar el historial pero proteger el proceso actual".

---

## Estrategia para la Versión Consolidada
Para el archivo consolidado (`HU-CD-30.md`), se extrajeron los aportes más fuertes de cada lado: 

1. El formato pedagógico y amplio de cruces modulares que proponía **Mily** se mantuvo como la espina dorsal (*La Solución Funcional*).
2. Las columnas y protecciones UI listadas por **Oscar C** se tradujeron a lenguaje no-SaaS, volviéndolo una Regla "RN". Los bloques SQL no fueron transcritos, delegando esa lógica de constricción de clave a las especificaciones técnicas del DB.
3. Se expandieron los Casos de Aceptación Gherkin de **Oscar E** volviéndolos más explícitos y agregando un escenario final ("Cruce interanual denegado") para reforzar que un registro INV errante tampoco provoque falsos positivos.
