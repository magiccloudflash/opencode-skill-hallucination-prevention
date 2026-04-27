# Habilidad de OpenCode: Prevención de alucinaciones de IA

> Una habilidad de agente de OpenCode que evita las alucinaciones de IA mediante la verificación forzada, el reconocimiento de incertidumbre y respuestas basadas en evidencia.

[English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | Español

## Características

- **Verifica antes de afirmar** — lee archivos, ejecuta código, consulta documentación antes de concluir
- **Reconoce la incertidumbre** — distingue claramente entre especulación y hechos verificados
- **No inventes** — nunca fabriques errores, APIs, resultados de pruebas o correcciones
- **Fundaméntate en evidencia** — cita rutas de archivo, números de línea y resultados reales
- **Autoverificación** — lista de verificación de 10 puntos antes de cada respuesta
- **Guardas de generación de código** — doble verificación antes y después de escribir código
- **Reglas por herramienta** — reglas anti-alucinación específicas para read, write, edit, bash, grep, glob, etc.
- **8 tipos de alucinaciones** — cada tipo tiene su propia estrategia de prevención

## Instalación rápida

### Método 1: Clonar el repositorio (recomendado)

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

Copia la habilidad a tu configuración global de OpenCode:

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### Método 2: Instalación manual

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### Método 3: Como regla global (carga automática en cada sesión — recomendado)

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> El archivo global `AGENTS.md` se carga automáticamente en cada sesión, sin necesidad de invocar la habilidad manualmente.

## Uso

### Bajo demanda (Método 1, 2)

OpenCode descubre automáticamente la habilidad. El agente la cargará cuando sea necesario:

```javascript
skill({ name: "hallucination-prevention" })
```

### Carga automática (Método 3)

Las reglas de `AGENTS.md` se inyectan automáticamente en el contexto de cada sesión. **No se requiere ninguna acción manual.**

## Configuración de permisos

En `opencode.json`:

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

Para agentes específicos:

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    }
  }
}
```

Los permisos admiten patrones comodín:

| Permiso | Comportamiento |
|----------|----------------|
| `allow` | La habilidad se carga inmediatamente |
| `deny` | La habilidad se oculta del agente, acceso denegado |
| `ask` | Solicita confirmación antes de cargar |

## Estructura del repositorio

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Documentación en inglés
├── README_CN.md                              # Documentación en chino
├── README_RU.md                              # Documentación en ruso
├── README_FR.md                              # Documentación en francés
├── README_DE.md                              # Documentación en alemán
├── README_ES.md                              # Documentación en español (este archivo)
├── LICENSE                                   # Licencia MIT
├── opencode.json                             # Ejemplo de configuración
├── AGENTS.md                                 # Reglas globales (auto-carga)
└── hallucination-prevention/
    └── SKILL.md                              # Archivo de definición de la habilidad
```

## Contenido de la habilidad v2.0

### Clasificación de los 8 tipos de alucinaciones

| Tipo | Descripción | Ejemplo | Prevención |
|------|-------------|---------|------------|
| **A: Fáctica** | Afirmar hechos no verificados | "Esta función devuelve una Promise" (sin leer el código) | Lee fuentes, ejecuta código |
| **B: Estructural** | Inventar archivos/rutas | "El manejador está en src/routes/auth.ts" (el archivo no existe) | Usa glob/ls para verificar |
| **C: Comportamental** | Suponer comportamiento del código | "El error está corregido" (sin ejecutar el proyecto) | Ejecuta el código y muestra el resultado |
| **D: Externa** | Inventar APIs/funciones | "Lodash tiene una función groupByAndSort()" (no existe) | Verifica documentación o fuentes |
| **E: Resultado** | Imaginar resultados de pruebas | "Todas las pruebas pasan" (sin ejecutarlas) | Ejecuta las pruebas, muestra el resultado real |
| **F: Temporal** | Versiones/fechas incorrectas | "Node v24 añadió esta funcionalidad" (v24 no existe) | Verifica versión y changelog |
| **G: Autoridad** | Fingir haber verificado | "He confirmado que el cambio es correcto" (sin releer) | Realiza la verificación y muestra evidencia |
| **H: Referencia** | Inventar URLs/IDs/commits | "Ver https://github.com/foo/issues/99999" (enlace falso) | Verifica la URL o indica "no verificado" |

### Reglas por herramienta

| Herramienta | Regla principal |
|-------------|-----------------|
| **read** | No adivines el contenido; verifica existencia del archivo; relee después de editar |
| **write** | Relee inmediatamente después de escribir; no crees documentación sin que te lo pidan |
| **edit** | Lee siempre antes de editar; relee después; respeta la indentación |
| **bash** | No inventes salidas de comandos; re-ejecuta para verificar correcciones; pregunta antes de acciones destructivas |
| **grep** | Confirma existencia de funciones/clases mediante grep |
| **glob** | Usa glob para verificar estructura antes de referenciar rutas |
| **question** | Ante instrucciones ambiguas, pregunta primero, actúa después |

### Lista de verificación de 10 puntos

Antes de cada respuesta, verifica:

| # | Pregunta | Acción si "No" |
|---|----------|-----------------|
| 1 | ¿Realmente he LEÍDO los archivos de los que hablo? | Léelos ahora |
| 2 | ¿Realmente he EJECUTADO los comandos cuya salida reporto? | Ejecútalos ahora |
| 3 | ¿Realmente he VERIFICADO el paquete/API que estoy referenciando? | Verifica o marca como incierto |
| 4 | ¿Estoy seguro de que este mensaje de error es real? | Muestra la salida real |
| 5 | ¿He vuelto a leer los archivos después de editarlos? | Vuelve a leerlos ahora |
| 6 | ¿Estoy adivinando la estructura de directorios? | Lista el directorio |
| 7 | ¿Estoy adivinando una firma de función? | Lee el código fuente |
| 8 | ¿He afirmado que una corrección funciona sin probarla? | Ejecuta el código |
| 9 | ¿Estoy inventando opciones de comandos? | Verifica --help |
| 10 | ¿Podría mi respuesta contener datos inventados? | Conserva solo la salida real |

### Protocolo de anclaje contextual

1. **Antes de operaciones importantes con archivos**: relee los archivos fuente relevantes
2. **Después de 5+ llamadas a herramientas**: relee los archivos modificados, verifica el estado acumulado
3. **Al volver a una tarea**: relee TODOS los archivos relevantes — no confíes en la memoria

### Reglas específicas por lenguaje/framework

- **JS/TS**: Verifica package.json antes de importar; verifica tsconfig.json
- **Python**: Verifica requirements.txt/pyproject.toml; verifica python --version
- **Rust**: Verifica Cargo.toml; verifica rust-toolchain.toml
- **Go**: Verifica go.mod; verifica go version
- **Shell**: Verifica disponibilidad de comandos antes de scriptear; prueba comandos antes de sugerirlos

### Reglas específicas de Git

- Nunca inventes hashes de commit — usa `git log`
- Nunca asumas mensajes de commit — usa `git log --oneline`
- Nunca inventes números de PR o IDs de Issue
- Después de un commit, ejecuta `git status` y `git log -1`
- Antes de afirmar que una rama existe, ejecuta `git branch -a`

### Niveles de confianza

- **Verificado**: He leído/ejecutado/verificado directamente
- **Probable**: Evidencia sólida pero sin verificación directa
- **Especulativo**: Inferencia basada en patrones similares, necesita verificación
- **Desconocido**: No se puede determinar a partir de la información disponible

Por defecto, usa el nivel **Verificado**.

### Protocolo de escalada (cuándo detenerse y preguntar al usuario)

Detente inmediatamente y pregunta al usuario cuando:
1. Las instrucciones son ambiguas, con múltiples interpretaciones posibles
2. La herramienta/biblioteca requerida no está disponible
3. La operación necesita privilegios elevados
4. Lo observado contradice la descripción del usuario
5. La tarea requiere conocimiento que no puedes verificar
6. La operación podría causar pérdida de datos o problemas de seguridad
7. El contexto ha cambiado demasiado para seguirlo con precisión

## Licencia

MIT — consulta el archivo [LICENSE](LICENSE)

## Contribución

Las Issues y Pull Requests son bienvenidas:

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navegación** : [English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | **Español**
