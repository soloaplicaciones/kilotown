# Guía de Extensión para Desarrolladores

Esta guía explica cómo extender Hermes Agent con nuevos plugins, utilizar la API para desarrolladores, seguir patrones recomendados y ver ejemplos prácticos.

## Cómo extender Hermes Agent con nuevos plugins

Hermes Agent está diseñado para ser extensible mediante un sistema de plugins. Para crear un nuevo plugin:

1. Crea una nueva clase que implemente la interfaz `Plugin`.
2. Registra tu plugin en el archivo de configuración de plugins.
3. Asegúrate de que tu plugin siga el ciclo de vida de inicialización y limpieza.

### Estructura básica de un plugin

```typescript
import { Plugin, PluginContext } from '@hermes-agent/core';

export class MiPlugin implements Plugin {
  private context?: PluginContext;

  async initialize(context: PluginContext): Promise<void> {
    this.context = context;
    // Lógica de inicialización
    console.log('MiPlugin inicializado');
  }

  async shutdown(): Promise<void> {
    // Lógica de limpieza
    console.log('MiPlugin detenido');
    this.context = undefined;
  }

  getMetadata() {
    return {
      name: 'mi-plugin',
      version: '1.0.0',
      description: 'Descripción de mi plugin',
    };
  }
}
```

## API para desarrolladores

Hermes Agent expone una API que permite a los plugins interactuar con el núcleo y otros componentes.

### PluginContext

El `PluginContext` proporciona acceso a:

- `eventBus`: Para emitir y escuchar eventos.
- `config`: Acceso a la configuración del agente.
- `logger`: Sistema de logging.
- `storage`: Almacenamiento persistente.
- `dependencyInjection`: Contenedor de inyección de dependencias.

### Ejemplo de uso del eventBus

```typescript
// Emitir un evento
this.context.eventBus.emit('mi-evento', { datos: 'importantes' });

// Escuchar un evento
this.context.eventBus.on('otro-evento', (data) => {
  console.log('Recibido evento:', data);
});
```

## Patrones para agregar funcionalidades

### Patrón de Observador

Utiliza el eventBus para implementar comunicación entre plugins sin acoplamiento directo.

### Patrón de Estrategia

Define interfaces comunes para funcionalidades que pueden tener múltiples implementaciones (por ejemplo, diferentes proveedores de almacenamiento).

### Patrón de Fabrica

Utiliza el contenedor de inyección de dependencias para crear instancias de servicios de manera flexible.

## Ejemplos de extensiones

### Ejemplo 1: Plugin de notificaciones

Un plugin que envía notificaciones por correo electrónico cuando ocurre ciertos eventos.

```typescript
import { Plugin, PluginContext } from '@hermes-agent/core';

export class NotificacionesPlugin implements Plugin {
  private context?: PluginContext;

  async initialize(context: PluginContext): Promise<void> {
    this.context = context;
    // Escuchar eventos que déclenchen notificaciones
    this.context.eventBus.on('error.critico', this.handleError.bind(this));
    this.context.eventBus.on('tarea.completada', this.handleTaskComplete.bind(this));
  }

  private async handleError(data: any) {
    // Lógica para enviar email de error
    console.log(`Enviando notificación de error: ${data.message}`);
  }

  private async handleTaskComplete(data: any) {
    // Lógica para enviar email de completado
    console.log(`Enviando notificación de completado: ${data.taskId}`);
  }

  async shutdown(): Promise<void> {
    this.context = undefined;
  }

  getMetadata() {
    return {
      name: 'notificaciones',
      version: '1.0.0',
      description: 'Plugin para enviar notificaciones por email',
    };
  }
}
```

### Ejemplo 2: Plugin de métricas

Un plugin que recopila y exporta métricas de rendimiento.

```typescript
import { Plugin, PluginContext } from '@hermes-agent/core';

export class MetricasPlugin implements Plugin {
  private context?: PluginContext;
  private intervalId?: NodeJS.Timeout;

  async initialize(context: PluginContext): Promise<void> {
    this.context = context;
    // Recopilar métricas cada 30 segundos
    this.intervalId = setInterval(() => this.collectMetrics(), 30000);
  }

  private collectMetrics() {
    // Lógica para recopilar métricas (memoria, CPU, latencia, etc.)
    const metricas = {
      timestamp: Date.now(),
      memory: process.memoryUsage(),
      // ... otras métricas
    };
    this.context.eventBus.emit('metricas.recopiladas', metricas);
  }

  async shutdown(): Promise<void> {
    if (this.intervalId) {
      clearInterval(this.intervalId);
    }
    this.context = undefined;
  }

  getMetadata() {
    return {
      name: 'metricas',
      version: '1.0.0',
      description: 'Plugin para recopilar y exportar métricas',
    };
  }
}
```

## Guía paso a paso

Sigue estos pasos para crear, probar y desplegar tu primer plugin:

### Paso 1: Configurar el entorno de desarrollo

1. Asegúrate de tener Node.js >= 16 y npm instalados.
2. Clona el repositorio de Hermes Agent.
3. Instala las dependencias: `npm install`

### Paso 2: Crear el plugin

1. Crea una nueva carpeta en `plugins/` para tu plugin (por ejemplo, `plugins/mi-plugin`).
2. Dentro de esa carpeta, crea el archivo `index.ts` con la implementación de tu plugin.
3. Añade un `package.json` si tu plugin tiene dependencias específicas.

### Paso 3: Registrar el plugin

1. Edita el archivo de configuración `config/plugins.json`.
2. Añade una entrada para tu plugin:
   ```json
   {
     "name": "mi-plugin",
     "path": "./plugins/mi-plugin",
     "enabled": true,
     "config": {}
   }
   ```

### Paso 4: Probar el plugin

1. Inicia Hermes Agent en modo desarrollo: `npm run dev`
2. Observa los logs para ver si tu plugin se inicializa correctamente.
3. Prueba las funcionalidades de tu plugin (por ejemplo, desencadenando eventos que debería manejar).

### Paso 5: Empaquetar y distribuir

1. Si planeas compartir tu plugin, crea un paquete npm público.
2. Asegúrate de incluir los archivos necesarios y documentar la configuración requerida.

## Consejos adicionales

- Mantén tus plugins lo más pequeños y enfocados posible.
- Maneja adecuadamente los errores para no afectar la estabilidad del agente.
- Utiliza el sistema de logging para depuración y monitoreo.
- Respeta las convenciones de código del proyecto (consulta `CONTRIBUTING.md` para más detalles).
- Escribe pruebas unitarias para tu plugin siempre que sea posible.

## Conclusión

Con esta guía, tienes todo lo necesario para comenzar a extender Hermes Agent. Recuerda que la comunidad valora las contribuciones de calidad, así que no dudes en compartir tus plugins en el repositorio oficial o en el registro de plugins de Hermes Agent.

¿Tienes preguntas? Únete a nuestro canal de Discord o abre un issue en GitHub para obtener ayuda.