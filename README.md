# Pascal AI Assistant - Asistente Inmobiliario Inteligente

## 📋 Descripción

**Pascal AI Assistant** es un asistente virtual inteligente especializado en el sector inmobiliario que utiliza inteligencia artificial para ayudar a los usuarios a encontrar propiedades según sus criterios específicos. El sistema está diseñado para funcionar como un agente conversacional que puede integrarse con WhatsApp a través de webhooks.

### 🎯 Características Principales

- **Agente Conversacional Inteligente**: Utiliza GPT-4 para mantener conversaciones naturales
- **Búsqueda de Propiedades**: Filtra proyectos por distrito, habitaciones, precio y otros criterios
- **Gestión de Memoria**: Mantiene el historial de conversaciones por usuario usando MongoDB
- **Integración con WhatsApp**: Conecta con UltraMsg para envío de mensajes
- **Interfaz Web**: Incluye una interfaz de prueba para simular conversaciones
- **Herramientas Especializadas**: Múltiples herramientas para consultar información inmobiliaria

## 🏗️ Arquitectura del Proyecto

```
pascal-ai-assistant/
├── app/
│   ├── api/
│   │   └── webhook.py          # Endpoint principal para webhooks
│   ├── core/
│   │   ├── agent.py            # Configuración del agente LangChain
│   │   ├── prompts.py          # Prompts del sistema
│   │   ├── tools.py            # Herramientas del agente
│   │   └── memory.py           # Gestión de memoria
│   ├── db/
│   │   └── mongo.py            # Cliente MongoDB y gestión de historial
│   ├── data/
│   │   └── questions.py        # Lista de preguntas predefinidas
│   ├── services/
│   │   └── sender.py           # Servicio para envío de mensajes WhatsApp
│   └── main.py                 # Aplicación FastAPI principal
├── index.html                  # Interfaz web de prueba
├── requirements.txt            # Dependencias de Python
├── env_example.txt             # Variables de entorno de ejemplo
├── test_webhook.sh             # Script de pruebas
└── .render.yaml                # Configuración de despliegue
```

## 🚀 Instalación

### Prerrequisitos

- Python 3.8 o superior
- MongoDB (local o en la nube)
- Cuenta de OpenAI con API key
- Cuenta de UltraMsg (opcional, para WhatsApp)

### Pasos de Instalación

1. **Clonar el repositorio**
   ```bash
   git clone <url-del-repositorio>
   cd pascal-ai-assistant
   ```

2. **Instalar dependencias**
   ```bash
   pip install -r requirements.txt
   ```

3. **Configurar variables de entorno**
   ```bash
   cp env_example.txt .env
   ```
   
   Editar el archivo `.env` con tus credenciales:
   ```env
   # OpenAI API Key
   OPENAI_API_KEY=tu_openai_api_key_aqui
   
   # MongoDB URI
   MONGO_URI=mongodb://localhost:27017
   
   # Configuración del servidor
   HOST=0.0.0.0
   PORT=8000
   ```

4. **Configurar MongoDB**
   - Asegúrate de que MongoDB esté ejecutándose
   - El sistema creará automáticamente la base de datos `chat_memory` y la colección `conversations`

## 🏃‍♂️ Ejecución

### Desarrollo Local

1. **Iniciar el servidor**
   ```bash
   uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload
   ```

2. **Probar la interfaz web**
   - Abre `index.html` en tu navegador
   - O accede a `http://localhost:8000` si configuras el servido de archivos estáticos

3. **Probar el webhook**
   ```bash
   chmod +x test_webhook.sh
   ./test_webhook.sh
   ```

### Despliegue en Producción

El proyecto incluye configuración para Render. Para desplegar:

1. Conecta tu repositorio a Render
2. Configura las variables de entorno en el dashboard de Render
3. El archivo `.render.yaml` se encargará de la configuración automática

## 🔧 Configuración

### Variables de Entorno

| Variable | Descripción | Requerido |
|----------|-------------|-----------|
| `OPENAI_API_KEY` | API key de OpenAI | ✅ |
| `MONGO_URI` | URI de conexión a MongoDB | ✅ |
| `HOST` | Host del servidor | ❌ (default: 0.0.0.0) |
| `PORT` | Puerto del servidor | ❌ (default: 8000) |

### Configuración de WhatsApp (UltraMsg)

Para integrar con WhatsApp, configura las siguientes variables en `app/services/sender.py`:

```python
self.token = 'tu_token_ultramsg'
self.instance_id = 'tu_instance_id'
```

## 📡 API Endpoints

### POST `/webhook`

Endpoint principal para recibir mensajes y generar respuestas.

**Request Body:**
```json
{
  "message": "Hola, busco una propiedad",
  "phone": "+1234567890"
}
```

**Response:**
```json
{
  "reply": "¡Hola! Soy GIA, tu asistente inmobiliario..."
}
```

## 🤖 Funcionalidades del Agente

### Herramientas Disponibles

1. **`obtener_proyectos`**: Lista todos los proyectos disponibles
2. **`proyecto_por_distrito`**: Filtra proyectos por distrito específico
3. **`detalles_proyecto`**: Obtiene información detallada de un proyecto
4. **`propiedades_disponibles`**: Muestra propiedades disponibles en un proyecto
5. **`precio_propiedades`**: Consulta rangos de precios
6. **`imagenes_proyecto_zonas_comunes`**: Muestra imágenes de zonas comunes
7. **`imagenes_propiedades`**: Muestra imágenes de propiedades específicas

### Flujo de Conversación

1. **Saludo inicial** y recopilación de datos del usuario
2. **Identificación de necesidades** (distrito, habitaciones, presupuesto)
3. **Búsqueda de proyectos** según criterios
4. **Presentación de opciones** con detalles relevantes
5. **Gestión de consultas adicionales** (imágenes, precios, promociones)
6. **Agendamiento de visitas** (si el usuario lo solicita)

## 💾 Base de Datos

### Estructura MongoDB

**Colección: `conversations`**

```json
{
  "phone": "+1234567890",
  "status": "open",
  "messages": [
    {
      "type": "human",
      "data": {"content": "Hola, busco una propiedad"}
    },
    {
      "type": "ai", 
      "data": {"content": "¡Hola! Soy GIA, tu asistente inmobiliario..."}
    }
  ]
}
```

## 🧪 Pruebas

### Pruebas del Webhook

```bash
# Prueba básica
curl -X POST http://localhost:8000/webhook \
  -H "Content-Type: application/json" \
  -d '{"message": "Hola", "phone": "+1234567890"}'

# Prueba con búsqueda específica
curl -X POST http://localhost:8000/webhook \
  -H "Content-Type: application/json" \
  -d '{"message": "Busco en Miraflores", "phone": "+1234567890"}'
```

### Interfaz Web de Pruebas

Abre `index.html` en tu navegador para probar la funcionalidad de manera interactiva.

## 🔒 Seguridad

- **CORS**: Configurado para permitir todas las solicitudes (ajustar para producción)
- **Validación**: Los mensajes se validan antes de procesarse
- **Memoria**: Cada conversación se almacena de forma segura por número de teléfono

## 🚨 Solución de Problemas

### Errores Comunes

1. **Error de conexión a MongoDB**
   - Verifica que MongoDB esté ejecutándose
   - Confirma que la URI de conexión sea correcta

2. **Error de API de OpenAI**
   - Verifica que tu API key sea válida
   - Confirma que tengas créditos disponibles

3. **Error de webhook**
   - Verifica que el servidor esté ejecutándose en el puerto correcto
   - Confirma que el formato del JSON sea correcto

### Logs

Los logs se muestran en la consola del servidor. Para debugging más detallado, puedes habilitar logs adicionales modificando el nivel de logging en `app/main.py`.

## 🤝 Contribución

1. Fork el proyecto
2. Crea una rama para tu feature (`git checkout -b feature/AmazingFeature`)
3. Commit tus cambios (`git commit -m 'Add some AmazingFeature'`)
4. Push a la rama (`git push origin feature/AmazingFeature`)
5. Abre un Pull Request

## 📄 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo `LICENSE` para más detalles.

## 📞 Soporte

Para soporte técnico o preguntas sobre el proyecto, contacta al equipo de desarrollo.

---

**Desarrollado con ❤️ para revolucionar la experiencia inmobiliaria**
