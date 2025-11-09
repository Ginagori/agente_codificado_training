# CLAUDE.md

Este archivo proporciona una guía integral para Claude Code cuando se trabaja con código Python en este repositorio.

---

## 🤖 Sobre Este Proyecto

Este es un **Pydantic AI Agent** avanzado que combina múltiples capacidades de IA:

- **Agentic RAG**: Consulta documentos con inteligencia contextual
- **Memoria a Largo Plazo**: El agente recuerda conversaciones previas (usando mem0)
- **Búsqueda Web**: Búsqueda en internet usando Brave API
- **Análisis de Imágenes**: Analiza imágenes con modelos de visión
- **Ejecución de Código**: Genera y ejecuta código Python de forma segura
- **Multi-LLM**: Compatible con OpenAI, OpenRouter, o Ollama local

### Configuración Actual del Proyecto

- **Framework**: Pydantic AI
- **LLM Provider**: OpenAI (gpt-4o-mini)
- **Embeddings**: text-embedding-3-small (1536 dimensiones)
- **Base de Datos**: Supabase Cloud (PostgreSQL + pgvector)
- **Búsqueda Web**: Brave Search API
- **UI**: Streamlit

### Entornos Virtuales

Este proyecto usa **DOS entornos virtuales separados**:

1. **`venv/`** (raíz): Para el agente principal
2. **`RAG_Pipeline/venv/`**: Para el pipeline de procesamiento de documentos

**IMPORTANTE**: Activar el entorno correcto según la tarea:
```bash
# Para trabajar con el agente principal:
cd 4_Pydantic_AI_Agent
venv\Scripts\activate

# Para trabajar con RAG Pipeline:
cd 4_Pydantic_AI_Agent\RAG_Pipeline
venv\Scripts\activate
```

### Arquitectura Real del Proyecto

```
4_Pydantic_AI_Agent/
├── .env                        # Configuración (NO en Git)
├── CLAUDE.md                   # Esta guía
├── PROGRESO.md                 # Seguimiento del proyecto
├── README.md                   # Documentación principal
├── requirements.txt            # Dependencias del agente
├── venv/                       # Entorno virtual del agente
│
├── agent.py                    # Implementación principal del agente Pydantic AI
├── clients.py                  # Configuración de clientes (LLM, DB, memoria)
├── prompt.py                   # Template del sistema de prompts
├── tools.py                    # Implementación de herramientas del agente
├── streamlit_ui.py            # Interfaz de usuario básica
│
├── RAG_Pipeline/               # Pipeline de procesamiento de documentos
│   ├── requirements.txt        # Dependencias del pipeline
│   ├── venv/                   # Entorno virtual del pipeline
│   ├── common/                 # Funcionalidad común RAG
│   │   ├── db_handler.py      # Operaciones de BD para RAG
│   │   └── text_processor.py  # Procesamiento de texto para vector DB
│   ├── Google_Drive/          # Pipeline de Google Drive
│   │   ├── main.py
│   │   ├── drive_watcher.py
│   │   └── config.json
│   └── Local_Files/           # Pipeline de archivos locales
│       ├── main.py
│       ├── file_watcher.py
│       └── config.json
│
├── sql/                        # Scripts SQL para Supabase
│   ├── documents.sql
│   ├── document_metadata.sql
│   ├── document_rows.sql
│   └── execute_sql_rpc.sql
│
└── tests/                      # Tests del proyecto
```

### Comandos Específicos del Proyecto

#### Instalación Inicial (Pendiente)
```bash
# 1. Instalar dependencias del agente
cd 4_Pydantic_AI_Agent
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt

# 2. Instalar dependencias del RAG Pipeline
cd RAG_Pipeline
python -m venv venv
venv\Scripts\activate
pip install -r requirements.txt
```

#### Ejecutar el Agente
```bash
cd 4_Pydantic_AI_Agent
venv\Scripts\activate
streamlit run streamlit_ui.py
```

#### Ejecutar RAG Pipeline - Archivos Locales
```bash
cd 4_Pydantic_AI_Agent\RAG_Pipeline
venv\Scripts\activate
python Local_Files/main.py
```

#### Ejecutar RAG Pipeline - Google Drive
```bash
cd 4_Pydantic_AI_Agent\RAG_Pipeline
venv\Scripts\activate
python Google_Drive/main.py
```

### Variables de Entorno Requeridas

El archivo `.env` debe contener (ver `.env.example`):

```bash
# LLM Configuration
LLM_PROVIDER=openai
LLM_BASE_URL=https://api.openai.com/v1
LLM_API_KEY=tu-api-key
LLM_CHOICE=gpt-4o-mini
VISION_LLM_CHOICE=gpt-4o-mini

# Embeddings
EMBEDDING_PROVIDER=openai
EMBEDDING_BASE_URL=https://api.openai.com/v1
EMBEDDING_API_KEY=tu-api-key
EMBEDDING_MODEL_CHOICE=text-embedding-3-small

# Database (Supabase)
DATABASE_URL=postgresql://...
SUPABASE_URL=https://....supabase.co
SUPABASE_SERVICE_KEY=...

# Web Search
BRAVE_API_KEY=tu-brave-api-key
```

### Seguimiento del Proyecto

- **Ver progreso actual**: Lee `PROGRESO.md`
- **Ver documentación principal**: Lee `README.md`
- **Esta guía**: Mejores prácticas de desarrollo

---

## Filosofía Central de Desarrollo

### KISS (Keep It Simple, Stupid)

La simplicidad debe ser un objetivo clave en el diseño. Elige soluciones sencillas sobre las complejas siempre que sea posible. Las soluciones simples son más fáciles de entender, mantener y depurar.

### YAGNI (You Aren't Gonna Need It)

Evita construir funcionalidad basándote en especulaciones. Implementa características solo cuando se necesiten, no cuando anticipes que podrían ser útiles en el futuro.

### Principios de Diseño

- **Inversión de Dependencias**: Los módulos de alto nivel no deben depender de módulos de bajo nivel. Ambos deben depender de abstracciones.
- **Principio Abierto/Cerrado**: Las entidades de software deben estar abiertas para extensión pero cerradas para modificación.
- **Responsabilidad Única**: Cada función, clase y módulo debe tener un propósito claro.
- **Fallo Rápido**: Verifica posibles errores temprano y lanza excepciones inmediatamente cuando ocurran problemas.

## 🧱 Estructura de Código y Modularidad

### Límites de Archivos y Funciones

- **Nunca crear un archivo de más de 500 líneas de código**. Si se acerca a este límite, refactoriza dividiéndolo en módulos.
- **Las funciones deben tener menos de 50 líneas** con una responsabilidad única y clara.
- **Las clases deben tener menos de 100 líneas** y representar un solo concepto o entidad.
- **Organiza el código en módulos claramente separados**, agrupados por característica o responsabilidad.
- **La longitud de línea debe ser máximo 100 caracteres** regla de ruff en pyproject.toml
- **IMPORTANTE**: Este proyecto usa **DOS entornos virtuales separados**:
  - `venv/` en la raíz para el agente principal
  - `RAG_Pipeline/venv/` para el pipeline RAG
  - Activar el correcto según la tarea

### Organización de Archivos del Proyecto

Este proyecto está organizado en dos componentes principales:

1. **Agente Principal** (raíz del proyecto):
   - `agent.py`: Lógica principal del agente con Pydantic AI
   - `clients.py`: Configuración de clientes (LLM, DB, memoria)
   - `tools.py`: Herramientas disponibles para el agente
   - `prompt.py`: Sistema de prompts
   - `streamlit_ui.py`: Interfaz de usuario

2. **RAG Pipeline** (carpeta `RAG_Pipeline/`):
   - `common/`: Funciones compartidas para procesamiento de documentos
   - `Local_Files/`: Pipeline para archivos locales
   - `Google_Drive/`: Pipeline para Google Drive

Ver la arquitectura completa en la sección superior de este documento.

## 🛠️ Entorno de Desarrollo

### Gestión de Paquetes UV

Este proyecto usa UV para gestión ultrarrápida de paquetes y entornos Python.

```bash
# Instalar UV (si no está ya instalado)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Crear entorno virtual
uv venv

# Sincronizar dependencias
uv sync

# Agregar un paquete ***NUNCA ACTUALIZAR UNA DEPENDENCIA DIRECTAMENTE EN PYPROJECT.toml***
# SIEMPRE USAR UV ADD
uv add requests

# Agregar dependencia de desarrollo
uv add --dev pytest ruff mypy

# Remover un paquete
uv remove requests

# Ejecutar comandos en el entorno
uv run python script.py
uv run pytest
uv run ruff check .

# Instalar versión específica de Python
uv python install 3.12
```

### Comandos de Desarrollo

```bash
# Ejecutar todas las pruebas
uv run pytest

# Ejecutar pruebas específicas con salida detallada
uv run pytest tests/test_module.py -v

# Ejecutar pruebas con cobertura
uv run pytest --cov=src --cov-report=html

# Formatear código
uv run ruff format .

# Verificar linting
uv run ruff check .

# Corregir problemas de linting automáticamente
uv run ruff check --fix .

# Verificación de tipos
uv run mypy src/

# Ejecutar hooks de pre-commit
uv run pre-commit run --all-files
```

## 📋 Estilo y Convenciones

### Guía de Estilo Python

- **Sigue PEP8** con estas elecciones específicas:
- **Siempre usa type hints** para firmas de función y atributos de clase
- **Formatea con `ruff format`** (alternativa más rápida a Black)
- **Usa `pydantic` v2** para validación de datos y gestión de configuraciones

### Estándares de Docstring

Usa docstrings estilo Google para todas las funciones públicas, clases y módulos:

```python
def calculate_discount(
    price: Decimal,
    discount_percent: float,
    min_amount: Decimal = Decimal("0.01")
) -> Decimal:
    """
    Calcula el precio con descuento para un producto.

    Args:
        price: Precio original del producto
        discount_percent: Porcentaje de descuento (0-100)
        min_amount: Precio final mínimo permitido

    Returns:
        Precio final después de aplicar el descuento

    Raises:
        ValueError: Si discount_percent no está entre 0 y 100
        ValueError: Si el precio final sería menor que min_amount

    Example:
        >>> calculate_discount(Decimal("100"), 20)
        Decimal('80.00')
    """
```

### Convenciones de Nombrado

- **Variables y funciones**: `snake_case`
- **Clases**: `PascalCase`
- **Constantes**: `UPPER_SNAKE_CASE`
- **Atributos/métodos privados**: `_guion_bajo_inicial`
- **Alias de tipos**: `PascalCase`
- **Valores de Enum**: `UPPER_SNAKE_CASE`

## 🧪 Estrategia de Pruebas

### Desarrollo Dirigido por Pruebas (TDD)

1. **Escribe la prueba primero** - Define el comportamiento esperado antes de la implementación
2. **Observa que falle** - Asegúrate de que la prueba realmente prueba algo
3. **Escribe código mínimo** - Solo lo suficiente para hacer que la prueba pase
4. **Refactoriza** - Mejora el código mientras mantienes las pruebas en verde
5. **Repite** - Una prueba a la vez

### Mejores Prácticas de Pruebas

```python
# Siempre usa fixtures de pytest para configurarimeimport pytest
from datetime import datetime

@pytest.fixture
def sample_user():
    """Proporciona un usuario de ejemplo para pruebas."""
    return User(
        id=123,
        name="Usuario de Prueba",
        email="test@example.com",
        created_at=datetime.now()
    )

# Usa nombres descriptivos para las pruebas
def test_user_can_update_email_when_valid(sample_user):
    """Prueba que los usuarios pueden actualizar su email con entrada válida."""
    new_email = "newemail@example.com"
    sample_user.update_email(new_email)
    assert sample_user.email == new_email

# Prueba casos límite y condiciones de error
def test_user_update_email_fails_with_invalid_format(sample_user):
    """Prueba que los formatos de email inválidos son rechazados."""
    with pytest.raises(ValidationError) as exc_info:
        sample_user.update_email("not-an-email")
    assert "Invalid email format" in str(exc_info.value)
```

### Organización de Pruebas

- Pruebas unitarias: Prueban funciones/métodos individuales de forma aislada
- Pruebas de integración: Prueban interacciones entre componentes
- Pruebas de extremo a extremo: Prueban flujos completos de usuario
- Mantén los archivos de prueba junto al código que prueban
- Usa `conftest.py` para fixtures compartidos
- Apunta a 80%+ de cobertura de código, pero enfócate en rutas críticas

## 🚨 Manejo de Errores

### Mejores Prácticas de Excepciones

```python
# Crea excepciones personalizadas para tu dominio
class PaymentError(Exception):
    """Excepción base para errores relacionados con pagos."""
    pass

class InsufficientFundsError(PaymentError):
    """Se lanza cuando la cuenta tiene fondos insuficientes."""
    def __init__(self, required: Decimal, available: Decimal):
        self.required = required
        self.available = available
        super().__init__(
            f"Fondos insuficientes: requeridos {required}, disponibles {available}"
        )

# Usa manejo específico de excepciones
try:
    process_payment(amount)
except InsufficientFundsError as e:
    logger.warning(f"Pago falló: {e}")
    return PaymentResult(success=False, reason="insufficient_funds")
except PaymentError as e:
    logger.error(f"Error de pago: {e}")
    return PaymentResult(success=False, reason="payment_error")

# Usa gestores de contexto para manejo de recursos
from contextlib import contextmanager

@contextmanager
def database_transaction():
    """Proporciona un ámbito transaccional para operaciones de base de datos."""
    conn = get_connection()
    trans = conn.begin_transaction()
    try:
        yield conn
        trans.commit()
    except Exception:
        trans.rollback()
        raise
    finally:
        conn.close()
```

### Estrategia de Logging

```python
import logging
from functools import wraps

# Configurar logging estructurado
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

# Registrar entrada/salida de función para depuración
def log_execution(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        logger.debug(f"Entrando a {func.__name__}")
        try:
            result = func(*args, **kwargs)
            logger.debug(f"Saliendo de {func.__name__} exitosamente")
            return result
        except Exception as e:
            logger.exception(f"Error en {func.__name__}: {e}")
            raise
    return wrapper
```

## 🔧 Gestión de Configuración

### Variables de Entorno y Configuraciones

```python
from pydantic_settings import BaseSettings
from functools import lru_cache

class Settings(BaseSettings):
    """Configuraciones de aplicación con validación."""
    app_name: str = "MyApp"
    debug: bool = False
    database_url: str
    redis_url: str = "redis://localhost:6379"
    api_key: str
    max_connections: int = 100

    class Config:
        env_file = ".env"
        env_file_encoding = "utf-8"
        case_sensitive = False

@lru_cache()
def get_settings() -> Settings:
    """Obtiene instancia de configuraciones en caché."""
    return Settings()

# Uso
settings = get_settings()
```

## 🏗️ Modelos de Datos y Validación

### Modelos Pydantic de Ejemplo estrictos con pydantic v2

```python
from pydantic import BaseModel, Field, validator, EmailStr
from datetime import datetime
from typing import Optional, List
from decimal import Decimal

class ProductBase(BaseModel):
    """Modelo base de producto con campos comunes."""
    name: str = Field(..., min_length=1, max_length=255)
    description: Optional[str] = None
    price: Decimal = Field(..., gt=0, decimal_places=2)
    category: str
    tags: List[str] = []

    @validator('price')
    def validate_price(cls, v):
        if v > Decimal('1000000'):
            raise ValueError('El precio no puede exceder 1,000,000')
        return v

    class Config:
        json_encoders = {
            Decimal: str,
            datetime: lambda v: v.isoformat()
        }

class ProductCreate(ProductBase):
    """Modelo para crear nuevos productos."""
    pass

class ProductUpdate(BaseModel):
    """Modelo para actualizar productos - todos los campos opcionales."""
    name: Optional[str] = Field(None, min_length=1, max_length=255)
    description: Optional[str] = None
    price: Optional[Decimal] = Field(None, gt=0, decimal_places=2)
    category: Optional[str] = None
    tags: Optional[List[str]] = None

class Product(ProductBase):
    """Modelo completo de producto con campos de base de datos."""
    id: int
    created_at: datetime
    updated_at: datetime
    is_active: bool = True

    class Config:
        from_attributes = True  # Habilitar modo ORM
```

## 🔄 Flujo de Trabajo Git

### Estrategia de Ramas

- `main` - Código listo para producción
- `develop` - Rama de integración para características
- `feature/*` - Nuevas características
- `fix/*` - Corrección de errores
- `docs/*` - Actualizaciones de documentación
- `refactor/*` - Refactorización de código
- `test/*` - Adiciones o correcciones de pruebas

### Formato de Mensajes de Commit

Nunca incluir "claude code" o "escrito por claude code" en mensajes de commit

```
<tipo>(<ámbito>): <asunto>

<cuerpo>

<pie>
```

Tipos: feat, fix, docs, style, refactor, test, chore

Ejemplo:

```
feat(auth): agregar autenticación de dos factores

- Implementar generación y validación TOTP
- Agregar generación de código QR para apps autenticadoras
- Actualizar modelo de usuario con campos 2FA

Closes #123
```

## 🗄️ Estándares de Nombrado de Base de Datos

### Claves Primarias Específicas de Entidad

Todas las tablas de base de datos usan claves primarias específicas de entidad para claridad y consistencia:

```sql
-- ✅ ESTANDARIZADO: Claves primarias específicas de entidad
sessions.session_id UUID PRIMARY KEY
leads.lead_id UUID PRIMARY KEY
messages.message_id UUID PRIMARY KEY
daily_metrics.daily_metric_id UUID PRIMARY KEY
agencies.agency_id UUID PRIMARY KEY
```

### Convenciones de Nombrado de Campos

```sql
-- Claves primarias: {entidad}_id
session_id, lead_id, message_id

-- Claves foráneas: {entidad_referenciada}_id
session_id REFERENCES sessions(session_id)
agency_id REFERENCES agencies(agency_id)

-- Timestamps: {acción}_at
created_at, updated_at, started_at, expires_at

-- Booleanos: is_{estado}
is_connected, is_active, is_qualified

-- Conteos: {entidad}_count
message_count, lead_count, notification_count

-- Duraciones: {propiedad}_{unidad}
duration_seconds, timeout_minutes
```

### Auto-derivación del Patrón Repository

El BaseRepository mejorado deriva automáticamente nombres de tabla y claves primarias:

```python
# ✅ ESTANDARIZADO: Repositorios basados en convención
class LeadRepository(BaseRepository[Lead]):
    def __init__(self):
        super().__init__()  # Auto-deriva "leads" y "lead_id"

class SessionRepository(BaseRepository[AvatarSession]):
    def __init__(self):
        super().__init__()  # Auto-deriva "sessions" y "session_id"
```

**Beneficios**:

- ✅ Esquema auto-documentado
- ✅ Relaciones de clave foránea claras
- ✅ Elimina overrides de métodos de repositorio
- ✅ Consistente con patrones de nombrado de entidades

### Alineación Modelo-Base de Datos

Los modelos reflejan exactamente los campos de base de datos para eliminar complejidad de mapeo de campos:

```python
# ✅ ESTANDARIZADO: Modelos reflejan exactamente la base de datos
class Lead(BaseModel):
    lead_id: UUID = Field(default_factory=uuid4)  # Coincide con campo de BD
    session_id: UUID                               # Coincide con campo de BD
    agency_id: str                                 # Coincide con campo de BD
    created_at: datetime = Field(default_factory=lambda: datetime.now(UTC))

    model_config = ConfigDict(
        use_enum_values=True,
        populate_by_name=True,
        alias_generator=None  # Usar nombres exactos de campos
    )
```

### Estándares de Rutas API

```python
# ✅ ESTANDARIZADO: RESTful con nombrado consistente de parámetros
router = APIRouter(prefix="/api/v1/leads", tags=["leads"])

@router.get("/{lead_id}")           # GET /api/v1/leads/{lead_id}
@router.put("/{lead_id}")           # PUT /api/v1/leads/{lead_id}
@router.delete("/{lead_id}")        # DELETE /api/v1/leads/{lead_id}

# Sub-recursos
@router.get("/{lead_id}/messages")  # GET /api/v1/leads/{lead_id}/messages
@router.get("/agency/{agency_id}")  # GET /api/v1/leads/agency/{agency_id}
```

Para estándares completos de nombrado, ver NAMING_CONVENTIONS.md.

## 📝 Estándares de Documentación

### Documentación de Código

- Cada módulo debe tener un docstring explicando su propósito
- Las funciones públicas deben tener docstrings completos
- La lógica compleja debe tener comentarios en línea con prefijo `# Razón:`
- Mantén README.md actualizado con instrucciones de configuración y ejemplos
- Mantén CHANGELOG.md para historial de versiones

### Documentación de API

```python
from fastapi import APIRouter, HTTPException, status
from typing import List

router = APIRouter(prefix="/products", tags=["products"])

@router.get(
    "/",
    response_model=List[Product],
    summary="Listar todos los productos",
    description="Recuperar una lista paginada de todos los productos activos"
)
async def list_products(
    skip: int = 0,
    limit: int = 100,
    category: Optional[str] = None
) -> List[Product]:
    """
    Recuperar productos con filtrado opcional.

    - **skip**: Número de productos a omitir (para paginación)
    - **limit**: Número máximo de productos a devolver
    - **category**: Filtrar por categoría de producto
    """
    # Implementación aquí
```

## 🚀 Consideraciones de Rendimiento

### Pautas de Optimización

- Perfilar antes de optimizar - usa `cProfile` o `py-spy`
- Usa `lru_cache` para cálculos costosos
- Prefiere generadores para conjuntos de datos grandes
- Usa `asyncio` para operaciones limitadas por I/O
- Considera `multiprocessing` para tareas limitadas por CPU
- Cachea consultas de base de datos apropiadamente

### Ejemplo de Optimización

```python
from functools import lru_cache
import asyncio
from typing import AsyncIterator

@lru_cache(maxsize=1000)
def expensive_calculation(n: int) -> int:
    """Cachea resultados de cálculos costosos."""
    # Cálculo complejo aquí
    return result

async def process_large_dataset() -> AsyncIterator[dict]:
    """Procesa conjunto de datos grande sin cargar todo en memoria."""
    async with aiofiles.open('large_file.json', mode='r') as f:
        async for line in f:
            data = json.loads(line)
            # Procesar y generar cada elemento
            yield process_item(data)
```

## 🛡️ Mejores Prácticas de Seguridad

### Pautas de Seguridad

- Nunca confirmar secretos - usa variables de entorno
- Valida toda entrada de usuario con Pydantic
- Usa consultas parametrizadas para operaciones de base de datos
- Implementa limitación de velocidad para APIs
- Mantén dependencias actualizadas con `uv`
- Usa HTTPS para todas las comunicaciones externas
- Implementa autenticación y autorización apropiadas

### Ejemplo de Implementación de Seguridad

```python
from passlib.context import CryptContext
import secrets

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")

def hash_password(password: str) -> str:
    """Hashea contraseña usando bcrypt."""
    return pwd_context.hash(password)

def verify_password(plain_password: str, hashed_password: str) -> bool:
    """Verifica una contraseña contra su hash."""
    return pwd_context.verify(plain_password, hashed_password)

def generate_secure_token(length: int = 32) -> str:
    """Genera un token aleatorio criptográficamente seguro."""
    return secrets.token_urlsafe(length)
```

## 🔍 Herramientas de Depuración

### Comandos de Depuración

```bash
# Depuración interactiva con ipdb
uv add --dev ipdb
# Agregar breakpoint: import ipdb; ipdb.set_trace()

# Perfilado de memoria
uv add --dev memory-profiler
uv run python -m memory_profiler script.py

# Perfilado de línea
uv add --dev line-profiler
# Agregar decorador @profile a funciones

# Depurar con tracebacks ricos
uv add --dev rich
# En código: from rich.traceback import install; install()
```

## 📊 Monitoreo y Observabilidad

### Logging Estructurado

```python
import structlog

logger = structlog.get_logger()

# Log con contexto
logger.info(
    "pago_procesado",
    user_id=user.id,
    amount=amount,
    currency="USD",
    processing_time=processing_time
)
```

## 📚 Recursos Útiles

### Herramientas Esenciales

- Documentación UV: https://github.com/astral-sh/uv
- Ruff: https://github.com/astral-sh/ruff
- Pytest: https://docs.pytest.org/
- Pydantic: https://docs.pydantic.dev/
- FastAPI: https://fastapi.tiangolo.com/

### Mejores Prácticas Python

- PEP 8: https://pep8.org/
- PEP 484 (Type Hints): https://www.python.org/dev/peps/pep-0484/
- Guía del Autoestopista de Python: https://docs.python-guide.org/

## ⚠️ Notas Importantes

- **NUNCA ASUMIR O ADIVINAR** - Cuando tengas dudas, pide aclaraciones
- **Siempre verificar rutas de archivos y nombres de módulos** antes de usar
- **Mantén CLAUDE.md actualizado** al agregar nuevos patrones o dependencias
- **Prueba tu código** - Ninguna característica está completa sin pruebas
- **Documenta tus decisiones** - Los futuros desarrolladores (incluyéndote) te lo agradecerán

## 🔍 Requisitos de Comando de Búsqueda

**CRÍTICO**: Siempre usa `rg` (ripgrep) en lugar de comandos tradicionales `grep` y `find`:

```bash
# ❌ No uses grep
grep -r "pattern" .

# ✅ Usa rg en su lugar
rg "pattern"

# ❌ No uses find con name
find . -name "*.py"

# ✅ Usa rg con filtrado de archivos
rg --files | rg "\.py$"
# o
rg --files -g "*.py"
```

**Reglas de Cumplimiento**:

```
(
    r"^grep\b(?!.*\|)",
    "Usa 'rg' (ripgrep) en lugar de 'grep' para mejor rendimiento y características",
),
(
    r"^find\s+\S+\s+-name\b",
    "Usa 'rg --files | rg pattern' o 'rg --files -g pattern' en lugar de 'find -name' para mejor rendimiento",
),
```

## 🚀 Resumen del Flujo de Trabajo GitHub Flow

```
main (protegida) ←── PR ←── feature/tu-característica
       ↓                              ↑
    deploy                      desarrollo
```

### Flujo Diario:

1. `git checkout main && git pull origin main`
2. `git checkout -b feature/nueva-característica`
3. Hacer cambios + pruebas
4. `git push origin feature/nueva-característica`
5. Crear PR → Revisar → Fusionar a main

---

*Este documento es una guía viva. Actualízalo a medida que el proyecto evolucione y emerjan nuevos patrones.*
