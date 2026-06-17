# Prueba Técnica: Sistema Híbrido de Recomendación y Personalización Retail

Este repositorio contiene una solución reproducible para una prueba técnica de Retail que combina **Machine Learning tradicional** y **GenAI** para segmentar clientes y generar mensajes personalizados de fidelización.

## Objetivo

Diseñar un prototipo que:

1. Extraiga métricas de cliente tipo RFM desde tablas relacionales.
2. Genere un dataset sintético de clientes.
3. Clasifique clientes en segmentos comerciales.
4. Genere mensajes personalizados con un LLM usando salida JSON estructurada.

## Estructura del repositorio

```text
.
├── Prueba_Tecnica_Cochez_GenAI_ML.ipynb
├── README.md
└── requirements.txt
```

Al ejecutar el notebook también se generan:

```text
clientes_sinteticos_cochez.csv
campania_personalizada_cochez.csv
```

## Arquitectura de la solución

```text
Base SQL transaccional
        │
        ▼
Extracción RFM por cliente
        │
        ▼
Dataset sintético / Feature Engineering
        │
        ▼
Modelo ML: Random Forest
        │
        ▼
Segmento del cliente
        │
        ▼
LLM + Prompt Engineering + JSON Schema
        │
        ▼
Mensaje personalizado para campaña CRM
```

## Cómo correr el proyecto

1. Clonar el repositorio:

```bash
git clone <URL_DEL_REPOSITORIO>
cd <NOMBRE_DEL_REPOSITORIO>
```

2. Crear ambiente virtual:

```bash
python -m venv .venv
source .venv/bin/activate  # Linux/Mac
.venv\Scripts\activate     # Windows
```

3. Instalar dependencias:

```bash
pip install -r requirements.txt
```

4. Abrir el notebook:

```bash
jupyter notebook Prueba_Tecnica_Cochez_GenAI_ML.ipynb
```

## Uso de API Key

El notebook funciona por defecto con `mock=True`, por lo que no consume créditos de ninguna API.

Para usar OpenAI realmente:

```bash
export OPENAI_API_KEY="tu_api_key"
```

En Windows PowerShell:

```powershell
$env:OPENAI_API_KEY="tu_api_key"
```

Luego cambiar en el notebook:

```python
generate_personalized_message(..., mock=False)
```

## Decisiones técnicas

### Dataset sintético

Se simulan variables RFM:

- `recencia_dias`: días desde la última compra.
- `frecuencia`: número total de compras/transacciones.
- `monto_total_gastado`: valor monetario acumulado.
- `categoria_mas_comprada`: categoría favorita del cliente.

La variable objetivo se construye con reglas de negocio para representar tres segmentos:

- `Alto Valor`
- `En Riesgo`
- `Estandar`

### Modelo seleccionado

Se eligió **Random Forest** porque:

- Funciona bien en datos tabulares.
- Captura relaciones no lineales.
- Es robusto ante ruido.
- Permite interpretar importancia de variables.
- Es fácil de explicar a negocio.

### Métricas

Se reportan:

- Accuracy.
- F1 macro.
- Classification report.
- Matriz de confusión.
- Validación cruzada estratificada.

Se prioriza F1 macro porque las clases pueden estar desbalanceadas.

### GenAI

La generación usa:

- Asignación de rol.
- Few-shot prompting.
- Salida estructurada mediante JSON Schema.
- Variables de entorno para API keys.
- Modo mock para reproducibilidad.

El JSON esperado contiene:

```json
{
  "asunto": "string",
  "cuerpo_mensaje": "string",
  "cupon_descuento_sugerido": "string"
}
```

## Cómo escalar a producción

Una arquitectura productiva podría incluir:

1. **Data Warehouse / Lakehouse**
   - BigQuery, Snowflake, Redshift, Databricks o Synapse.

2. **Pipelines ETL/ELT**
   - Airflow, dbt, Prefect o herramientas cloud nativas.

3. **Feature Store**
   - Reutilización de variables RFM, categorías, frecuencia, margen y engagement.

4. **Servicio de inferencia ML**
   - API en FastAPI desplegada en Cloud Run, ECS, AKS o Kubernetes.

5. **Servicio GenAI**
   - Capa separada con control de prompts, validación JSON, retries, logging y control de costos.

6. **Integración CRM/CDP**
   - Salesforce, HubSpot, Braze, Iterable, Customer.io o plataforma interna.

7. **Experimentación A/B**
   - Comparar campañas genéricas vs personalizadas.
   - Medir apertura, clic, conversión, redención de cupón y retención.

8. **Monitoreo**
   - Drift de datos.
   - Rendimiento del modelo.
   - Tasa de errores del LLM.
   - Costos por segmento/campaña.

## Consideraciones éticas y de privacidad

- No incluir datos sensibles en prompts.
- Minimizar información enviada al LLM.
- Usar IDs internos y no datos personales cuando sea posible.
- Validar que los mensajes no sean discriminatorios ni engañosos.
- Registrar consentimiento y preferencias de comunicación.

## Resultado esperado

El notebook produce una tabla final lista para activación de campaña con:

- ID del cliente.
- Segmento ML.
- Categoría más comprada.
- Monto gastado.
- Asunto personalizado.
- Cuerpo del mensaje.
- Cupón sugerido.
