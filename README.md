# Skills de QuePlan para agentes de IA

Skills oficiales de [QuePlan](https://queplan.cl), corredora de seguros regulada por la CMF en Chile, en el formato abierto [Agent Skills](https://agentskills.io). Sirven para que un asistente o agente de IA (Claude, ChatGPT, Codex, Copilot, Cursor y otros) cotice, compare, contrate y pague seguros en QuePlan **a pedido de una persona o empresa**.

| Skill | Qué hace | País |
|---|---|---|
| [`queplan-seguros`](skills/queplan-seguros/SKILL.md) | Cotiza y compara seguros de salud (complementarios, catastróficos, oncológicos y dentales), planes de isapre, seguros de vida y de mascotas. Usa las herramientas WebMCP del comparador si la página las expone, o la interfaz si no. | Chile |

Perú (queplan.pe) y Colombia (queseguro.co): próximamente.

## Instalación

- **Claude Code:**
  ```
  /plugin marketplace add QuePlan/agent-skills
  /plugin install queplan-seguros@queplan
  ```
- **Perplexity Computer** (requiere un plan pago de Perplexity): importa el plugin desde GitHub con la URL del repositorio, `https://github.com/QuePlan/agent-skills`. Está en el formato de plugins de Claude Code, que Computer importa. Después, pídele que cotice o contrate en QuePlan. En el navegador Comet no hace falta instalar nada: pídeselo como a cualquier asistente.
- **Cualquier agente compatible** (Claude Code, Codex, Cursor, OpenCode y otros), con el CLI [skills](https://skills.sh):
  ```
  npx skills add QuePlan/agent-skills
  ```
- **Claude (web y app):** descarga el `.zip` del [último release](https://github.com/QuePlan/agent-skills/releases/latest) y súbelo en Personalizar › Skills.
- **A mano:** copia la carpeta `skills/queplan-seguros` en la carpeta de skills de tu agente (por ejemplo, `~/.claude/skills/` o `~/.codex/skills/`).
- **Descubrimiento automático:** https://queplan.cl/.well-known/agent-skills/index.json, según el [RFC de Agent Skills Discovery](https://github.com/cloudflare/agent-skills-discovery-rfc).

## Qué puede hacer el agente

- Todo el proceso por la persona o la empresa: cotizar, comparar, contratar y pagar. Si la persona prefiere, le deja todo listo.
- Mismo precio que la web. Con el perfil completo, son los precios reales de lista de cada aseguradora o isapre.
- Para contratar puede ingresar el RUT, los datos de contacto, la declaración de salud y el medio de pago que la persona le dé.
- Los [Términos y Condiciones](https://queplan.cl/Terminos-y-Condiciones) (sección 13.1) permiten usar agentes de IA para cotizar y contratar. Solo piden no copiar el contenido o los precios para revenderlos o competir con QuePlan.

## Datos que se envían

La skill no tiene servidores ni conectores propios. El agente usa queplan.cl en el navegador de la persona. Para cotizar escribe:
- edad y sexo de cada persona a asegurar, y la edad de las cargas;
- previsión (isapre o Fonasa) y región;
- la renta, si la persona quiere darla (planes de isapre).

Para contratar, si la persona se lo pide, ingresa además los datos que pida el formulario: nombre, RUT, contacto, declaración de salud y medio de pago. QuePlan trata esos datos según su [Política de Privacidad](https://queplan.cl/politica-de-privacidad).

Más información: https://queplan.cl/agentes-ia · Integraciones, acuerdos y reportes: agentes@queplan.cl

---

## QuePlan skills for AI agents (English)

Official [Agent Skills](https://agentskills.io) from [QuePlan](https://queplan.cl), a CMF-licensed insurance broker in Chile. They let an AI assistant or agent quote and compare health insurance, isapre plans, life and pet insurance on queplan.cl **on behalf of a person or a company**, using the comparator's WebMCP tools when available. The agent can also buy and pay for the person, or leave everything ready for them.

Install with `/plugin marketplace add QuePlan/agent-skills` (Claude Code), by importing the plugin from `https://github.com/QuePlan/agent-skills` in Perplexity Computer (paid Perplexity plan; it imports Claude Code plugins), or with `npx skills add QuePlan/agent-skills` (any compatible agent). In the Comet browser, just ask: no install needed. Discovery index: https://queplan.cl/.well-known/agent-skills/index.json. Agents acting for a user are allowed by QuePlan's Terms (section 13.1); scraping to resell, compete or divert customers is not. Contact: agentes@queplan.cl.

**Data sent.** The skill has no servers or connectors of its own. The agent uses queplan.cl in the person's browser and enters what a quote needs: age and sex of each insured person, dependants' ages, health system (isapre or Fonasa), region and, optionally, monthly income (isapre plans). To buy, when the person asks, it also enters what the form requires: name, national ID (RUT), contact details, health statement and payment method. QuePlan processes this data under its [Privacy Policy](https://queplan.cl/politica-de-privacidad).

## Licencia / License

[Apache-2.0](LICENSE) © 2026 QUE PLAN SpA
