# Guía de Instalación de Hermes Agent

Esta guía le ayudará a instalar y configurar Hermes Agent en su sistema. Siga los pasos detallados a continuación para una instalación exitosa.

## Tabla de Contenidos

1. [Requisitos Previos](#requisitos-previos)
2. [Instalación](#instalación)
3. [Configuración Inicial](#configuración-inicial)
4. [Primeros Pasos](#primeros-pasos)
5. [Solución de Problemas](#solución-de-problemas)

---

## Requisitos Previos

Antes de instalar Hermes Agent, asegúrese de que su sistema cumpla con los siguientes requisitos:

### Sistema Operativo

- **Linux**: Ubuntu 20.04+, Debian 11+, Fedora 34+, o distribuciones equivalentes
- **macOS**: macOS 11 (Big Sur) o superior
- **Windows**: Windows 10 o superior (con WSL2 recomendado)

### Dependencias del Sistema

- **Git**: versión 2.30 o superior
  ```bash
  git --version
  ```

- **Node.js**: versión 18.x o superior
  ```bash
  node --version
  ```

- **npm**: versión 9.0 o superior (o yarn/pnpm equivalentes)
  ```bash
  npm --version
  ```

- **Python**: versión 3.9 o superior (para ciertos scripts de utilidad)
  ```bash
  python3 --version
  ```

### Hardware

- **CPU**: 2 núcleos o más
- **RAM**: 4 GB mínimo, 8 GB recomendados
- **Disco**: 500 MB de espacio libre para la instalación

### Permisos

- Acceso administrativo o privilegios de sudo para instalación de dependencias globales
- Permisos de lectura/escritura en el directorio de instalación

---

## Instalación

### Opción 1: Instalación desde Fuente (Recomendado)

1. **Clone el repositorio**:

   ```bash
   git clone https://github.com/tu-organizacion/hermes-agent.git
   cd hermes-agent
   ```

2. **Instale las dependencias**:

   ```bash
   npm install
   ```

   *Nota*: Si usa yarn o pnpm, use:
   ```bash
   # Para yarn
   yarn install
   
   # Para pnpm
   pnpm install
   ```

3. **Compile el proyecto**:

   ```bash
   npm run build
   ```

4. **Verifique la instalación**:

   ```bash
   npm run --help
   ```

   Debe ver la lista de comandos disponibles de Hermes Agent.

### Opción 2: Instalación Global

Para instalar Hermes Agent globalmente en su sistema:

```bash
npm install -g hermes-agent
```

Después de la instalación, verifique que esté disponible:

```bash
hermes-agent --version
```

### Opción 3: Instalación con Docker

Si prefiere usar Docker:

1. **Construya la imagen Docker**:

   ```bash
   docker build -t hermes-agent:latest .
   ```

2. **Ejecute el contenedor**:

   ```bash
   docker run -it --rm \
     -v $(pwd)/config:/app/config \
     -v $(pwd)/data:/app/data \
     hermes-agent:latest
   ```

3. **Use Docker Compose** (para entornos de producción):

   ```bash
   docker-compose up -d
   ```

---

## Configuración Inicial

### Creación del Directorio de Configuración

Hermes Agent utiliza un directorio específico para sus archivos de configuración. Cree la siguiente estructura de directorios:

```bash
# En Linux/macOS
mkdir -p ~/.hermes-agent/config
mkdir -p ~/.hermes-agent/data
mkdir -p ~/.hermes-agent/logs

# En Windows
mkdir %USERPROFILE%\.hermes-agent\config
mkdir %USERPROFILE%\.hermes-agent\data
mkdir %USERPROFILE%\.hermes-agent\logs
```

### Archivo de Configuración Básico

Cree el archivo de configuración inicial:

**En Linux/macOS**:
```bash
touch ~/.hermes-agent/config/config.json
```

**En Windows**:
```cmd
type nul > %USERPROFILE%\.hermes-agent\config\config.json
```

### Contenido del Archivo de Configuración

Edite el archivo `config.json` con el siguiente contenido básico:

```json
{
  "agentName": "MiHermesAgent",
  "server": {
    "host": "localhost",
    "port": 3000,
    "protocol": "http"
  },
  "logging": {
    "level": "info",
    "file": "~/.hermes-agent/logs/hermes.log",
    "maxFiles": 7,
    "maxSize": "10m"
  },
  "data": {
    "path": "~/.hermes-agent/data"
  },
  "plugins": {
    "enabled": [],
    "path": "~/.hermes-agent/plugins"
  },
  "security": {
    "apiKey": "",
    "enableAuth": false
  }
}
```

### Variables de Entorno (Opcional)

Puede configurar Hermes Agent usando variables de entorno para mayor seguridad o flexibilidad:

```bash
# Configuración del servidor
export HERMES_SERVER_HOST="localhost"
export HERMES_SERVER_PORT="3000"

# Configuración de logging
export HERMES_LOG_LEVEL="debug"

# Configuración de seguridad
export HERMES_API_KEY="tu-clave-secreta-aqui"
export HERMES_ENABLE_AUTH="true"

# Rutas personalizadas
export HERMES_CONFIG_PATH="/ruta/personalizada/config"
export HERMES_DATA_PATH="/ruta/personalizada/data"
```

Cree un archivo `.env` en el directorio de configuración:

```bash
cat > ~/.hermes-agent/config/.env << EOF
HERMES_SERVER_HOST=localhost
HERMES_SERVER_PORT=3000
HERMES_LOG_LEVEL=info
HERMES_API_KEY=generar-clave-segura-aqui
HERMES_ENABLE_AUTH=true
EOF
```

---

## Primeros Pasos

### 1. Iniciar Hermes Agent

**En modo desarrollo**:
```bash
npm run dev
```

**En modo producción**:
```bash
npm start
```

**Si instaló globalmente**:
```bash
hermes-agent start
```

Deberá ver un mensaje similar a:
```
[INFO] Hermes Agent iniciado en localhost:3000
[INFO] Agent 'MiHermesAgent' listo y esperando conexiones
```

### 2. Verificar el Estado

Compruebe que el agente está funcionando correctamente:

```bash
# Usando curl
curl http://localhost:3000/api/status

# Usando el comando CLI (si está disponible)
hermes-agent status
```

Respuesta esperada:
```json
{
  "status": "running",
  "agentName": "MiHermesAgent",
  "uptime": 1234,
  "version": "1.0.0"
}
```

### 3. Ejemplo de Uso Básico

#### Crear una Tarea Simple

```bash
curl -X POST http://localhost:3000/api/tasks \
  -H "Content-Type: application/json" \
  -d '{
    "task": "procesar-datos",
    "payload": {
      "source": "archivo.csv",
      "destination": "resultados/"
    }
  }'
```

#### Consultar Tareas Actuales

```bash
curl http://localhost:3000/api/tasks
```

#### Obtener Detalles de una Tarea

```bash
curl http://localhost:3000/api/tasks/{task-id}
```

### 4. Monitorizar Logs

Vea los logs en tiempo real:

```bash
tail -f ~/.hermes-agent/logs/hermes.log
```

O si ejecuta Hermes Agent en modo desarrollo, verá los logs directamente en la consola.

### 5. Detener Hermes Agent

**Si ejecuta en modo desarrollo**: Presione `Ctrl+C`

**Si ejecuta como servicio**:
```bash
hermes-agent stop
```

**O usando systemd**:
```bash
sudo systemctl stop hermes-agent
```

---

## Solución de Problemas

### Problema: Puerto 3000 ya está en uso

**Síntoma**: Error al iniciar: "EADDRINUSE: address already in use :::3000"

**Solución**:

1. Encuentre el proceso usando el puerto:
   ```bash
   lsof -i :3000
   ```

2. Mátelo (reemplace PID con el ID del proceso):
   ```bash
   kill -9 PID
   ```

3. O cambie el puerto en su configuración:
   ```json
   {
     "server": {
       "host": "localhost",
       "port": 3001
     }
   }
   ```

### Problema: Error de Permisos

**Síntoma**: "EACCES: permission denied"

**Solución**:

1. Verifique los permisos del directorio:
   ```bash
   ls -la ~/.hermes-agent/
   ```

2. Corrija los permisos:
   ```bash
   chmod -R 755 ~/.hermes-agent/
   ```

3. Si persiste, asegúrese de no necesitar sudo:
   ```bash
   # NO use sudo para npm run dev
   # Use sudo solo para instalaciones globales
   sudo npm install -g hermes-agent
   ```

### Problema: Dependencias No Instaladas

**Síntoma**: "Module not found" o "Cannot find module"

**Solución**:

1. Elimine node_modules y reinstale:
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   ```

2. Limpie la caché de npm:
   ```bash
   npm cache clean --force
   ```

3. Vuelva a instalar:
   ```bash
   npm install
   ```

### Problema: Hermes Agent no Inicia

**Síntoma**: Sin respuesta o error al iniciar

**Solución**:

1. Verifique los logs para detalles del error:
   ```bash
   cat ~/.hermes-agent/logs/hermes.log
   ```

2. Valide su configuración:
   ```bash
   npm run validate-config
   ```

3. Pruebe en modo debug:
   ```bash
   DEBUG=hermes:* npm run dev
   ```

4. Revise la sintaxis de config.json:
   ```bash
   cat ~/.hermes-agent/config/config.json | jq .
   ```
   *(Instale jq si no lo tiene: `sudo apt-get install jq`)*

### Problema: Error de Conexión

**Síntoma**: "ECONNREFUSED" o timeout al conectar

**Solución**:

1. Verifique que el agente esté corriendo:
   ```bash
   curl http://localhost:3000/api/status
   ```

2. Revise el firewall:
   ```bash
   # En Linux (ufw)
   sudo ufw status
   sudo ufw allow 3000/tcp
   
   # En macOS
   sudo pfctl -s rules
   ```

3. Verifique la configuración del host:
   ```bash
   ping localhost
   ```

### Problema: Logs Demasiado Grandes

**Síntoma**: Espacio en disco lleno o logs muy lentos

**Solución**:

1. Configure rotación de logs en config.json:
   ```json
   {
     "logging": {
       "level": "warn",
       "maxFiles": 3,
       "maxSize": "5m"
     }
   }
   ```

2. Limpie logs antiguos manualmente:
   ```bash
   find ~/.hermes-agent/logs/ -name "*.log.*" -mtime +7 -delete
   ```

### Problema: Plugins No Cargan

**Síntoma**: Los plugins configurados no funcionan

**Solución**:

1. Verifique la lista de plugins disponibles:
   ```bash
   npm list --depth=0
   ```

2. Revise la ruta de plugins en config.json:
   ```json
   {
     "plugins": {
       "path": "/ruta/absoluta/a/plugins"
     }
   }
   ```

3. Valide la sintaxis del plugin:
   ```bash
   node -c ~/.hermes-agent/plugins/mi-plugin/index.js
   ```

### Recursos Adicionales

Si el problema persiste:

1. Revise la documentación completa en: [Documentación Oficial](https://github.com/tu-organizacion/hermes-agent/wiki)
2. Busque issues similares en: [GitHub Issues](https://github.com/tu-organizacion/hermes-agent/issues)
3. Consulte la API en: [Documentación de API](../api/)
4. Vea ejemplos en: [Ejemplos de Uso](../ejemplos/)

### Obtener Ayuda

Para obtener más ayuda o reportar un problema:

- **Documentación de desarrollador**: [Guía para Desarrolladores](../guia-desarrollador/)
- **Wiki del proyecto**: [Hermes Agent Wiki](https://github.com/tu-organizacion/hermes-agent/wiki)
- **Foro de soporte**: [Community Forum](https://community.hermes-agent.org)
- **Discord**: [Join our Discord](https://discord.gg/hermes-agent)

---

## Siguientes Pasos

¡Felicidades! Ha instalado y configurado Hermes Agent exitosamente. Ahora puede:

- Leer la [Guía de Configuración](configuracion.md) para opciones avanzadas
- Explorar [Ejemplos de Uso](../ejemplos/) para casos prácticos
- Revisar la [Documentación de API](../api/) para integración con sus aplicaciones
- Consultar la [Guía para Desarrolladores](../guia-desarrollador/) para extender funcionalidades

---

**Versión de la guía**: 1.0.0  
**Última actualización**: Mayo 2026  
**Mantenido por**: Equipo de Documentación Hermes Agent