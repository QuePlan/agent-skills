# Skills de QuePlan para agentes de IA

Skills oficiales de [QuePlan](https://queplan.cl), corredora de seguros regulada por la CMF en Chile, en el formato abierto [Agent Skills](https://agentskills.io). Sirven para que un asistente o agente de IA (Claude, ChatGPT, Codex, Copilot, Cursor y otros) cotice y compare seguros en QuePlan **a pedido de una persona o empresa**. La persona confirma y contrata en QuePlan.

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
- **Cualquier agente compatible** (Claude Code, Codex, Cursor, OpenCode y otros), con el CLI [skills](https://skills.sh):
  ```
  npx skills add QuePlan/agent-skills
  ```
- **Claude (web y app):** descarga el `.zip` del [último release](https://github.com/QuePlan/agent-skills/releases/latest) y súbelo en Personalizar › Skills.
- **A mano:** copia la carpeta `skills/queplan-seguros` en la carpeta de skills de tu agente (por ejemplo, `~/.claude/skills/` o `~/.codex/skills/`).
- **Descubrimiento automático:** https://queplan.cl/.well-known/agent-skills/index.json, según el [RFC de Agent Skills Discovery](https://github.com/cloudflare/agent-skills-discovery-rfc).

## Reglas

- La persona o la empresa confirma lo esencial en QuePlan: aceptar el contrato, la declaración de salud y el pago.
- Mismo precio que la web. Los precios son referenciales; el precio final lo fija cada aseguradora o isapre.
- Datos mínimos: nunca RUT, tarjetas, claves ni datos de salud.
- Los [Términos y Condiciones](https://queplan.cl/Terminos-y-Condiciones) (sección 13.1) permiten usar agentes de IA para cotizar y contratar. No permiten copiar el contenido o los precios para revenderlos, competir o captar clientes.

Más información: https://queplan.cl/agentes-ia · Integraciones, acuerdos y reportes: agentes@queplan.cl

---

## QuePlan skills for AI agents (English)

Official [Agent Skills](https://agentskills.io) from [QuePlan](https://queplan.cl), a CMF-licensed insurance broker in Chile. They let an AI assistant or agent quote and compare health insurance, isapre plans, life and pet insurance on queplan.cl **on behalf of a person or a company**, using the comparator's WebMCP tools when available. The person always confirms and buys on QuePlan.

Install with `/plugin marketplace add QuePlan/agent-skills` (Claude Code) or `npx skills add QuePlan/agent-skills` (any compatible agent). Discovery index: https://queplan.cl/.well-known/agent-skills/index.json. Agents acting for a user are allowed by QuePlan's Terms (section 13.1); scraping to resell, compete or divert customers is not. Contact: agentes@queplan.cl.

## Licencia / License

[Apache-2.0](LICENSE) © 2026 QUE PLAN SpA
