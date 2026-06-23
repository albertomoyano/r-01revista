::: {.appendix}
## Pruebas exhaustivas con referencias bibliográficas

Artículo de prueba exhaustivo para verificar que el parser de gbpublisher reconoce **TODOS** los formatos de cita de Pandoc documentados en la sección 8.20 *Citation syntax del Pandoc User's Guide* (edición 2026-03-17), y que el filtro Lua preserva el modo correcto en el JATS canónico. Cada sección describe la sintaxis, el comportamiento esperado en el rendering final, y la representación esperada en JATS canónico (citation-mode y subelementos de element-citation). Cuando el caso es comportamiento inferido (no documentado explícitamente en la sintaxis de citas pero esperable desde reglas generales de Markdown), se indica como tal.

## Formato 1: cita básica — `[@key]`

Forma estándar entre paréntesis. Renderiza "(Autor, Año)" según el CSL. En JATS, `citation-mode="parenthetical"`, sin `<prefix>`, sin `<suffix>`, sin `<page-range>`.

La transformación digital impacta en múltiples dimensiones organizacionales [@2961-KACZAN2016]. El factor regional ha sido caracterizado en términos similares [@3036-ORBUCH2020].

## Formato 2: supresión de autor — `[-@key]`

Documentado textualmente en Pandoc: "A minus sign (`-`) before the `@` will suppress mention of the author in the citation." Se usa cuando el autor ya se nombró en el flujo del texto. Renderiza solo "(Año)". En JATS, `citation-mode="suppress-author"`.

Como señala Kaczan [-@2961-KACZAN2016], el fenómeno no debe confundirse con la simple digitalización de procesos. Pineau [-@3041-PINEAU1999] aborda la misma distinción en un contexto histórico.

## Formato 3: cita con localizador de página — `[@key, p. N]` / `[@key, pp. N-M]`

Página única con `p.`, rango con `pp.`. El localizador debe aparecer en JATS como `<page-range>` o atributo equivalente del `<element-citation>`. Render esperado: "(Autor, Año, p. N)".

El fenómeno ha sido estudiado en profundidad [@2961-KACZAN2016, p. 5]. También se han analizado los antecedentes históricos [@3041-PINEAU1999, pp. 55-60].

## Formato 4: supresión de autor con localizador — `[-@key, p. N]`

Combina los ejes 2 y 3. Render esperado: "(Año, p. N)". En JATS, `citation-mode="suppress-author"` + `<page-range>`.

Orbuch [-@3036-ORBUCH2020, p. 235] clasifica los desafíos en tres categorías. Kaczan [-@2961-KACZAN2016, pp. 225-230] analiza el impacto sectorial.

## Formato 5: prefijo textual — `[ver @key]`

Texto introductorio dentro del bracket, antes del `@`. La doc oficial muestra el ejemplo canónico `[see @doe99, pp. 33-35 and *passim*; @smith04, chap. 1]`, donde "see" es el prefijo del primer ítem. Render esperado: "(ver Autor, Año)". En JATS, el prefijo va como `<prefix>` dentro del `<element-citation>`. El parser tiene que encontrar el `@` aunque no sea el primer carácter del bracket.

Para un análisis detallado del contexto regional [ver @3036-ORBUCH2020]. Para el marco histórico [cf. @3041-PINEAU1999].

## Formato 6: múltiples citas en un bloque — `[@key1; @key2]`

Documentado textualmente: "Normal citations should be included in square brackets, with semicolons separating distinct items." Render esperado: "(Autor1, Año1; Autor2, Año2)". En JATS, múltiples `<element-citation>` agrupados.

Diversos autores han abordado la cuestión desde perspectivas complementarias [@2961-KACZAN2016; @3041-PINEAU1999]. La convergencia se replica en el plano sectorial [@3036-ORBUCH2020; @2961-KACZAN2016].

## Formato 7: múltiples con supresión parcial — `[@key1; -@key2]`

Cada cita del bloque mantiene su propio modo independientemente de las demás. El parser debe procesar cita por cita, no aplicar el modo en bloque.

Kaczan y Pineau [@2961-KACZAN2016; -@3041-PINEAU1999] coinciden en la importancia del marco histórico. El segundo autor ya fue mencionado antes.

## Formato 8: prefijo textual con múltiples citas — `[ver @key1; @key2]`

El prefijo aplica solo a la primera cita; las restantes son citas paralelas. En JATS, `<prefix>` solo en el primer `<element-citation>` del grupo.

Las estadísticas disponibles [ver @3036-ORBUCH2020; @3041-PINEAU1999] ofrecen un marco comparativo útil.

## Formato 9: combinaciones mixtas

Caso de stress: prefijo + múltiples + supresión parcial + localizador. Pone a prueba la independencia de procesamiento de cada cita dentro del bracket.

La literatura reciente [@2961-KACZAN2016; @3036-ORBUCH2020; -@3041-PINEAU1999] muestra convergencia en los hallazgos principales. Orbuch [-@3036-ORBUCH2020, p. 228] anticipa varios de estos resultados desde su campo específico.

## Formato 10: cita narrativa sin brackets — `@key`

CRÍTICO. Documentado textualmente: "You can also write an author-in-text citation, by omitting the square brackets: `@smith04 says blah.`" Es el segundo modo sintáctico primario de Pandoc, distinto del cite-in-brackets. Render esperado: "Autor (Año)" — el autor queda fuera del paréntesis. En JATS, `citation-mode="narrative"` (o `inline-citation` según perfil). Si el parser solo busca dentro de `[...]`, este caso se le escapa por completo y cae al modo por defecto del XSL, saliendo erróneamente como "(Autor, Año)".

Como sostiene @2961-KACZAN2016, la dimensión organizacional es central en el análisis. @3041-PINEAU1999 había anticipado esta línea desde una perspectiva histórica. Más recientemente, @3036-ORBUCH2020 amplió el marco a otros sectores.

### Subcaso 10b: narrativa con localizador

La doc muestra textualmente la forma `@smith04 [p. 33] says blah.` para combinar narrativa con localizador. Render esperado: "Autor (Año, p. N)".

@2961-KACZAN2016 [p. 33] demuestra el punto con un caso paradigmático. @3036-ORBUCH2020 [pp. 12-18] dedica un capítulo completo a la cuestión.

## Formato 11: localizadores no-página (lista completa del locale)

Pandoc detecta locator terms a partir del **locale CSL activo**, no de una lista fija. La doc oficial lista los términos del locale `en-US`, en singular y plural, abreviados y completos: `book`/`bk.`/`bks.`; `chapter`/`chap.`/`chaps.`; `column`/`col.`/`cols.`; `figure`/`fig.`/`figs.`; `folio`/`fol.`/`fols.`; `number`/`no.`/`nos.`; `line`/`l.`/`ll.`; `note`/`n.`/`nn.`; `opus`/`op.`/`opp.`; `page`/`p.`/`pp.`; `paragraph`/`para.`/`paras.`; `part`/`pt.`/`pts.`; `section`/`sec.`/`secs.`; `sub verbo`/`s.v.`/`s.vv.`; `verse`/`v.`/`vv.`; `volume`/`vol.`/`vols.`; y los símbolos `¶`/`¶¶` y `§`/`§§`. Cada uno produce un `<element-citation>` con la subestructura JATS apropiada (no siempre `<page-range>` — puede ser `<chapter-title>`, atributos `content-type`, etc.).

**Punto a verificar con mini-test antes de comprometer código**: si el CSL en uso es español, no está documentado en la sección de sintaxis si Pandoc detecta locator terms del locale del CSL activo (`cap.`/`caps.`, `pág.`/`págs.`, `vol.`/`vols.`) o si solo reconoce los de `en-US`. La doc remite a "the locator terms defined in the CSL locale files" pero no especifica el comportamiento de fallback.

El argumento central se desarrolla en detalle [@3036-ORBUCH2020, chap. 3]. La discusión metodológica aparece en otro lugar [@3041-PINEAU1999, sec. 2.1]. Las series estadísticas completas están en el primer tomo [@3036-ORBUCH2020, vol. 1, p. 45]. Los datos primarios aparecen en una nota al pie específica [@2961-KACZAN2016, n. 12]. El esquema general se presenta tempranamente [@3036-ORBUCH2020, pt. 1]. La definición operativa aparece en una sección específica [@3041-PINEAU1999, sec. 4]. El número de figura referido es claro [@3036-ORBUCH2020, fig. 7]. La nota marginal complementa la lectura [@2961-KACZAN2016, ¶ 5].

## Formato 12: prefijo combinado con supresión — `[ver -@key]`

Combina los ejes del Formato 5 y el Formato 2 en un caso que ningún ejemplo previo cubre: el `-` está después del prefijo, no al inicio del bracket. No está documentado explícitamente como caso aparte, pero se sigue de la composicionalidad: la doc define prefijo y supresión como dos modificadores independientes que pueden coexistir. El parser tiene que reconocer el `-` inmediatamente antes del `@`, no asumir que está pegado al `[`.

Pineau ya había trabajado esta línea en otro registro [ver -@3041-PINEAU1999]. El contraste con la lectura más reciente es ilustrativo [cf. -@3036-ORBUCH2020].

## Formato 13: sufijo no-localizador y llaves alrededor del locator

Tres subcasos verificados en la doc oficial. Los ejemplos canónicos son:

- `[@smith{ii, A, D-Z}, with a suffix]`
- `[@smith, {pp. iv, vi-xi, (xv)-(xvii)} with suffix here]`
- `[@smith{}, 99 years later]`

### Subcaso 13a: sufijo de texto libre

Texto después de la cita que NO es un locator term ni dígitos puros — Pandoc lo manda a `<suffix>` en JATS. Diferente del prefijo del Formato 5.

El argumento se sostiene en términos generales [@2961-KACZAN2016, énfasis añadido]. La interpretación es polémica [@3041-PINEAU1999, pero ver la crítica reciente].

### Subcaso 13b: localizador entre llaves

Las llaves `{...}` son el escape hatch oficial de Pandoc para forzar el parsing del locator cuando la heurística falla, o para usar un locator con caracteres no estándar (paginación romana, rangos con paréntesis, listas de partes alfabéticas).

La referencia clásica al libro IV es ineludible [@3041-PINEAU1999{libro IV, cap. 3}]. La paginación romana también necesita llaves [@3036-ORBUCH2020{p. xiv}]. El listado fragmentario es claro [@3036-ORBUCH2020{pp. iv, vi-xi, (xv)-(xvii)}].

### Subcaso 13c: llaves vacías + sufijo

`{}` vacío fuerza "no hay locator" y todo lo que sigue a la coma va como suffix puro, sin que Pandoc intente leer dígitos iniciales como página.

La obra completa es relevante [@3036-ORBUCH2020{}, sin paginación específica]. La cita se mantiene aunque no haya página [@2961-KACZAN2016{}, passim].

## Formato 14: cita escapada — caso negativo (comportamiento inferido)

El `\` antes del `@` o del `[` debe impedir que Pandoc procese el bloque como cita. **No está documentado explícitamente en la sección 8.20 (Citation syntax)** — se sigue del comportamiento general de escaping de Markdown en Pandoc, pero conviene marcarlo así para evitar asumirlo como garantía formal. Si el parser de gbpublisher no lo respeta, va a corromper texto donde `@` aparece por otra razón (handles de redes sociales, direcciones de email mencionadas, sintaxis literal de Pandoc citada como ejemplo en un texto sobre el propio Pandoc).

En este párrafo, la secuencia \@2961-KACZAN2016 NO debe convertirse en cita: tiene que aparecer literalmente como `@2961-KACZAN2016` en el render final. Lo mismo aplica si todo el bracket está escapado: \[@3041-PINEAU1999\] debe aparecer literal, no como cita procesada.

## Formato 15: llaves alrededor de la KEY entera — `[@{key especial}]`

Documentado textualmente: "Unless a citation key starts with a letter, digit, or `_`, and contains only alphanumerics and single internal punctuation characters (`:.#$%&-+?<>~/`), it must be surrounded by curly braces". La doc da el ejemplo canónico: `[@{https://example.com/bib?name=foobar&date=2000}, p. 33]`.

Caracteres válidos en una key SIN llaves: empieza con letra, dígito o `_`; contiene solo alfanuméricos y los signos `:.#$%&-+?<>~/`. Todo lo demás exige llaves alrededor de la key. El carácter `|` NO está en la lista permitida: una key con `|` exige llaves obligatorias.

Las tres keys reales del proyecto (`2961-KACZAN2016`, `3041-PINEAU1999`, `3036-ORBUCH2020`) cumplen las reglas básicas y NO necesitan llaves. Las pruebas siguientes usan keys hipotéticas para ejercitar el parser, dado que el parser tiene que tolerar este caso por compatibilidad con bibliografías importadas (entradas con DOIs como key, URLs como identificador, keys heredadas con caracteres exóticos).

La fuente en línea se cita directamente con su URL como identificador [@{https://www.pagina12.com.ar/}, p. 33]. La entrada con pipe en la key heredada de un sistema externo se cita con llaves obligatorias [@{2961-KACZAN2016|variante-extendida}]. El DOI usado como key tampoco puede ir suelto si contiene caracteres fuera del set permitido [@{10.5821/ace.v4i12.2483|2016-235}].

## Formato 16: Markdown inline dentro de prefix y suffix

La doc oficial muestra el ejemplo `[see @doe99, pp. 33-35 and *passim*; @smith04, chap. 1]`, donde `*passim*` es énfasis Markdown DENTRO del suffix. Esto implica que los prefijos y sufijos NO son texto plano: pueden contener `*énfasis*`, `**negrita**`, `` `código en línea` ``, y en principio cualquier inline Markdown válido.

Implicancia para el pipeline: si el parser de gbpublisher trata el contenido de los corchetes como texto opaco antes de pasárselo a Pandoc, va a perder el formateo. Y si el filtro Lua post-procesa las citas, tiene que respetar el inline ya convertido. En JATS canónico, los `<prefix>` y `<suffix>` pueden contener `<italic>`, `<bold>`, `<monospace>` según los inline detectados.

La interpretación se desarrolla en detalle [ver especialmente @2961-KACZAN2016, pp. 33-35 y *passim*]. El argumento se refuerza con la lectura clásica [**comparar con** @3041-PINEAU1999, cap. 3, *muy especialmente*]. La nota técnica usa formato literal [@3036-ORBUCH2020, ver `appendix-B.tex` para los datos crudos]. El énfasis del autor se mantiene en el sufijo [@2961-KACZAN2016, *cursivas del original*].

## Formato 17: locator implícito sin término — `[@key, N]`

Documentado textualmente: "If no locator term is used, 'page' is assumed." Cuando aparecen dígitos después de la coma sin un locator term que los anteceda, Pandoc los interpreta como página. Render esperado: idéntico a `[@key, p. N]`. En JATS, debe producir el mismo `<page-range>` que la forma explícita.

Importancia práctica: muchos autores escriben las citas en estilo informal, omitiendo `p.`. El parser tiene que producir el mismo JATS canónico en ambas formas, o el rendering va a salir inconsistente entre artículos de autores con estilos distintos.

La página específica se referencia directamente [@2961-KACZAN2016, 33]. El rango también admite la forma corta [@3041-PINEAU1999, 55-60]. La combinación con supresión también [-@3036-ORBUCH2020, 235].

## Formato 18: reglas de terminación de key — casos negativos

La doc enumera tres reglas de borde que el parser tiene que respetar para no devorarse puntuación que no corresponde al identificador bibliográfico:

1. **Punto final fuera de la key**: en `@Foo_bar.baz.`, la key es `Foo_bar.baz` — el punto final NO es puntuación interna, queda fuera. Esto es crítico para citas al final de oración.
2. **Puntuación interna repetida termina la key**: en `@Foo_bar--baz`, la key es `Foo_bar` — el doble guion corta la key (la doc dice "single internal punctuation characters").
3. **Comienzo inválido exige llaves**: si la key empieza con algo que no sea letra, dígito o `_`, hay que envolverla en llaves o el parser falla.

Las keys reales del proyecto usan solo guion único como puntuación interna y arrancan con dígito, por lo que están a salvo de las reglas 2 y 3. Pero la regla 1 sí las afecta: una cita al final de oración o seguida de coma tiene que terminar correctamente.

### Subcaso 18a: cita al final de oración

El punto debe quedar AFUERA de la key, no dentro. Render esperado: la key se cierra en `2016`/`1999`/`2020`, y el punto va como puntuación de la oración.

El planteo central lo desarrolla @2961-KACZAN2016. La línea histórica la traza @3041-PINEAU1999. La síntesis más reciente es @3036-ORBUCH2020.

### Subcaso 18b: cita seguida de signos de puntuación

Coma, punto y coma, dos puntos, signo de pregunta y signo de exclamación deben quedar afuera de la key.

El argumento de @2961-KACZAN2016, retomado por @3041-PINEAU1999, llega a @3036-ORBUCH2020. ¿Coincide @2961-KACZAN2016 con esta lectura? Definitivamente sí, según @3036-ORBUCH2020.

### Subcaso 18c: doble guion no aparece en keys reales (verificación negativa)

Caso hipotético que NO debe procesarse como una key real. Si un texto contiene `@2961-KACZAN--2016` (con doble guion), Pandoc cortaría la key en `@2961-KACZAN` y dejaría `--2016` como texto. Las keys reales del proyecto NO tienen doble guion, por lo que esto solo importa para tolerancia frente a errores tipográficos del autor.

En este párrafo, la secuencia @2961-KACZAN--2016 debería ser parseada como cita a `2961-KACZAN` (que NO existe en la bibliografía) seguida de texto literal `--2016`. El resultado esperado es un warning de citeproc por key inexistente, no un crash.


## Formato 19: narrativa con localizador en posición posterior

@2961-KACZAN2016 demuestra el punto con un caso paradigmático [p. 33].
@3036-ORBUCH2020 dedica un capítulo completo a la cuestión [pp. 12-18].

## Formato 20: cita al final de oración seguida de coma (narrativa)

Como señala @2961-KACZAN2016, el fenómeno es complejo.
Según @3041-PINEAU1999, la línea histórica es clara.

## Formato 21: prefijo con término que parece localizador

[cap. 3 de @2961-KACZAN2016]
[vol. 1 de @3036-ORBUCH2020, p. 45]

## Formato 22: sufijo que empieza con dígito pero no es localizador

[@2961-KACZAN2016, 2020 es un año clave para el estudio].
[@3041-PINEAU1999, 1999 vio la primera edición].

## Formato 23: cita dentro de footnote explícita

Diversos autores han abordado la cuestión.[^1]

[^1]: Estudios previos [@2961-KACZAN2016; @3041-PINEAU1999, p. 15] lo documentan. Ver también @3036-ORBUCH2020 [cap. 2] para una síntesis reciente.

## Formato 24: escaping parcial dentro de corchetes

En este párrafo, la secuencia [\@2961-KACZAN2016] NO debe convertirse en cita.
Tampoco [@3041-PINEAU1999\], porque el cierre está escapado.

## Resumen de cobertura

Si el pipeline procesa correctamente los 18 formatos de arriba (incluyendo todos los subcasos), cubre la totalidad de la sintaxis de citas de Pandoc documentada en la sección 8.20 del manual oficial, edición 2026-03-17.

:::
