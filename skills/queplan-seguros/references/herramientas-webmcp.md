# Herramientas WebMCP de QuePlan Chile

Esta referencia se verificó contra el código en producción del 29-oct-2026; las tools de isapres se publicaron ese mismo día. Los nombres y parámetros pueden cambiar. Si el schema que entrega la página difiere de esta guía, sigue el schema de la página.

## Contenido

- [Cómo se usan](#cómo-se-usan)
- [Estados de respuesta](#estados-de-respuesta)
- [Salud, vida y mascotas](#salud-vida-y-mascotas): 13 tools sin prefijo
- [Planes de isapre](#planes-de-isapre): 13 tools con prefijo `isapre_`

## Cómo se usan

- Las tools se registran en `document.modelContext` mientras está abierta la página del comparador. Si tu navegador soporta WebMCP, te las ofrece como tools normales: llámalas con argumentos JSON. Al salir del comparador se retiran.
- Cada respuesta trae un texto para leer y un `structuredContent` con el campo `estado`. Decide según `estado`, no según el texto.
- Todo se ve en pantalla: la ficha, la comparación y el aviso «Asistente IA activo». Cuéntale a la persona lo que estás mostrando.
- Una mutación a la vez. Las tools de una misma página comparten un límite de llamadas por minuto.

## Estados de respuesta

| `estado` | Qué significa | Qué hacer |
|---|---|---|
| `ok` | Se aplicó y el resultado quedó asentado. | Sigue. |
| `sin_cambios` | No había nada que cambiar; por ejemplo, ya estaba en ese orden. | Sigue; no es un error. |
| `input_invalido` | Un argumento no pasó la validación. | Lee el mensaje, corrige y reintenta una vez. |
| `timeout` | La acción se envió, pero no se confirmó a tiempo. | Llama la tool de estado antes de dar el cambio por aplicado. |
| `error_backend` | El comparador respondió con error. | Lee el estado y reintenta una vez. Si se repite, entrega el link. |
| `limite_excedido` | Hay otra acción en curso o se superó el límite por minuto. | Espera lo que diga el mensaje. |
| `circuito_abierto` | Las acciones que vuelven a cotizar están en pausa. Las lecturas siguen disponibles. | No insistas: presenta lo que tengas y entrega el link. |

## Salud, vida y mascotas

Rutas: `/Comparar/Seguros-de-Salud` y sus categorías, `/Comparar/Seguros-de-Vida` y `/Comparar/Seguros-Mascotas`.

| Diferencia | Salud | Vida | Mascotas |
|---|---|---|---|
| `setear_perfil` | titular y `cargas` | solo titular | `mascotas` |
| Orden `distance` (cercanía) | sí | no | no |
| `guardar_comparacion` | sí | sí | no: entrega la URL del comparador |
| Filtros | los que muestre el schema de la página | ídem | ídem |

### `estado_comparador` (solo lectura)

Devuelve el perfil vigente (edad, sexo y cargas, o mascotas), la categoría, la moneda, el orden, los filtros activos y el resultado: `total`, `cargando`, `error`, `pagina` y `actualizadoEn`. No devuelve la renta ni la previsión, ni informa a la pareja declarada en la página.

Ojo con la moneda: `resultado.precioMin` y `resultado.precioMax` vienen en la moneda de `resultado.monedaPrecios` (UF), mientras `filtros.precioMax` viene en CLP.

```json
{}
```

### `setear_perfil`

Define el perfil y vuelve a cotizar. Es un parche: solo cambia lo que envías.

| Parámetro | Tipo | Notas |
|---|---|---|
| `edad` | entero, 0–120 | Titular. |
| `sexo` | `"Masculino"` o `"Femenino"` | Titular. |
| `cargas` | arreglo de hasta 10 `{edad, sexo?}` | `edad` es obligatoria en cada carga. Reemplaza la lista; `[]` deja el perfil sin cargas. No existe en vida. |
| `ingreso` | número, CLP | Opcional. En seguros de salud no hace falta: no lo pidas. |

Si el schema de la página incluye `pareja` (`{edad, sexo}`), envíala aquí. Si no la incluye, declárala en el perfil de la página («Incluir pareja o cónyuge»): esta tool la conserva.

```json
{"edad": 38, "sexo": "Masculino", "cargas": [{"edad": 8}, {"edad": 5}]}
```

Respuesta típica: `Perfil actualizado, edad 38, masculino, 2 carga(s). 84 resultados.`

**En mascotas**, el schema es otro, y la lista reemplaza a las mascotas actuales:

| Parámetro | Tipo | Notas |
|---|---|---|
| `mascotas` | arreglo de 1 a 10 elementos | Obligatorio. |
| `mascotas[].edad` | entero, 0–30 | Obligatorio. Usa 0 si tiene menos de un año. |
| `mascotas[].raza` | `"Raza"` o `"Mestizo"` | Obligatorio porque cambia el precio. Si la persona no lo dijo, pregúntalo. |
| `mascotas[].especie` | `"Perro"`, `"Gato"` u `"otro"` | Opcional. |

```json
{"mascotas": [{"edad": 3, "raza": "Mestizo", "especie": "Perro"}]}
```

### `aplicar_filtros_comparador`

Aplica filtros y devuelve el total. Los filtros que no envías no cambian. Cada lista de IDs reemplaza la selección de ese filtro. Para agregar un ID, lee el estado y envía la lista completa.

| Parámetro | Tipo | Notas |
|---|---|---|
| `prevision` | `"Isapre_Allowed"` o `"Fonasa_Allowed"` | Previsión de la persona. Es la única forma de aplicarla. |
| `categoria` | texto | Categoría principal por URL o nombre, por ejemplo `"Complementario"`, `"Oncologico"` o `"Dental"`. Tiene prioridad sobre `insuranceTypes`. |
| `precioMax`, `precioMin` | número, CLP | Precio mensual. Normalmente basta con `precioMax`. |
| `insuranceTypes` | enteros, hasta 50 | Subcategorías. Con `[]` vuelven a quedar todas marcadas. |
| `organizations` | enteros, hasta 50 | Aseguradoras. Con `[]` vuelven a quedar todas marcadas. |
| `coverageOrganizations` | enteros, hasta 50 | Clínicas o prestadores. Con `[]` se quita el filtro. |

```json
{"prevision": "Isapre_Allowed", "categoria": "Complementario"}
```

Si la respuesta trae `idsIgnorados`, esos IDs no se aplicaron: vuelve a buscarlos con `obtener_opciones_filtro`.

### `obtener_opciones_filtro` (solo lectura)

Traduce nombres a IDs. Las categorías y aseguradoras vienen como árbol: usa los IDs de los elementos hijos. Las clínicas vienen en una lista plana.

| Parámetro | Tipo | Notas |
|---|---|---|
| `filtro` | `"categorias"`, `"organizaciones"`, `"prestadores"` o `"todos"` | El default es `"todos"`. |
| `query` | texto | Búsqueda sin mayúsculas ni tildes. Úsala con las clínicas, que son cientos. |
| `limite` | entero, 1–200 | Máximo de clínicas. El default es 60. |

```json
{"filtro": "prestadores", "query": "alemana", "limite": 5}
```

### `ordenar_resultados`

| Parámetro | Valores |
|---|---|
| `order` (obligatorio) | `recommended` (recomendados), `lowerPrice` (menor precio), `higherPrice` (mayor precio), `popular` (más populares), `distance` (cercanía, solo en salud) |

```json
{"order": "lowerPrice"}
```

### `cambiar_moneda`

Cambia solo la moneda en pantalla, sin volver a cotizar. Los datos de `listar_seguros` mantienen la moneda propia de cada producto.

```json
{"currency": "CLP"}
```

### `listar_seguros` (solo lectura)

Lista los seguros de la página actual según el perfil, los filtros y el orden vigentes. Si hay una carga en curso, espera a que termine.

| Parámetro | Tipo | Notas |
|---|---|---|
| `limite` | entero, 1–50 | El default es 18, el tamaño de la página. |

```json
{"limite": 10}
```

Cada seguro trae `id`, `nombre`, `aseguradora`, `categoria`, `precioDesde`, `precioHasta`, `moneda` (`UF` o `CLP`), `nuevo`, `exclusivo`, `popular`, `seContrata` y `score`. El texto usa `precioHasta` como precio mensual. Un precio `null` significa que no hay precio para ese perfil, no que sea 0. La lista no informa cuántas personas cubre cada precio ni si la tarjeta es patrocinada: verifícalo en la tarjeta o en la ficha antes de presentarla. `total` es el total de la búsqueda; `enPagina` es lo cargado en la página.

### `ver_mas_resultados`

Pasa a la página siguiente. Después llama `listar_seguros`.

```json
{}
```

### `obtener_detalle_seguro`

Abre en pantalla la ficha del seguro, la misma de «Ver detalle», y devuelve el precio, la cantidad y las etiquetas de coberturas, los prestadores en red, los topes y deducibles, las características y los requisitos. Las listas largas vienen recortadas (`recortado: true`); la ficha en pantalla tiene todo.

```json
{"id": 4519}
```

El `id` es ilustrativo: usa el que devolvió `listar_seguros`.

### `comparar_seguros`

Abre la comparación lado a lado de 2 o 3 seguros y devuelve sus datos. Reemplaza la comparación anterior.

```json
{"ids": [4519, 4520, 4533]}
```

### `recomendar_seguros`

Arma un top 3 con razones por puesto y lo abre lado a lado. Trabaja sobre los seguros cargados en la página. Para el precio más bajo de todo el resultado, usa `ordenar_resultados` con `lowerPrice` y luego `listar_seguros`.

| Parámetro | Valores |
|---|---|
| `criterio` | `mejor_valor` (default: puntaje del comparador), `menor_precio`, `mayor_cobertura` |

```json
{"criterio": "mejor_valor"}
```

### `guardar_comparacion`

Genera un link corto, del tipo `https://queplan.cl/url/<código>`, que restaura el perfil (edad, sexo, cargas y región), el orden, la moneda, los filtros, el precio y los seguros en comparación. Crea un registro en el sitio, así que no es de solo lectura. `restaura` y `noRestaura` dicen qué viaja; la previsión no viaja. Avísale a la persona qué contiene el link antes de que lo comparta.

```json
{}
```

### `preparar_solicitud`

Abre la ficha y resalta el botón real de contratación para que lo presione la persona. No envía nada. Si el seguro no se contrata en línea, responde error: sugiere otra opción o el contacto.

```json
{"id": 4519}
```

Después dile a la persona: «Revisa la ficha y, si estás de acuerdo, presiona tú el botón resaltado».

## Planes de isapre

Ruta: `/Comparar/Isapres`. Los planes se identifican por `codigoplan` y se cotizan en UF.

### `isapre_estado` (solo lectura)

Devuelve el perfil (`cotizantes` con edad y sexo, `cargas`, `region` e `ingresoIngresado`, sin el monto de la renta), los filtros, el orden, la moneda, el resultado (`total`, `pagina`, `paginas`, `precioMinimoUF` y `precioMaximoUF`) y los `avisos`.

```json
{}
```

### `isapre_setear_perfil`

Define el perfil y vuelve a cotizar. Es un parche. Al cambiar el perfil se recalcula el rango de precio y se reemplaza el filtro de precio, igual que en la página.

| Parámetro | Tipo | Notas |
|---|---|---|
| `edad`, `sexo` | entero 18–120; `"Masculino"` o `"Femenino"` | Cotizante titular. |
| `ingreso` | número, CLP | Opcional. Define el 7% legal y el rango de precio. Si la persona no lo dio o es independiente, omítelo y filtra por precio. |
| `pareja` | `{edad, sexo, ingreso?}` | Pareja que cotiza en el mismo plan. |
| `quitarPareja` | booleano | Con `true` queda un solo cotizante. No lo combines con `pareja`. |
| `cargas` | arreglo de hasta 10 `{edad, sexo?}` | `[]` deja el perfil sin cargas. |
| `region` | texto | Nombre o número de la región, por ejemplo `"Metropolitana"` o `"5"`. |

```json
{"edad": 35, "sexo": "Femenino", "region": "Metropolitana", "ingreso": 2000000, "cargas": []}
```

```json
{"pareja": {"edad": 36, "sexo": "Masculino"}, "cargas": [{"edad": 5}, {"edad": 8}]}
```

Si trae `avisos`, por ejemplo excedentes o un rango sin planes, transmíteselos a la persona.

### `isapre_aplicar_filtros`

Solo cambia los filtros que envíes. Las listas reemplazan la selección.

| Parámetro | Tipo | Notas |
|---|---|---|
| `precioMin`, `precioMax` | número, CLP | Precio mensual. |
| `modalidades` | arreglo con `libreEleccion`, `prestadorPreferente` o `planCerrado` | Libre elección: misma cobertura en todos lados. Preferente: mejor cobertura en ciertos prestadores. Cerrado: cobertura solo donde se indica. |
| `clinica` | texto | Nombre o RUT de la clínica. La cobertura y el puntaje pasan a medirse en esa clínica. No se combina con `libreEleccion`. Si hay varias candidatas, repite con el RUT que te ofrece la respuesta. |
| `quitarClinica` | booleano | Quita la clínica elegida. |
| `coberturaHospitalaria`, `coberturaAmbulatoria` | 0, 60, 70, 80, 90 o 100 | Mínimo de cobertura en %. 0 quita el mínimo. |
| `isapres` | arreglo de 1 a 20 textos | Nombres, como `"Colmena"`, o códigos. |

```json
{"clinica": "Clínica Alemana", "modalidades": ["prestadorPreferente", "planCerrado"], "coberturaHospitalaria": 90}
```

### `isapre_obtener_opciones_filtro` (solo lectura)

Devuelve las opciones de cada filtro con cuántos planes tiene cada una.

| Parámetro | Valores |
|---|---|
| `filtro` | `isapres`, `modalidades`, `coberturas`, `clinicas`, `regiones` o `todos` (default) |
| `query` | Búsqueda de clínicas, sin mayúsculas ni tildes. |
| `limite` | 1–100 clínicas. El default es 30. |

```json
{"filtro": "clinicas", "query": "alemana", "limite": 5}
```

### `isapre_ordenar`

| Parámetro | Valores |
|---|---|
| `order` (obligatorio) | `recommended` (mejor puntaje), `lowerPrice` (menor precio), `higherPrice` (mayor precio) |

```json
{"order": "recommended"}
```

### `isapre_cambiar_moneda`

Cambia solo la moneda en pantalla; los planes se cotizan siempre en UF.

```json
{"currency": "CLP"}
```

### `isapre_listar_planes` (solo lectura)

Lista los planes de la página actual. Cada plan trae `codigoplan`, `nombre`, `isapre`, `modalidad`, `precioUF`, `precioCLP`, `puntaje`, `coberturaHospitalaria`, `coberturaAmbulatoria` y `url`. La cobertura se mide en la clínica elegida o, si no hay, en libre elección. `precioCLP` es `precioUF` convertido con el valor de la UF que usa la página. Usa una sola unidad en toda la respuesta.

```json
{"limite": 10}
```

### `isapre_ver_mas`

| Parámetro | Notas |
|---|---|
| `pagina` | 1–500. El default es la siguiente. |

```json
{"pagina": 2}
```

### `isapre_obtener_detalle_plan`

Abre la ficha del plan en pantalla. Devuelve el precio mensual para el perfil, por persona (plan más GES), las clínicas preferentes con su cobertura, los beneficios y el puntaje por categoría. Acepta cualquier código de plan, aunque no esté en la página.

| Parámetro | Tipo | Notas |
|---|---|---|
| `codigoplan` (obligatorio) | texto | Por ejemplo `"E70500725"`. |
| `pestana` | `resumen`, `precio`, `prestaciones`, `puntaje` o `contrato` | Pestaña que se abre. El default es `resumen`. |
| `buscarPrestacion` | texto, 3–60 | Cobertura y tope de las prestaciones que contienen ese texto, por ejemplo `"parto"`, `"consulta"` o `"resonancia"`. |

```json
{"codigoplan": "E70500725", "pestana": "prestaciones", "buscarPrestacion": "parto"}
```

Si el texto dice que el plan ya no se comercializa, no lo recomiendes.

### `isapre_comparar`

Abre lado a lado 2 o 3 planes de la página actual. Devuelve el precio, el puntaje, la modalidad, las coberturas hospitalaria y ambulatoria y el tope anual. Reemplaza la comparación anterior.

```json
{"codigos": ["E70500725", "NP32610080"]}
```

Los códigos son ilustrativos: usa los de `isapre_listar_planes`.

### `isapre_recomendar`

Devuelve un top 3 con razones y lo abre lado a lado. Deja el comparador ordenado por el criterio, desde la página 1. Con una clínica elegida, el puntaje se mide en esa clínica.

| Parámetro | Valores |
|---|---|
| `criterio` | `mejor_puntaje` (default) o `menor_precio` |

```json
{"criterio": "mejor_puntaje"}
```

### `isapre_guardar_comparacion`

Genera un link corto que restaura el perfil (edad y sexo de cotizantes y cargas, y región), el orden, la moneda, las modalidades, la clínica, los mínimos de cobertura, las isapres y los planes en comparación. No viajan la renta ni el rango de precio: quien abra el link ve el rango por defecto hasta ingresar su renta. Díselo a la persona.

```json
{}
```

### `isapre_preparar_solicitud`

Abre la pestaña de solicitud del plan y resalta el formulario para que la persona ingrese sus datos y lo envíe. Nunca lo llenes ni lo envíes: al enviarlo se crea una solicitud y la contacta un ejecutivo de la isapre. Si el plan ya no se comercializa, responde error.

```json
{"codigoplan": "E70500725"}
```
