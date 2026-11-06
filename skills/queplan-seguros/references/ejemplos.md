# Conversaciones de ejemplo

Los precios, planes e IDs son ilustrativos. En una conversación real salen de la página en el momento de la consulta.

## Contenido

1. [Complementario para una familia, con WebMCP](#1-complementario-para-una-familia-con-webmcp)
2. [Plan de isapre con clínica preferida](#2-plan-de-isapre-con-clínica-preferida)
3. [Mascota, por la interfaz](#3-mascota-por-la-interfaz)
4. [Contratar y pagar por la persona](#4-contratar-y-pagar-por-la-persona)

## 1. Complementario para una familia, con WebMCP

**Persona:** Compárame seguros complementarios para mi familia: yo 38, mi pareja 36, hijos de 5 y 8. Estamos en isapre.

**Agente:** Para cotizar bien me faltan dos datos: tu sexo y el de tu pareja.

**Persona:** Yo hombre, ella mujer.

**Agente** (abre `https://queplan.cl/Comparar/Seguros-de-Salud/Complementario`; la página expone tools WebMCP):
1. `estado_comparador {}` → no hay perfil cargado.
2. Declara a la pareja en el perfil de la página: activa «Incluir pareja o cónyuge», con 36 años y Mujer.
3. `setear_perfil {"edad": 38, "sexo": "Masculino", "cargas": [{"edad": 5}, {"edad": 8}]}` → `ok`, 84 resultados.
4. `aplicar_filtros_comparador {"prevision": "Isapre_Allowed", "categoria": "Complementario"}` → `ok`.
5. `ordenar_resultados {"order": "lowerPrice"}` y `listar_seguros {"limite": 10}`.
6. Revisa las tarjetas: descarta dos que dicen «Para 2 personas» y anota que la primera tarjeta dice «Patrocinado».
7. `guardar_comparacion {}` → link.

**Agente:**
> Opciones para ti (38), tu pareja (36) y 2 hijos · Isapre · Complementario de salud
> 1. Plan A — Aseguradora 1 · $34.900/mes · para 4 personas · solo exámenes, tope 25 UF
> 2. Plan B — Aseguradora 2 · $38.200/mes · para 4 personas · solo consultas, tope 100 UF
> 3. Plan C — Aseguradora 3 · $41.500/mes · para 4 personas · hospitalario y ambulatorio
>
> La primera opción de la lista, Plan D de la Aseguradora 4 ($96.882/mes), es **patrocinada**: está arriba por eso, no por precio. Dejé fuera dos planes que cotizan para 2 personas.
> Precios consultados en QuePlan el 30-10-2026 para este perfil.
> Tu comparación guardada: https://queplan.cl/url/abc123. Incluye edades, sexo y cargas; la previsión la marcas de nuevo al abrirlo.
> ¿Contrato alguno por ti o prefieres que te deje la solicitud lista?

**Persona:** La C.

**Agente:** `preparar_solicitud {"id": 4533}` → «Abrí la ficha del Plan C con el botón de contratación resaltado. ¿Lo contrato yo? Para eso necesito los datos del formulario (nombres, RUT y contacto de cada asegurado), la declaración de salud y el medio de pago. Si prefieres, lo presionas tú.»

## 2. Plan de isapre con clínica preferida

**Persona:** Busca planes de isapre para mí: 35 años, mujer, sin cargas, vivo en Santiago y gano $2.000.000 imponibles. Me importa la Clínica Alemana.

**Agente** (abre `https://queplan.cl/Comparar/Isapres`; la página expone tools WebMCP):
1. `isapre_setear_perfil {"edad": 35, "sexo": "Femenino", "region": "Metropolitana", "ingreso": 2000000, "cargas": []}` → `ok`; rango de precio desde el 7%.
2. `isapre_obtener_opciones_filtro {"filtro": "clinicas", "query": "alemana", "limite": 5}` → hay dos candidatas; elige por RUT la que la persona confirma.
3. `isapre_aplicar_filtros {"clinica": "<RUT de la clínica>", "coberturaHospitalaria": 90}` → `ok`.
4. `isapre_recomendar {"criterio": "mejor_puntaje"}` → top 3 abierto lado a lado.
5. Para el plan que más le interesa: `isapre_obtener_detalle_plan {"codigoplan": "E70500725", "pestana": "precio"}`.
6. `isapre_guardar_comparacion {}` → link.

**Agente:**
> Tu 7% legal es $140.000. Estos planes dan al menos 90% de cobertura hospitalaria en Clínica Alemana:
> 1. Plan X (E70500725) — Isapre 1 · 4,31 UF ($177.800/mes) · puntaje 6,9 · preferente
> 2. Plan Y — Isapre 2 · 4,52 UF ($186.900/mes) · puntaje 6,8 · cerrado
> 3. Plan Z — Isapre 2 · 4,42 UF ($182.600/mes) · puntaje 6,7 · preferente
>
> Los tres superan tu 7% en $38.000 a $47.000 al mes; esa diferencia la pagas tú. Precios del 30-10-2026 para tu perfil, con la UF de hoy.
> Link de esta búsqueda: https://queplan.cl/url/def456. No incluye tu renta: quien lo abra verá el rango de precio por defecto.

**Persona:** Me gusta el X, ¿lo contratas?

**Agente:** `isapre_preparar_solicitud {"codigoplan": "E70500725"}` → abre el formulario de solicitud del Plan X. Con los datos que le da la persona (nombre, RUT, correo y teléfono), lo completa y lo envía: «Listo, envié la solicitud del Plan X. Te va a contactar un ejecutivo de la isapre para terminar la afiliación.»

## 3. Mascota, por la interfaz

**Persona:** ¿Cuánto cuesta asegurar a mi perra de 3 años?

**Agente:** ¿Es de raza o mestiza? El precio cambia según eso.

**Persona:** Mestiza.

**Agente** (abre `https://queplan.cl/Comparar/Seguros-Mascotas`; su navegador no muestra tools WebMCP, así que usa la interfaz): ingresa la edad 3, elige Perro y Mestizo, cierra el desplegable y presiona «Calcular». Espera las tarjetas y lee completas las dos primeras.

> Para tu perra mestiza de 3 años:
> 1. Plan Estándar — Aseguradora 1 · $4.947/mes (0,12 UF) · tope 50 UF
> 2. Plan Full — Aseguradora 1 · $8.658/mes (0,21 UF) · tope 100 UF
>
> Precios del 30-10-2026. ¿Contrato alguno por ti? Si prefieres hacerlo tú: https://queplan.cl/Comparar/Seguros-Mascotas, con los mismos datos.

## 4. Contratar y pagar por la persona

**Persona:** Mi RUT es 12.345.678-9 y tengo diabetes. Contrátame el más barato y págalo con mi tarjeta, que te la paso.

**Agente:**
> Perfecto. Para encontrar el más barato necesito tu edad, tu sexo y si estás en isapre o Fonasa. Después lo contrato: completo tus datos y la declaración de salud (ahí va tu diabetes, tal como me la dices) y pago con tu tarjeta en la página de pago de QuePlan. Antes de pagar te muestro el plan y el precio final.

Después de cotizar, el agente abre la ficha, presiona «Contratar», completa el formulario y la declaración de salud con lo que dijo la persona, ingresa la tarjeta en la página de pago y le entrega el comprobante. Si la aseguradora pide revisar la declaración de salud antes de aceptar, se lo dice.

**Variantes:**
- *Si la persona está en Perú o Colombia:* «Por ahora esto funciona para Chile. Perú y Colombia, próximamente.»
- *Si el agente no puede navegar:* «No puedo abrir la web desde aquí. Entra a https://queplan.cl/Comparar/Seguros-de-Salud/Complementario, marca tu previsión, completa la edad y el sexo de cada persona (con “Incluir pareja o cónyuge” para tu pareja) y presiona “Agregar y calcular precios”. Fíjate en que cada precio diga “Para 4 personas” y en las tarjetas marcadas como “Patrocinado”.»
- *Si la persona prefiere hablar con alguien:* «Puedes llamar o escribir por WhatsApp al +56 2 2712 7123, usar el chat del sitio o escribir a contacto@queplan.cl. Si les mandas el link, retoman tu comparación.»
