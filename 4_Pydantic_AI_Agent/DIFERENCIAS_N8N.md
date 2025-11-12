# 📋 Diferencias entre Implementación n8n y Código Python

## Fecha de Análisis: 12 de Noviembre de 2024

---

## Resumen Ejecutivo

El agente Python Pydantic AI es **100% compatible** con la estructura de datos creada por el workflow n8n, con una **única limitación**: análisis de imágenes requiere adaptación.

---

## ✅ Compatibilidad Completa

### Tablas de Supabase
Todas las tablas son idénticas entre ambas implementaciones:

| Tabla | Columnas | Estado |
|-------|----------|--------|
| `documents` | id, content, metadata (jsonb), embedding (vector) | ✅ Idéntico |
| `document_metadata` | id, title, url, created_at, schema | ✅ Idéntico |
| `document_rows` | id, dataset_id, row_data (jsonb) | ✅ Idéntico |

### Funciones RPC
| Función | Propósito | Estado |
|---------|-----------|--------|
| `match_documents` | Búsqueda vectorial RAG | ✅ Existe |
| `execute_custom_sql` | Consultas SQL en datos tabulares | ✅ Existe |

---

## ⚠️ Diferencias en Metadata (JSONB)

### Estructura en Base de Datos (Actual)

Ejemplo del campo `metadata` en tabla `documents`:
```json
{
  "loc": {
    "lines": {
      "to": 1,
      "from": 1
    }
  },
  "source": "blob",
  "file_id": "1Q2dNKzwRotE0T0pmjHd8IhqxjJ4Hb1P5",
  "blobType": "text/plain",
  "file_url": "https://drive.google.com/file/d/1Q2dNKzwRotE0T0pmjHd8IhqxjJ4Hb1P5/view?usp=drivesdk",
  "file_title": "09_lista_empaque.png"
}
```

### Comparación de Campos

| Campo | Workflow n8n | Código Python Esperado | Impacto |
|-------|--------------|------------------------|---------|
| `file_id` | ✅ Presente | ✅ Esperado | ✅ Compatible |
| `file_title` | ✅ Presente | ✅ Esperado | ✅ Compatible |
| `file_url` | ✅ Presente | ✅ Esperado | ✅ Compatible |
| `blobType` | ✅ Presente | Espera `mime_type` | ⚠️ Diferente nombre, mismo propósito |
| `loc` | ✅ Presente | No usado | ℹ️ Extra, no causa problemas |
| `source` | ✅ Presente | No usado | ℹ️ Extra, no causa problemas |
| `chunk_index` | ❌ No existe | Esperado | ℹ️ Opcional, no crítico |
| `file_contents` | ❌ No existe | Esperado (imágenes) | ⚠️ Limita análisis de imágenes |

---

## 🔧 Limitación Identificada: Análisis de Imágenes

### Problema
La función `image_analysis_tool()` en `tools.py` (línea ~290) busca:
```python
file_contents = metadata.get('file_contents')  # Base64 de la imagen
```

**Este campo NO existe** en metadata generada por workflow n8n.

### Comportamiento Actual
- El agente puede **listar** archivos de imagen
- Puede **obtener la URL** de la imagen
- NO puede **analizar el contenido** de la imagen con vision LLM

### Soluciones Posibles

#### Opción A: Modificar Código Python (Recomendado)
**Archivo**: `tools.py`, función `image_analysis_tool()`

**Cambio**: En lugar de leer `file_contents`, descargar imagen desde `file_url`

```python
# Actual (línea ~290):
file_contents = metadata.get('file_contents')

# Propuesto:
file_url = metadata.get('file_url')
# Descargar imagen desde Google Drive
# Convertir a base64
# Enviar a vision LLM
```

**Ventajas**:
- No requiere cambios en workflow n8n
- Mantiene datos existentes intactos
- Más flexible (funciona con URLs externas)

**Desventajas**:
- Requiere permisos de Google Drive
- Latencia adicional (descarga on-demand)

#### Opción B: Modificar Workflow n8n
**Nodo a modificar**: Procesamiento de imágenes

**Cambio**: Agregar conversión de imagen a base64 y guardar en metadata

**Ventajas**:
- Análisis más rápido (sin descarga)
- No requiere permisos adicionales

**Desventajas**:
- Aumenta tamaño de metadata significativamente
- Requiere re-procesar imágenes existentes
- Modificar workflow funcionando

#### Opción C: Deshabilitar Temporalmente
**Cambio**: Documentar limitación y mostrar mensaje claro al usuario

**Ventajas**:
- Sin cambios de código
- Sin riesgos

**Desventajas**:
- Funcionalidad no disponible

---

## 📊 Herramientas del Agente: Estado

| # | Herramienta | Función | Estado | Notas |
|---|-------------|---------|--------|-------|
| 1 | `retrieve_relevant_documents` | Búsqueda RAG semántica | ✅ **Funcional** | Busca en chunks de documentos |
| 2 | `list_documents` | Listar documentos disponibles | ✅ **Funcional** | Muestra metadata de todos los docs |
| 3 | `get_document_content` | Obtener contenido completo | ✅ **Funcional** | Recupera texto completo |
| 4 | `execute_sql_query` | Consultas SQL en CSV/XLSX | ✅ **Funcional** | Análisis de datos tabulares |
| 5 | `web_search` | Búsqueda en internet | ✅ **Funcional** | Usa Brave Search API |
| 6 | `execute_code` | Ejecutar código Python | ✅ **Funcional** | Cálculos y transformaciones |
| 7 | `image_analysis` | Análisis de imágenes | ⚠️ **Limitado** | Obtiene URL, no analiza contenido |

**Resultado**: 6/7 herramientas completamente funcionales (85.7%)

---

## 🔍 Campos Adicionales en Metadata (No Problemáticos)

### `loc` (Location)
```json
"loc": {
  "lines": {
    "to": 1,
    "from": 1
  }
}
```
**Origen**: Extractor de documentos de n8n
**Uso**: Tracking de ubicación del chunk en documento original
**Impacto**: Ninguno (código Python lo ignora)

### `source`
```json
"source": "blob"
```
**Origen**: Tipo de fuente de datos en n8n
**Uso**: Identificar origen del documento
**Impacto**: Ninguno (código Python lo ignora)

### `blobType`
```json
"blobType": "text/plain"
```
**Origen**: Tipo MIME del archivo
**Uso**: Equivalente a `mime_type` esperado por código Python
**Impacto**: Mínimo (código nunca usa este campo directamente)

---

## 📈 Análisis del Workflow n8n

### Arquitectura General
- **68 nodos** en total
- **Procesamiento de 4 tipos de archivos**: PDF, XLSX, CSV, TXT/Docs
- **Chunking**: 400 caracteres, 100 de overlap
- **Embeddings**: OpenAI text-embedding-3-small (1536 dimensiones)
- **Memoria a largo plazo**: Vector store separado con deduplicación

### Flujo de Datos
```
Google Drive Trigger
    ↓
Descarga y Limpieza de IDs
    ↓
Eliminación de Registros Antiguos
    ↓
Insert Document Metadata
    ↓
Extracción por Tipo
    ↓
Normalización de Caracteres
    ↓
Chunking
    ↓
Generación de Embeddings
    ↓
Insert a Vector DB (documents)
```

### Para Datos Tabulares (CSV/XLSX)
```
Extracción de Datos
    ↓
Agregación de Filas
    ↓
Extracción de Schema
    ↓
Insert Document Rows (JSONB)
    ↓
Update Schema en Metadata
```

---

## 🎯 Conclusiones

### ✅ Fortalezas
1. **Alta Compatibilidad**: 85.7% de funcionalidad operativa sin cambios
2. **Datos Íntegros**: Workflow n8n crea estructura correcta
3. **Sin Migraciones**: No se requiere mover o transformar datos
4. **Funciones RPC**: Ya configuradas y funcionales

### ⚠️ Área de Mejora
1. **Análisis de Imágenes**: Requiere adaptación (1 de 7 herramientas)
2. Solución recomendada: Modificar `tools.py` para descargar desde URL

### 🚀 Próximos Pasos Sugeridos
1. Decidir solución para imágenes (A, B o C)
2. Implementar solución elegida
3. Probar análisis de imágenes
4. Documentar uso del agente para equipo

---

## 📝 Referencias

- **Workflow n8n**: `n8n_reference/mi_agente_n8n`
- **Código Python**: `tools.py`, `agent.py`, `clients.py`
- **Progreso Detallado**: `PROGRESO.md`

---

**Mantenido por**: Claude Code
**Última actualización**: 12 de Noviembre de 2024
