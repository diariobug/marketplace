[English](README.md) | **Español**

# Marketplace de Diario Bug

*Skills* de pruebas de software de [Diario Bug](https://diariobug.com) para tu agente de código.
Gratuitos para todos. Los suscriptores reciben los kits con la guía del método y se enteran
primero de los nuevos *skills*: [diariobug.com/recursos/](https://diariobug.com/recursos/).

## Skills

**Actividades de prueba**: se aplican a cualquier tipo de prueba.

- **test-planning**: el punto de entrada. Decide qué pruebas necesita un cambio por nivel
  (componente, integración, sistema, aceptación) y por tipo (funcional, no funcional, caja
  negra, caja blanca), y pasa partes del trabajo a los otros *skills*. Planea pruebas nuevas,
  revisa las pruebas de un PR y evalúa la cobertura de un módulo. Pregúntale «¿está bien
  probado este PR?» o «planea las pruebas de este cambio».
- **test-design**: diseña los casos de prueba: particiones de equivalencia, valores límite,
  tablas de decisión, transiciones de estado, la cobertura de ramas leída como un mapa,
  predicción de errores y misiones de pruebas exploratorias. Elige la técnica según la forma
  de la regla, pregunta por los huecos que el diseño deja al descubierto y toma cada resultado
  esperado de los requisitos, nunca del código. Pregúntale «¿qué entradas debería probar
  aquí?» o «diseña las pruebas de esta regla».
- **spec-review**: revisa un documento de requisitos, un diseño técnico, un ADR, un plan de
  pruebas o la descripción de un PR con enfoques específicos (consistencia, capacidad de
  prueba, trazabilidad, conformidad con la arquitectura, seguridad, rendimiento, confiabilidad,
  operabilidad) y después prioriza: como máximo cinco hallazgos bloqueantes para el autor y
  unas cuantas preguntas para la revisión con personas. Pregúntale «revisa este PRD» o
  «compara este diseño con sus requisitos».
- **regression-strategy**: decide qué pruebas correr, dónde, cuándo y cómo, para que una
  suite que crece siga siendo rápida, estable y confiable. Asigna cada prueba a un punto de
  control, elige el mecanismo de selección más simple que funcione a tu escala y define reglas
  de salud para las pruebas inestables y lentas.

**Riesgos específicos y tipos de prueba**: lo que un tipo de prueba necesita además de las
actividades.

- **dependency-failures**: cómo puede fallar tu sistema por aquello que no controla (SDK,
  API, servicios en la nube, scripts de terceros), con un análisis de fronteras basado en STPA.
  Produce escenarios de pérdida respaldados por evidencia en el código, mitigaciones y las
  pruebas que conviene agregar.

Los nuevos *skills* llegan con los artículos que los enseñan; los suscriptores se enteran
primero.

**Nombres nuevos desde la versión 1.4.0:** `testing-by-level-and-type` → `test-planning`,
`test-design-techniques` → `test-design`, `artifact-review` → `spec-review`,
`dependency-resilience-audit` → `dependency-failures`. Claude Code toma los nombres nuevos al
actualizar; con `npx skills` o Cline, elimina las carpetas anteriores y agrega las nuevas.

## Instalación (Claude Code)

```sh
/plugin marketplace add diariobug/marketplace
/plugin install diariobug
```

Actualízalo después con `/plugin update diariobug`. Los *skills* llevan el nombre del plugin
como prefijo: `/diariobug:test-planning`, `/diariobug:test-design`, `/diariobug:spec-review`,
`/diariobug:regression-strategy`, `/diariobug:dependency-failures`. Puedes agregar un flujo de
trabajo después del nombre: `/diariobug:test-planning review`.

## Instalación (Cursor, Codex, Gemini CLI, Copilot y otros agentes)

Las mismas carpetas de *skills* funcionan en cualquier agente que admita `SKILL.md`. Con la
[CLI `skills`](https://github.com/vercel-labs/skills), que los instala en la mayoría de los
agentes de código:

```sh
npx skills add diariobug/marketplace                                  # elige los skills y los agentes
npx skills add diariobug/marketplace --skill test-planning
```

Con Cline (se instala con la misma CLI):

```sh
cline skill add diariobug/marketplace -g --skill '*' -y    # todos los skills, para tu usuario
cline skill update -g                                     # después, para actualizar
```

¿Tu agente no tiene marketplace? Sin Node.js, descarga este repositorio
([ZIP](https://github.com/diariobug/marketplace/archive/refs/heads/main.zip)) y copia la
carpeta del *skill* que quieras, por ejemplo `plugins/diariobug/skills/test-planning/`, a la
carpeta de *skills* de tu agente. Si tu agente no admite *skills*, agrega el contenido de
`SKILL.md`, con sus carpetas `references/` y `workflows/`, a las instrucciones de tu proyecto.

## Privacidad

El plugin no hace llamadas de red. Sin telemetría ni rastreo: solo lee el proyecto que le
indiques.

## Licencia

CC BY-NC-SA 4.0: puedes compartirlo y adaptarlo sin fines comerciales, siempre que des crédito
a Diario Bug y publiques tus versiones con la misma licencia. Texto completo:
https://creativecommons.org/licenses/by-nc-sa/4.0/deed.es
