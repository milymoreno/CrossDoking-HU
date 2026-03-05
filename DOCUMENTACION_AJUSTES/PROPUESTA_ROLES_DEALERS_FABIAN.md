# Propuesta Técnica: Roles y Condiciones de Creación de Dealers (Fabián Barragán)

Basado en la solicitud de **Fabián Barragán**, se definen los roles autorizados y las condiciones comerciales/técnicas para dar de alta un Dealer en el SII 2.0:

---

## 1. Roles Autorizados

Se proponen dos niveles de responsabilidad para garantizar el control:

| Rol | Acción | Descripción |
|-----|--------|-------------|
| **Analista Commex CD** | **Creación y Edición** | Único perfil facultado para ingresar nuevos Dealers Logísticos y sus Mapeos CAT al sistema. |
| **Coordinador Commex / Auditor** | **Aprobación (Opcional)** | Revisión de que el nuevo dealer cumple con la estructura fiscal y de compañía requerida. |

---

## 2. Condiciones de Creación (Integridad de Datos)

Para que un Dealer sea considerado "Válido" por el sistema, debe cumplir obligatoriamente con estos criterios durante su creación:

### A. Condiciones Fiscales y de Compañía
- **Compañía Obligatoria**: Debe estar amarrado a `cat_compania` (Gecolsa o Relianz). No se permiten dealers "huérfanos" o multiregistro.
- **Unicidad de Código Logístico**: El código interno (ej. RAPI) no puede repetirse dentro de la misma compañía.

### B. Condiciones de Mapeo (Conectividad Dynamics)
- **Mapeo CAT Activo**: Cada dealer creado mediante la interfaz de Dynamics debe tener al menos una relación en la tabla `cat_dealer_mapeo`.
- **Definición de Vía**: Debe especificarse si el mapeo es para carga **Aérea**, **Marítima** o **Ambas**.
- **Tipo de Inventario**: Debe clasificarse si recibe **Stock**, **Backorder** o **Reman** (para aplicar la regla de No Mezcla).

### C. Condiciones Operativas
- **Estado Inicial**: Todo dealer nuevo se crea en estado `Activo` por defecto.
- **Histórico**: Los dealers no se eliminan físicamente (Soft Delete), solo se inactivan para mantener la trazabilidad de las guías históricas.

---

## 3. Flujo de Creación Sugerido

1. El Analista ingresa a **Maestros > Dealers**.
2. Define los datos básicos (Código Logístico, Nombre, Compañía).
3. Configura el **Mapeo CAT** (Códigos de Caterpillar + Vía + Tipo).
4. El sistema valida automáticamente que el código CAT no esté mapeado a otra compañía (evitar cruces Gecolsa/Relianz).
5. **Fin**: El dealer queda habilitado para la generación de guías.

---

**¿Estás de acuerdo con estos Roles y Condiciones para la HU-CD-35?**
Una vez aprobado, incluiré estos puntos específicos como Reglas de Negocio (RN) y Criterios de Aceptación en la historia de usuario.
