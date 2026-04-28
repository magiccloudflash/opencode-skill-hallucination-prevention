# Habilidad de OpenCode: Prevención de alucinaciones de IA

> Una habilidad de agente de OpenCode que evita las alucinaciones de IA mediante la verificación forzada, el reconocimiento de incertidumbre y respuestas basadas en evidencia.

[English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Español](./README_ES.md) | Español (Cuba) | [Português](./README_PT.md) | [العربية](./README_AR.md)

## Características

- **Verifica antes de afirmar** — lee los ficheros, ejecuta el código, consulta la documentación antes de llegar a conclusiones
- **Reconoce la incertidumbre** — diferencia bien entre una especulación y un hecho verificado
- **No inventes ná** — nunca fabriques errores, APIs, resultados de pruebas o correcciones
- **Basate en evidencia** — cita las rutas de los ficheros, los números de línea y los resultados reales
- **Autoverificación** — lista de verificación de 10 puntos antes de cada respuesta
- **Guardas de generación de código** — doble verificación antes y después de escribir código
- **Reglas por herramienta** — reglas anti-alucinación específicas pa cada herramienta: read, write, edit, bash, grep, glob, etc.
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

> El fichero global `AGENTS.md` se carga automáticamente en cada sesión, sin necesidad de invocar la habilidad manualmente. Éso está de rechupete.

## Uso

### Bajo demanda (Método 1, 2)

OpenCode descubre automáticamente la habilidad. El agente la carga cuando la necesite:

```javascript
skill({ name: "hallucination-prevention" })
```

### Carga automática (Método 3)

Las reglas de `AGENTS.md` se inyectan automáticamente en el contexto de cada sesión. **No se requiere ninguna acción manual, mi socio.**

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
| `deny` | La habilidad se esconde del agente, acceso denegado |
| `ask` | Pide confirmación antes de cargar |

## Estructura del repositorio

```
opencode-skill-hallucination-prevention/
├── README.md                                 # Documentación en inglés
├── README_CN.md                              # Documentación en chino
├── README_RU.md                              # Documentación en ruso
├── README_FR.md                              # Documentación en francés
├── README_DE.md                              # Documentación en alemán
├── README_ES.md                              # Documentación en español
├── README_CU.md                              # Documentación en español cubano (este fichero)
├── README_PT.md                              # Documentación en portugués
├── README_AR.md                              # Documentación en árabe
├── LICENSE                                   # Licencia MIT
├── opencode.json                             # Ejemplo de configuración
├── AGENTS.md                                 # Reglas globales (auto-carga)
└── hallucination-prevention/
    └── SKILL.md                              # Fichero de definición de la habilidad
```

## Contenido de la habilidad v2.0

### Clasificación de los 8 tipos de alucinaciones

| Tipo | Descripción | Ejemplo | Prevención |
|------|-------------|---------|------------|
| **A: Fáctica** | Afirmar hechos no verificados | "Esta función devuelve una Promise" (sin leer el código) | Lee fuentes, ejecuta código |
| **B: Estructural** | Inventar ficheros/rutas | "El manejador está en src/routes/auth.ts" (el fichero no existe) | Usa glob/ls pa verificar |
| **C: Comportamental** | Suponer comportamiento del código | "El error está arreglao" (sin ejecutar el proyecto) | Ejecuta el código y muestra el resultado |
| **D: Externa** | Inventar APIs/funciones | "Lodash tiene una función groupByAndSort()" (no existe) | Verifica documentación o fuentes |
| **E: Resultado** | Imaginar resultados de pruebas | "Todas las pruebas pasan" (sin ejecutarlas) | Ejecuta las pruebas, muestra el resultado real |
| **F: Temporal** | Versiones/fechas incorrectas | "Node v24 añadió esta funcionalidad" (v24 no existe) | Verifica versión y changelog |
| **G: Autoridad** | Fingir haber verificado | "He confirmao que el cambio está correcto" (sin releer) | Haz la verificación y muestra evidencia |
| **H: Referencia** | Inventar URLs/IDs/commits | "Ver https://github.com/foo/issues/99999" (enlace falso) | Verifica la URL o pon "no verificaíto" |

### Reglas por herramienta

| Herramienta | Regla principal |
|-------------|-----------------|
| **read** | No adivines el contenido; verifica existencia del fichero; relee después de editar |
| **write** | Relee inmediatamente después de escribir; no crees documentación sin que te lo pidan |
| **edit** | Lee siempre antes de editar; relee después; respeta la sangría |
| **bash** | No inventes salidas de comandos; re-ejecuta pa verificar correcciones; pregunta antes de acciones destructivas |
| **grep** | Confirma existencia de funciones/clases mediante grep |
| **glob** | Usa glob pa verificar estructura antes de referenciar rutas |
| **question** | Si las instrucciones son ambiguas, pregunta primero, actúa después |

### Lista de verificación de 10 puntos (el "chéquiate antes de hablar")

Antes de cada respuesta, verifica:

| # | Pregunta | Acción si "No" |
|---|----------|-----------------|
| 1 | ¿De veras he LEÍDO los ficheros de los que hablo? | Léelos ahora mismo |
| 2 | ¿De veras he EJECUTAO los comandos cuya salida reporto? | Ejecútalos ahora |
| 3 | ¿De veras he VERIFICADO el paquete/API que estoy referenciando? | Verifica o márcalo como incierto |
| 4 | ¿Estoy seguro de que este mensaje de error es real? | Muestra la salida real |
| 5 | ¿He vuelto a leer los ficheros después de editarlos? | Vuelve a leerlos ahora |
| 6 | ¿Estoy adivinando la estructura de directorios? | Lista el directorio |
| 7 | ¿Estoy adivinando una firma de función? | Lee el código fuente |
| 8 | ¿He afirmado que una corrección funciona sin probarla? | Ejecuta el código |
| 9 | ¿Estoy inventando opciones de comandos? | Verifica --help |
| 10 | ¿Puede que mi respuesta contenga datos inventaos? | Deja solo la salida real |

### Protocolo de anclaje contextual

1. **Antes de operaciones importantes con ficheros**: relee los ficheros fuente relevantes
2. **Después de 5+ llamadas a herramientas**: relee los ficheros modificaos, verifica el estado acumulado
3. **Al volver a una tarea**: relee TOOS los ficheros relevantes — no confíes en la memoria

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

- **Verificao**: He leío/ejecutao/verificao directamente
- **Probable**: Evidencia sólida pero sin verificación directa
- **Especulativo**: Inferencia basá en patrones similares, necesita verificación
- **Desconocío**: No se puede determinar a partir de la información disponible

Por defecto, usa el nivel **Verificao**.

### Protocolo de escalada (cuándo parar y preguntarle al usuario)

Para en seco y pregúntale al usuario cuando:
1. Las instrucciones son ambiguas, con múltiples interpretaciones posibles
2. La herramienta/biblioteca requerida no está disponible
3. La operación necesita privilegios elevados
4. Lo que observas contradice lo que dijo el usuario
5. La tarea requiere conocimiento que no puedes verificar
6. La operación podría causar pérdida de datos o problemas de seguridad
7. El contexto ha cambiao mucho pa seguirlo con precisión

## Licencia

MIT — consulta el fichero [LICENSE](LICENSE)

## Contribución

Las Issues y Pull Requests son bienvenidas, ¡qué bola!

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**Navegación** : [English](./README.md) | [中文](./README_CN.md) | [Русский](./README_RU.md) | [Français](./README_FR.md) | [Deutsch](./README_DE.md) | [Español](./README_ES.md) | **Español (Cuba)** | [Português](./README_PT.md) | [العربية](./README_AR.md)
