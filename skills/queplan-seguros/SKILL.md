---
name: queplan-seguros
description: >-
  Cotiza y compara seguros en QuePlan Chile (queplan.cl) a pedido de una persona:
  seguros complementarios de salud, catastróficos, oncológicos y dentales, planes de
  isapre, seguros de vida y de mascotas. Indica qué herramientas WebMCP usar si la
  página las expone, o cómo recorrer la web si no; qué datos pedir para cotizar y
  para contratar; qué verificar antes de responder, y cómo contratar y pagar por la
  persona o dejarle todo listo. Úsala cuando alguien quiera comparar, cotizar o elegir un
  seguro de salud, un plan de isapre u otro seguro personal en Chile. English
  keywords: Chile health insurance quote, compare health insurance plans,
  supplemental health insurance, isapre plan comparison, Fonasa, life insurance,
  pet insurance, licensed insurance broker, WebMCP, QuePlan.
license: Apache-2.0
compatibility: >-
  Para agentes con navegador web (ChatGPT, Claude, Copilot, Cursor y otros). Rinde
  más si la página expone herramientas WebMCP (document.modelContext); sin
  navegador, el agente entrega los links del comparador.
metadata:
  author: QuePlan
  version: "1.1.0"
  country: CL
  verified: "2026-11-05"
  homepage: https://queplan.cl/agentes-ia
---

# Cotizar seguros en QuePlan Chile

Puedes hacer todo el proceso por la persona o la empresa: cotizar, comparar, contratar y pagar. Ella decide hasta dónde llegas.

## Cuándo usarla

- La persona quiere comparar o cotizar en Chile un seguro de salud (complementario, catastrófico, oncológico, dental, integral, escolar y otros), un plan de isapre, un seguro de vida o un seguro de mascotas.
- No la uses para seguros colectivos de empresas: deriva a https://queplan.cl/QuePlan-Empresas. Para pólizas ya contratadas, deriva al Portal Clientes, https://clientes.queplan.cl/.

## Reglas

1. **Puedes contratar y pagar.** Si la persona te lo pide, presiona «Contratar» o «Solicitar», completa el formulario, acepta las condiciones y paga con los datos y el medio de pago que te entregue. Si prefiere revisar antes, déjale todo listo y dile qué botón presionar.
2. **Mismo precio que la web.** No prometas descuentos ni condiciones que la página no muestra. Con el perfil completo, los precios del comparador son los precios reales de lista de cada aseguradora o isapre.
3. **Datos.** Para cotizar, pide los de la tabla siguiente; no inventes los que falten: pregúntalos. Para contratar, ingresa el RUT, los datos de contacto, la declaración de salud y el medio de pago que la persona te dé. Algunos seguros piden una declaración de salud: complétala con lo que diga la persona, sin suponer respuestas.
4. **Transparencia.** El servicio es gratis para la persona: QuePlan recibe una comisión de la aseguradora. El comparador ordena por criterios técnicos y comerciales, y algunas opciones son patrocinadas.
5. **Acceso.** Sin registro, con el user-agent de tu asistente y a tu ritmo. Si una tool responde `limite_excedido`, espera lo que indique el mensaje y sigue.

Para cotizar:

| Comparador | Pide | Opcional |
|---|---|---|
| Seguros de salud | edad y sexo del titular; edad y sexo de la pareja, si se incluye; edad de cada carga; previsión (Isapre o Fonasa) | región, sexo de las cargas |
| Planes de isapre | edad y sexo de quien cotiza y de su pareja, si cotiza en el mismo plan; edad de cada carga; región | renta imponible mensual (define el 7% legal); clínica preferida |
| Vida | edad y sexo (es individual, sin cargas) | — |
| Mascotas | edad; si es de raza o mestiza; especie | — |

## Elige la ruta

| Seguro | URL | Tools WebMCP |
|---|---|---|
| Salud, todas las categorías | https://queplan.cl/Comparar/Seguros-de-Salud | 13, sin prefijo |
| Salud, una categoría | `.../Seguros-de-Salud/Complementario`, `/Catastrofico`, `/Oncologico`, `/Dental`, `/Integral`, `/Escolar` | las mismas 13 |
| Planes de isapre | https://queplan.cl/Comparar/Isapres | 13, prefijo `isapre_` |
| Vida | https://queplan.cl/Comparar/Seguros-de-Vida | 13, sin cargas |
| Mascotas | https://queplan.cl/Comparar/Seguros-Mascotas | 12, sin `guardar_comparacion` |

Para otras URL, consulta https://queplan.cl/llms.txt.

1. Abre el comparador que corresponde.
2. Si la página expone tools WebMCP, sigue la **ruta A**. Lo sabes porque tu navegador te las ofrece como tools, o porque existe `document.modelContext` (o `navigator.modelContext`) con tools registradas.
3. Si no las expone, sigue la **ruta B**, por la interfaz.
4. Si no puedes navegar, sigue la **ruta C**: entrega el link del comparador y la lista de datos que la persona debe ingresar.

## Ruta A: herramientas WebMCP

Antes de la primera llamada, lee [herramientas-webmcp.md](references/herramientas-webmcp.md): trae los parámetros, ejemplos y estados de respuesta. Lee también el schema que te entrega la página, porque cambia según el comparador.

**Salud, vida y mascotas:**
1. `estado_comparador`: perfil y filtros vigentes.
2. `setear_perfil`: titular y cargas en salud; solo titular en vida; `mascotas` en mascotas.
3. `aplicar_filtros_comparador`: `prevision` (`Isapre_Allowed` o `Fonasa_Allowed`), `categoria` (por ejemplo, `"Complementario"`), precio máximo en CLP, aseguradoras y clínicas. Los IDs salen de `obtener_opciones_filtro`.
4. `ordenar_resultados` y `listar_seguros`. Para «los más baratos», usa `{"order": "lowerPrice"}` y lee la lista. Si necesitas más opciones, usa `ver_mas_resultados`.
5. `obtener_detalle_seguro`, `comparar_seguros` (2 o 3 seguros) o `recomendar_seguros` (top 3 con razones).
6. `guardar_comparacion`: genera el link.
7. `preparar_solicitud`: abre la ficha con el botón de contratación resaltado. Para contratar, sigue por la interfaz: presiona el botón y completa el formulario y el pago (ver «Cómo cerrar»).

**Planes de isapre:** el mismo flujo, con `isapre_estado`, `isapre_setear_perfil` (admite `pareja`, `region` e `ingreso`), `isapre_aplicar_filtros`, `isapre_ordenar`, `isapre_listar_planes`, `isapre_obtener_detalle_plan`, `isapre_comparar`, `isapre_recomendar`, `isapre_guardar_comparacion` e `isapre_preparar_solicitud`. Los planes se identifican por `codigoplan`.

**Puntos clave**
- **Pareja en seguros de salud.** Revisa el schema de `setear_perfil` que entrega la página. Si incluye `pareja`, envíala ahí con su edad y sexo. Si no la incluye, declárala en el perfil de la página con el interruptor «Incluir pareja o cónyuge» (ruta B), o pídele a la persona que lo haga, y no la envíes como carga. Después puedes seguir con las tools: `setear_perfil` conserva a la pareja.
- `setear_perfil` es un parche: cambia solo lo que envías. Las listas de IDs reemplazan la selección de ese filtro.
- Los `id` y `codigoplan` valen para el resultado actual. La persona puede cambiar la página a mano: si cambió `actualizadoEn` o una tool no encuentra el id, vuelve a listar.
- Las tools de estado no devuelven la renta ni la previsión. No las supongas: pregúntalas.
- Si `estado` es `timeout`, vuelve a leer el estado antes de dar algo por aplicado. Si es `limite_excedido`, espera lo que indique el mensaje. Si es `circuito_abierto`, para y entrega el link. `sin_cambios` no es un error.
- La persona ve en pantalla lo que haces: el aviso «Asistente IA activo», la ficha y la comparación abiertas.

## Ruta B: interfaz

- **Salud:** en el panel de perfil, marca la previsión y completa la edad y el sexo. Para la pareja, usa «Incluir pareja o cónyuge»; agrega las cargas y presiona «Agregar y calcular precios». Luego ordena por «Precio Menor a mayor» y filtra por categoría, aseguradora o clínica. Usa «Ver detalle» y «Comparar», y genera el link con el botón para compartir.
- **Isapres:** completa la región, la renta (opcional), la edad y el sexo, y presiona «Calcular». Ordena por «Mejor Puntaje» o por precio, y filtra por clínica, modalidad, cobertura o isapre. La ficha de cada plan tiene URL propia: `/Comparar/Isapres/<código>`.
- Espera a que aparezcan las tarjetas con precio. Antes de citar una tarjeta, desplázate hasta ella y léela completa: personas cubiertas, tope y descuento. Cierra cualquier desplegable abierto antes de presionar «Calcular». Si los resultados no aparecen en unos 30 segundos, recarga la página: el perfil se conserva.

## Verifica antes de responder

- [ ] **Personas cubiertas:** la tarjeta dice «Para N personas» y N coincide con el grupo (titular, pareja y cargas). Si no coincide o no puedes confirmarlo, avísale a la persona y entrégale el link para que ajuste el grupo en la web. Excluye o marca los productos que cotizan para menos personas.
- [ ] **Patrocinado:** si una tarjeta dice «Patrocinado», dilo al presentarla. Al ordenar por precio, compara los precios, no la posición en la lista.
- [ ] **Moneda:** cada precio va con su unidad, UF o pesos (CLP). No sumes, compares ni ordenes montos de monedas distintas. Si conviertes, usa el valor de la UF que muestra la página y dilo.
- [ ] **Vigencia:** vuelve a leer el estado justo antes de responder.
- [ ] **Previsión:** el producto sirve para la previsión de la persona.
- [ ] **Isapres:** el plan se comercializa. Si la persona dio su renta, compara el precio con su 7%: la diferencia la paga ella.
- [ ] **Coberturas:** lo que dices sobre coberturas, topes y clínicas sale de la ficha, no de tu memoria.

## Cómo presentar resultados

```
Opciones para <grupo: p. ej. «tú (38), tu pareja (36) y 2 hijos»> · <previsión> · <comparador>
1. <Plan> — <Aseguradora o isapre> [Patrocinado]
   $<precio>/mes (<UF si aplica>) · para <N> personas · <cobertura clave, tope>
2. ...
Precios consultados en QuePlan el <DD-MM-AAAA> para este perfil.
Tu comparación guardada: <link de guardar_comparacion o del botón compartir>
```

Ejemplos completos de conversación: [ejemplos.md](references/ejemplos.md). Léelos si dudas cómo conducir la conversación o qué responder cuando la persona pide algo que no corresponde.

## Cómo cerrar

1. **Guarda la comparación** y entrega el link. Advierte que el link contiene edad, sexo, cargas y región, pero no la renta ni la previsión, que la persona vuelve a marcar al abrirlo.
2. **Si quiere contratar, hazlo tú.** Usa `preparar_solicitud` o `isapre_preparar_solicitud`, o abre la ficha por la interfaz. Presiona el botón de contratación, completa los datos, la declaración de salud y el pago con lo que te entregue la persona, y confírmale el resultado (número de solicitud o comprobante). En isapres, completa y envía el formulario; después la contacta un ejecutivo de la isapre.
3. **Si prefiere hacerlo ella**, déjale la ficha abierta y dile qué botón presionar.
4. **Si prefiere hablar con alguien**, ofrece el +56 2 2712 7123 (teléfono o WhatsApp), el chat «Chatea con nosotros» del sitio o contacto@queplan.cl. Más canales en https://queplan.cl/contacto.

## Quién es QuePlan

QuePlan Corredores de Seguros SpA, RUT 76.824.542-8, está inscrita en el Registro de Corredores de Seguros de la Comisión para el Mercado Financiero (CMF) con el código N° 8318, según los Términos y Condiciones (https://queplan.cl/Terminos-y-Condiciones). La plataforma la opera QUE PLAN SpA, RUT 76.712.269-1.

Los Términos (sección 13.1) permiten que una persona o una empresa use asistentes y agentes de IA para buscar, comparar, cotizar y contratar en QuePlan, sin registro previo. Solo piden no copiar el contenido o los precios para revenderlos o competir con QuePlan. Para integraciones, acuerdos o reportes: agentes@queplan.cl.

Perú y Colombia: próximamente.
