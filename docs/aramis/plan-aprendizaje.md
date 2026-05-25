# Plan de aprendizaje para entender agent-skills

Este plan ordena los temas que conviene estudiar para entender como funciona el
repositorio, como se usa y como modificarlo con criterio.

## 1. Proposito del repositorio

- Leer `README.md`.
- Entender que este repo no es una aplicacion tradicional: es un paquete de
  instrucciones, workflows, personas, comandos y referencias para agentes de IA.
- Identificar el problema que resuelve: hacer que los agentes sigan procesos de
  ingenieria reproducibles en lugar de improvisar.

## 2. Mapa de directorios

- Revisar `CLAUDE.md` para una vista compacta de la estructura.
- Ubicar las piezas principales:
  - `skills/`: workflows reutilizables.
  - `agents/`: personas especializadas.
  - `.claude/commands/`: comandos slash para Claude Code.
  - `.gemini/commands/`: comandos equivalentes para Gemini CLI.
  - `references/`: checklists auxiliares.
  - `hooks/`: automatizaciones de sesion.
  - `docs/`: guias de instalacion y formato.
  - `scripts/`: herramientas de validacion.

## 3. Modelo mental: skills, personas y comandos

- Leer `agents/README.md`.
- Distinguir los tres niveles:
  - Skill: el como, con pasos y criterios de salida.
  - Persona: el quien, con una perspectiva y formato de respuesta.
  - Command: el cuando, como punto de entrada para el usuario.
- Aprender la regla central: las personas no invocan otras personas; el usuario o
  los comandos coordinan.

## 4. Anatomia de un skill

- Leer `docs/skill-anatomy.md`.
- Abrir 2 o 3 ejemplos en `skills/*/SKILL.md`.
- Verificar que cada skill tiene:
  - frontmatter `name` y `description`;
  - secciones de uso, proceso, racionalizaciones, red flags y verificacion;
  - instrucciones accionables, no solo consejos generales.

## 5. Skills por fase del ciclo de desarrollo

- Estudiar los skills en este orden:
  1. `using-agent-skills`
  2. `spec-driven-development`
  3. `planning-and-task-breakdown`
  4. `incremental-implementation`
  5. `test-driven-development`
  6. `debugging-and-error-recovery`
  7. `code-review-and-quality`
  8. `shipping-and-launch`
- Despues revisar los skills especializados: Lucia, UI, API, seguridad,
  performance, documentacion, migraciones, CI/CD y simplificacion.

## 6. Comandos slash

- Leer los archivos en `.claude/commands/`.
- Mapear cada comando con los skills que activa:
  - `/spec`: crea especificacion.
  - `/plan`: descompone tareas.
  - `/build`: implementa incrementalmente con TDD.
  - `/test`: prueba comportamiento o bugs.
  - `/review`: revisa calidad.
  - `/code-simplify`: simplifica sin cambiar comportamiento.
  - `/ship`: coordina revision final y decision de salida.

## 7. Personas en `agents/`

- Leer `agents/code-reviewer.md`, `agents/security-auditor.md` y
  `agents/test-engineer.md`.
- Entender para que sirve cada una y cuando se invoca directamente.
- Estudiar el patron de fan-out de `/ship`: tres personas revisan en paralelo y
  el agente principal sintetiza una decision.

## 8. Orquestacion

- Leer `references/orchestration-patterns.md`.
- Aprender los patrones permitidos:
  - invocacion directa;
  - comando de una sola persona;
  - fan-out paralelo con merge;
  - pipeline secuencial dirigido por el usuario;
  - investigacion aislada.
- Identificar anti-patrones: crear una persona router, encadenar personas o
  hacer arboles profundos de agentes.

## 9. Instalacion e integraciones

- Leer `docs/getting-started.md`.
- Revisar las guias especificas segun herramienta:
  - `docs/cursor-setup.md`
  - `docs/gemini-cli-setup.md`
  - `docs/opencode-setup.md`
  - `docs/codex-setup.md`
  - `docs/windsurf-setup.md`
  - `docs/copilot-setup.md`
- Entender que Claude Code usa el plugin, mientras otros agentes consumen los
  Markdown como reglas o instrucciones.

## 10. Plugin de Claude Code

- Leer `.claude-plugin/plugin.json`.
- Ver que declara:
  - nombre y descripcion del plugin;
  - ruta de comandos;
  - ruta de skills;
  - lista de agentes.
- Revisar `.claude-plugin/marketplace.json` si se quiere publicar o instalar el
  fork como marketplace propio.

## 11. Hooks

- Leer `hooks/hooks.json`.
- Revisar `hooks/session-start.sh` para entender que ocurre al iniciar sesion.
- Revisar los hooks auxiliares solo cuando se necesite modificar el
  comportamiento de arranque o cache.

## 12. Validacion del repo

- Leer `scripts/validate-skills.js`.
- Entender que valida:
  - existencia de `SKILL.md`;
  - frontmatter valido;
  - coincidencia entre nombre de directorio y `name`;
  - longitud de `description`;
  - secciones requeridas;
  - referencias rotas entre skills.
- Ejecutar:

```bash
node scripts/validate-skills.js
```

## 13. CI

- Leer `.github/workflows/test-plugin-install.yml`.
- Entender las tres fases:
  - validar contenido de skills;
  - validar estructura del plugin con Claude Code;
  - probar instalacion del plugin.

## 14. Como modificar un skill existente

- Elegir un skill concreto.
- Cambiar primero la descripcion si cambia el disparador.
- Mantener el proceso accionable y verificable.
- Evitar duplicar contenido que ya existe en otro skill o en `references/`.
- Ejecutar el validador antes de considerar terminado el cambio.

## 15. Como crear un skill nuevo

- Seguir `docs/skill-anatomy.md`.
- Crear `skills/<nombre>/SKILL.md`.
- Usar nombre en kebab-case.
- Escribir una descripcion que diga que hace y cuando usarlo.
- Agregar scripts solo si aportan ejecucion real.
- Actualizar `README.md` si el skill debe aparecer en el catalogo publico.
- Ejecutar `node scripts/validate-skills.js`.

## 16. Como adaptar este fork

- Revisar metadata que todavia apunta al upstream.
- Decidir si el fork sera:
  - copia personal para aprendizaje;
  - plugin personalizado;
  - contribucion de vuelta al upstream.
- Si sera plugin propio, actualizar `.claude-plugin/plugin.json`,
  `.claude-plugin/marketplace.json` y documentacion de instalacion.

## 17. Preguntas practicas para comprobar entendimiento

- Que diferencia hay entre un skill y una persona?
- Por que `/ship` usa tres personas en paralelo?
- Que archivo valida la estructura de los skills?
- Donde se cambia la metadata del plugin?
- Como agregarias un nuevo skill sin romper CI?
- Cuando conviene usar `references/` en vez de meter mas texto en `SKILL.md`?
- Que partes tendrias que tocar para publicar este fork como plugin propio?

## 18. Ruta recomendada de lectura

1. `README.md`
2. `CLAUDE.md`
3. `AGENTS.md`
4. `docs/skill-anatomy.md`
5. `agents/README.md`
6. `.claude/commands/*.md`
7. `references/orchestration-patterns.md`
8. `scripts/validate-skills.js`
9. `.claude-plugin/plugin.json`
10. `.github/workflows/test-plugin-install.yml`
