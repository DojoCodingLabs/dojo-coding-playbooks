# Master Multi-Agent Development with Claude Code, Cursor and Git Worktrees 🚀

## 📌 Objetivos de Aprendizaje

Al finalizar este tutorial serás capaz de:

- **Configurar un entorno multi-agente** utilizando Git Worktrees para aislar el trabajo de diferentes agentes de IA
- **Automatizar la creación de worktrees** con Codename Goose y prompts optimizados
- **Gestionar múltiples instancias de Claude Code y Cursor** trabajando simultáneamente en diferentes features
- **Integrar el trabajo de varios agentes** resolviendo conflictos y manteniendo la coherencia del código
- **Optimizar tu flujo de desarrollo** superando las limitaciones de un solo agente trabajando secuencialmente

## 💡 Introducción

¿Te imaginas tener un equipo de desarrolladores de IA trabajando para ti simultáneamente en el mismo proyecto? En este tutorial, aprenderás a crear y gestionar un ejército de agentes de codificación utilizando Claude Code, Git Worktrees y Codename Goose. La idea es superar la limitación de que Claude Code solo puede manejar una solicitud a la vez, permitiéndote realizar múltiples tareas y acelerar tu flujo de desarrollo de manera significativa.

---

## Herramientas que Necesitarás 🛠️

Para seguir este tutorial, necesitarás familiarizarte con las siguientes herramientas:

- **Claude Code**: Un potente agente de desarrollo en línea de comandos (CLI) creado por Anthropic. Es excelente para generar, modificar y entender código.
- **Codename Goose**: Una herramienta de código abierto de Block que actúa como un agente local en tu máquina, capaz de manipular archivos, carpetas y ejecutar comandos. La usaremos para automatizar la configuración de nuestro entorno.
- **Cursor**: Un editor de código "IA-first" que usaremos para visualizar y gestionar las diferentes carpetas y ramas de nuestro proyecto. Funciona muy bien con la terminal integrada.

> **Nota importante**: Este tutorial está basado en las versiones actuales de las herramientas (Julio 2025). Claude Code está en preview y algunas configuraciones pueden cambiar con actualizaciones futuras.

## El Problema: Multitasking con Agentes de IA

Cuando trabajas con un agente de IA como Claude Code, este toma una tarea, la procesa y te da un resultado. Durante ese tiempo de "pensamiento" y ejecución, no puedes asignarle otra tarea. Esto crea un cuello de botella. La solución obvia sería abrir varias instancias del agente, pero esto presenta un nuevo problema: si todos los agentes trabajan en la misma rama (main, por ejemplo), pueden sobrescribir el trabajo de los demás, generando conflictos y caos en el código.

## La Solución: Git Worktrees para Aislamiento

Aquí es donde entra en juego `git worktree`. Esta funcionalidad de Git nos permite tener múltiples ramas de un mismo repositorio "desplegadas" en diferentes carpetas al mismo tiempo. Cada carpeta (o worktree) se comporta como un repositorio independiente con su propia rama, pero todos están conectados al mismo repositorio `.git` subyacente.

Esto significa que podemos asignar a cada agente de IA su propia carpeta y su propia rama para trabajar. Una vez que cada agente ha completado su tarea, podemos fusionar (merge) su trabajo de vuelta a la rama principal, tal como lo haríamos en un equipo de desarrollo humano.

## Configuración Manual: Paso a Paso con Git Worktrees

Antes de automatizar el proceso, es importante entender cómo funciona la configuración manual. Esto te dará una base sólida para después apreciar la automatización.

### Configuración Inicial del Proyecto

1. **Estructura Base del Proyecto**
   ```
   mi-proyecto/
   ├── app/              # Repositorio principal 
   │   ├── src/
   │   ├── .git/
   │   ├── package.json
   │   └── README.md
   └── worktrees/        # Directorio para agentes (lo crearemos)
       ├── feature-auth/
       ├── feature-dashboard/
       └── feature-api/
   ```

2. **Crear el Directorio de Worktrees**
   ```bash
   # Desde el directorio raíz del proyecto
   mkdir worktrees
   cd app/  # Navegar al repositorio principal
   ```

### Creación Manual de Agentes con Worktrees

3. **Crear el Primer Agente (Ejemplo: Authentication)**
   ```bash
   # Crear worktree con rama nueva
   git worktree add ../worktrees/feature-auth -b feature/auth
   
   # Verificar que se creó correctamente
   git worktree list
   ```

4. **Configurar el Entorno del Agente**
   ```bash
   # Navegar al nuevo worktree
   cd ../worktrees/feature-auth/
   
   # Copiar configuraciones base
   cp -r ../../app/.claude ./
   cp -r ../../app/.vscode ./
   cp ../../app/package.json ./
   cp ../../app/.gitignore ./
   ```

5. **Crear Configuración Única de VS Code/Cursor**
   
   Crear `.vscode/settings.json`:
   ```json
   {
     "workbench.colorTheme": "Monokai",
     "workbench.colorCustomizations": {
       "titleBar.activeBackground": "#FF6B35",
       "titleBar.activeForeground": "#FFFFFF",
       "activityBar.background": "#FF6B35",
       "statusBar.background": "#FF6B35"
     },
     "window.title": "${rootName} - Agente AUTH"
   }
   ```

6. **Crear README.md Específico del Agente**
   ```markdown
   # Agente de Autenticación 🔐
   
   ## Responsabilidades
   - Implementar sistema de login/logout
   - Gestión de sesiones de usuario
   - Integración con JWT tokens
   - Middleware de autenticación
   
   ## Contexto del Proyecto
   Este worktree está dedicado exclusivamente al desarrollo
   del sistema de autenticación. Trabajas en la rama 
   feature/auth y debes enfocarte solo en estas funcionalidades.
   ```

7. **Hacer el Commit Inicial**
   ```bash
   git add .
   git commit -m "feat: inicializar worktree de autenticación"
   ```

### Repetir el Proceso para Más Agentes

8. **Crear Segundo Agente (Dashboard)**
   ```bash
   cd ../../app/  # Volver al repo principal
   git worktree add ../worktrees/feature-dashboard -b feature/dashboard
   
   cd ../worktrees/feature-dashboard/
   cp -r ../../app/.claude ./
   cp -r ../../app/.vscode ./
   cp ../../app/package.json ./
   cp ../../app/.gitignore ./
   ```

9. **Configurar Colores Únicos para Dashboard**
   
   `.vscode/settings.json`:
   ```json
   {
     "workbench.colorTheme": "Dark+ (default dark)",
     "workbench.colorCustomizations": {
       "titleBar.activeBackground": "#00D4AA",
       "titleBar.activeForeground": "#FFFFFF",
       "activityBar.background": "#00D4AA",
       "statusBar.background": "#00D4AA"
     },
     "window.title": "${rootName} - Agente DASHBOARD"
   }
   ```

### Abrir y Gestionar Múltiples Instancias

10. **Abrir Cada Agente en Cursor/VS Code**
    ```bash
    # Abrir cada worktree en una ventana separada
    cursor ../worktrees/feature-auth
    cursor ../worktrees/feature-dashboard
    cursor ../worktrees/feature-api
    ```

11. **Inicializar Claude Code en Cada Instancia**
    ```bash
    # En cada terminal de cada ventana
    claude
    ```

12. **Verificar la Configuración**
    - Cada ventana debe tener un color diferente en la barra de título
    - Cada agente debe estar en su rama correspondiente
    - Los archivos de configuración deben estar presentes

### Flujo de Trabajo Manual

13. **Asignar Tareas Específicas**
    ```bash
    # En la ventana del agente AUTH
    > Lee tu README.md y comienza a implementar el sistema de autenticación con JWT
    
    # En la ventana del agente DASHBOARD  
    > Lee tu README.md y crea un dashboard con componentes reutilizables
    ```

14. **Integrar Cambios Manualmente**
    ```bash
    # Una vez que un agente complete su trabajo
    cd worktrees/feature-auth/
    git add .
    git commit -m "feat: implementar autenticación JWT completa"
    
    # Volver al repo principal y fusionar
    cd ../../app/
    git merge feature/auth
    ```

### Ventajas del Proceso Manual

- **Control Total**: Entiendes cada paso del proceso
- **Personalización**: Puedes ajustar cada configuración específicamente
- **Debugging**: Más fácil identificar problemas en la configuración
- **Aprendizaje**: Comprendes profundamente cómo funcionan los worktrees

## Automatizando la Creación de Agentes con Goose y un Prompt Inteligente

Ahora que entiendes el proceso manual, automatizaremos usando Codename Goose para que puedas crear múltiples agentes en segundos.

### El Prompt para la Creación de Worktrees

Este prompt le indica a un agente (en nuestro caso, Goose ejecutando Claude Code) que configure todo el entorno por nosotros.

```
🎯 PROMPT PARA CLAUDE CODE:
Crea nuevos worktrees de características con estructura organizada y configuraciones únicas de VS Code. Ejecuta todo el proceso de configuración inmediatamente.

**PRIMERO: Pregunta al usuario qué ramas de características deben crearse.**

Pregunta: "¿Qué ramas de características te gustaría crear? Por favor proporciona los nombres de las características (sin el prefijo 'feature-'). Por ejemplo: auth, dashboard, payments, notifications"

Espera la respuesta del usuario con la lista de características, luego procede con la ejecución.

**Estructura de Proyecto Esperada:**
[nombre-proyecto]/
├── app/ # Aplicación principal (repositorio git)
│   ├── src/
│   ├── .claude/
│   └── ... (archivos de aplicación)
└── worktrees/ # Directorio de worktrees de características
    ├── feature-auth/ # Worktrees de características a crear
    ├── feature-dashboard/
    ├── feature-payments/
    └── feature-[especificado-por-usuario]/

**PARA CADA CARACTERÍSTICA ESPECIFICADA POR EL USUARIO, EJECUTA ESTOS PASOS:**

1. **Validar Entorno Actual**
   - Verificar que estamos en el directorio correcto del proyecto
   - Comprobar si existe repositorio git en el directorio app/
   - Asegurar que la estructura del directorio worktrees/ esté lista

2. **Crear Git Worktrees**
   ```bash
   cd app/
   git worktree add ../worktrees/feature-[NOMBRE_CARACTERISTICA] -b feature/[NOMBRE_CARACTERISTICA]
   ```

3. **Copiar Configuración Base**
   ```bash
   cd ../worktrees/feature-[NOMBRE_CARACTERISTICA]/
   cp -r ../../app/.claude ./
   cp -r ../../app/.vscode ./ # Copiar configuraciones de VS Code/Cursor
   cp ../../app/.gitignore ./
   cp ../../app/package.json ./
   ```

4. **Crear Configuración Única de VS Code** (con esquemas de colores distintos para no confundirnos)
   Crear `.vscode/settings.json` con configuraciones específicas de la característica:
   ```json
   {
     "workbench.colorTheme": "[TEMA_UNICO_PARA_CARACTERISTICA]",
     "workbench.colorCustomizations": {
       "titleBar.activeBackground": "[COLOR_UNICO]",
       "titleBar.activeForeground": "#FFFFFF",
       "activityBar.background": "[COLOR_UNICO]",
       "statusBar.background": "[COLOR_UNICO]"
     },
     "window.title": "${rootName} - Agente [NOMBRE_CARACTERISTICA]"
   }
   ```

5. **Crear Documentación de Característica** (un `README.md` para cada agente con sus responsabilidades)
   Crear README.md detallado para cada característica con responsabilidades específicas

6. **Inicializar Ramas de Características** (haciendo el primer commit)
   ```bash
   git add .
   git commit -m "feat: inicializar worktree de característica [NOMBRE_CARACTERISTICA]"
   ```

7. **Verificar Cada Configuración**
   - Confirmar que el worktree está correctamente vinculado al repositorio principal
   - Probar que las operaciones de git funcionen correctamente
   - Verificar que las configuraciones únicas estén aplicadas
```

Puedes encontrar el prompt completo aquí: [Prompt de Ejecución para Creación de Worktrees](https://gist.github.com/brolag/76e77785cd5233dbe91bad1d75457550)

### Pasos para la Automatización:

1. **Abre Codename Goose**: Navega al directorio padre que contiene tu aplicación (la carpeta `app`).

2. **Pega el Prompt**: Copia el prompt anterior y pégalo en la interfaz de chat de Goose.

3. **Define tus Agentes**: Goose te preguntará qué ramas de características (features) quieres crear. Introduce los nombres separados por comas. Por ejemplo: `landing-page, tutorial-system, code-generator, terminal-simulator, dashboard`.

4. **Ejecución**: Goose comenzará a crear una carpeta `worktrees` y dentro de ella, una carpeta para cada agente con su propia rama de Git, un README.md y una configuración de color única para VS Code o Cursor.

5. **Crea una "Receta"**: Una de las mejores funcionalidades de Goose es que puedes guardar esta sesión de chat como una "receta". Esto te permite reutilizar el proceso completo en el futuro con un solo clic, sin tener que copiar y pegar el prompt de nuevo.

## Ejecutando tu Ejército de Agentes 🎖️

Una vez que Goose ha terminado, te pedirá abrir cada worktree en una instancia separada de Cursor.

### Configuración de las Instancias:

1. **Abre las Instancias**: Acepta la sugerencia de Goose para abrir cada carpeta. Verás que cada ventana de Cursor tiene un color diferente en la barra de título, lo que facilita la identificación de cada agente.

2. **Inicializa Claude Code**: En la terminal de cada instancia de Cursor, inicializa Claude Code con el comando `claude`.

3. **Asigna las Tareas**: Ahora puedes empezar a dar instrucciones a cada agente de forma independiente. Un buen punto de partida es pedirle que lea su propio archivo README.md para que entienda su contexto y responsabilidades.

```bash
# En la ventana del agente 'landing-page'
claude
> Por favor comienza a trabajar en la tarea de la página de inicio. Asegúrate de leer el archivo readme.
```

Mientras un agente está "pensando", puedes saltar a otra ventana y dar instrucciones a otro agente, optimizando tu tiempo al máximo.

## Gestionando el Flujo de Trabajo y Resolviendo Conflictos

A medida que cada agente completa su trabajo, debes integrar sus cambios en la rama principal (`main`).

### Realiza Commits:

En cada worktree, haz commit de los cambios generados por el agente.

```bash
# Dentro de worktrees/feature-dashboard
git add .
git commit -m "feat: add initial dashboard structure"
```

### Fusiona las Ramas:

Vuelve a la carpeta principal de tu aplicación (`app`) y fusiona la rama de la característica.

```bash
# Dentro de la carpeta /app
git merge feature/dashboard
```

### Resolución de Conflictos:

¡Inevitablemente, surgirán conflictos! Especialmente si varios agentes modifican el mismo archivo (como `package.json`). Cuando esto ocurra, puedes pedirle ayuda al propio Claude Code para resolverlos.

```bash
# Después de un intento de merge fallido
claude
> Ayúdame a resolver estos conflictos de merge. Al fusionar la característica 'terminal-simulator', 
> crea una nueva página para ella y agrega enlaces de navegación para todas las nuevas características.
```

Claude es sorprendentemente bueno para entender el contexto de los conflictos y proponer soluciones lógicas, como crear nuevos componentes o páginas para evitar colisiones.

## Resultados y Próximos Pasos

Al final de este proceso, tendrás una rama `main` que integra el trabajo de todos tus agentes. Podrás ver las diferentes páginas y funcionalidades que cada uno creó, todo fusionado en un solo proyecto coherente.

**Consejo clave**: La calidad del resultado depende enormemente de la calidad de las instrucciones. Asegúrate de que cada README.md sea muy específico sobre las responsabilidades y el alcance de cada agente.

⛩️ ## Reflexión Final

El desarrollo multi-agente con Git Worktrees representa un cambio paradigmático en cómo abordamos proyectos de software complejos. Ya no estamos limitados por el procesamiento secuencial de un solo agente; en su lugar, podemos orquestar un verdadero equipo de desarrolladores de IA, cada uno especializado en una parte específica del proyecto.

Esta metodología no solo acelera significativamente el desarrollo, sino que también mejora la calidad del código al permitir que cada agente se enfoque profundamente en su dominio específico. La combinación de la potencia de Claude Code con el aislamiento perfecto de Git Worktrees crea un entorno donde la productividad puede multiplicarse exponencialmente.

Los beneficios van más allá de la velocidad: obtienes mejor arquitectura, código más mantenible, testing más comprehensivo, y documentación más detallada. Cada agente, al trabajar en su área especializada, puede dedicar toda su capacidad de procesamiento a perfeccionar esa funcionalidad específica.

## 📚 Recursos Extra

### Prompts y Scripts Esenciales
- **[Prompt de Ejecución para Creación de Worktrees](https://gist.github.com/brolag/76e77785cd5233dbe91bad1d75457550)** - Prompt principal para automatización
- **[Scripts de Coordinación Multi-agente](https://gist.github.com/brolag/301311f0ff4e41d8cd1ef76075a4423e)** - Scripts de gestión y monitoreo
- **[Estrategias de Resolución de Conflictos](https://gist.github.com/brolag/647cf9870bb433b5f31db1aeccab9273)** - Técnicas para resolver conflictos
- **[Plantillas de Flujo de Trabajo Avanzadas](https://gist.github.com/brolag/55d232650fcec6f35493398f1ab6adc4)** - Plantillas para proyectos complejos

### Gestores de Multi-agentes
- **[Magnet](https://www.magnet.run/)** - Plataforma para orquestar equipos de agentes de IA
- **[Granja de Agentes Claude Code](https://github.com/Dicklesworthstone/claude_code_agent_farm)** - Sistema distribuido para gestionar múltiples agentes Claude
- **[Conductor](https://conductor.build/)** - Framework para construir aplicaciones multi-agente
- **[Claude Squad](https://github.com/smtg-ai/claude-squad)** - Herramientas para coordinar equipos de Claude

### Otras Herramientas de IA para Desarrollo
- **[OpenCode AI](https://opencode.ai/)** - Plataforma de desarrollo asistido por IA
- **[Gemini CLI](https://github.com/google-gemini/gemini-cli)** - Interfaz de línea de comandos para Google Gemini

### Certificación y Formación
- **[Claude Code en Acción - Certificación](https://anthropic.skilljar.com/claude-code-in-action)** - Curso oficial de certificación en Claude Code de Anthropic

### Documentación Oficial
- **[Codename Goose](https://block.xyz/goose)** - Página oficial de Codename Goose
- **[Claude Code](https://claude.ai/code)** - Página oficial de Claude Code
- **[Documentación de Cursor](https://docs.cursor.sh/)** - Guía completa de Cursor
- **[Documentación sobre Worktrees en Claude Code](https://docs.anthropic.com/en/docs/build-with-claude/claude-code/guides/worktrees)** - Ejecutar sesiones paralelas de Claude Code con Git worktrees

### Lecturas Recomendadas
- **[El Ingeniero de Software Nativo de IA](https://addyo.substack.com/p/the-ai-native-software-engineer)** - Cómo evoluciona el rol del desarrollador con IA
- **[El Patrón "Confía pero Verifica" para Ingeniería de IA](https://addyo.substack.com/p/the-trust-but-verify-pattern-for)** - Estrategias para trabajar efectivamente con IA
- **[El Manual de Ingeniería de Prompts para Desarrolladores](https://addyo.substack.com/p/the-prompt-engineering-playbook-for)** - Guía completa para ingeniería de prompts
- **[La IA No Matará a los Desarrolladores Junior, Pero Tu Mentalidad Sí Podría](https://addyo.substack.com/p/ai-wont-kill-junior-devs-but-your)** - Perspectivas sobre el futuro del desarrollo con IA

### Recursos Complementarios
- **[Documentación de Git Worktrees](https://git-scm.com/docs/git-worktree)** - Documentación oficial de Git Worktrees
- **[Mejores Prácticas de Desarrollo Multi-agente](https://github.com/topics/multi-agent)** - Repositorios y ejemplos
- **[Commits Semánticos](https://www.conventionalcommits.org/)** - Estándar para mensajes de commit

¡Espero que este tutorial te sea de gran utilidad! Prueba esta metodología, experimenta con diferentes agentes y comparte tus resultados. ¡Feliz codificación con tu nuevo ejército de IA! 🚀