# Plan

El agente trabaja una fase cada vez y marca las casillas al cerrarla. Al cerrar cada fase: ejecutar todas las pruebas, confirmar que no hay errores de consola/avisos relevantes, y actualizar este `PLAN.md` en el **mismo commit** (solo commit local; nunca push).

## Decisiones fijadas (aplican a todas las fases)

- **Fórmulas** (sistema francés):
  - `n = años * 12` (meses totales).
  - `i = (interésAnual / 100) / 12` (tipo mensual).
  - Si `i === 0`: `cuota = principal / n`.
  - Si `i > 0`: `cuota = principal * (i * (1 + i) ** n) / ((1 + i) ** n - 1)`.
  - `totalPagado = cuota * n`; `interesesTotales = totalPagado - principal`.
- **Formato de salida**: moneda EUR con 2 decimales en locale `es-ES` (p. ej. `Intl.NumberFormat('es-ES', { style: 'currency', currency: 'EUR' })`). La regla de redondeo debe ser consistente con los casos de `docs/project-info.md` (se fija y prueba en Fase 1).
- **Entradas válidas** (según spec): importe `> 0` (decimales permitidos), interés anual `>= 0` (decimales permitidos), plazo en años **entero** `> 0`. Rechazar vacíos, no numéricos, negativos y plazo decimal.
- **Parseo**: definir comportamiento explícito para coma/punto decimal y entradas con separadores; no asumir solo `Number()` sobre texto ambiguo. Cubrir con pruebas los formatos soportados.
- **Testabilidad en archivo único**: mantener la lógica de negocio (cálculo, validación, formateo/parseo) separada del DOM en funciones puras/reutilizables dentro de `index.html`. Respetar el límite de **500 líneas** por archivo (`AGENTS.md`); si se acerca, reorganizar por responsabilidad antes de seguir.
- **E2E estables**: usar selectores robustos desde el inicio (`data-testid` y/o roles/labels accesibles). Evitar acoplar pruebas a clases de estilo.
- **Ejecución E2E**: Playwright debe correr contra una URL servida (servidor estático efímero), no depender de supuestos frágiles de `file://`. La dependencia/harness de Playwright es *tooling de test* (justificarla antes de añadirla, según `AGENTS.md`).

## Fase 0 · Esqueleto y harness E2E

**Objetivo:** `index.html` mínimo visible y un harness de Playwright en `e2e/` capaz de ejecutar pruebas contra la app servida.

**Tareas:**
- [ ] Crear `index.html` con estructura semántica inicial: nombre/título visible de la app, formulario con campos (importe, interés anual, plazo en años), botón "Calcular" y contenedor de resultados/errores. Incluir `data-testid`/atributos accesibles estables.
- [ ] Configurar tooling de pruebas E2E en `e2e/` (dependencia Playwright + config) y un modo de servir `index.html` para las pruebas. Documentar el comando para ejecutar la suite.
- [ ] Añadir prueba E2E mínima: la app carga, no hay errores de consola fatales y se muestra el nombre de la app.

**Pruebas:**
- E2E: carga de la página y presencia del nombre/título de la app.

**Hecho cuando:** `index.html` se abre sin errores, se ve el nombre, el harness E2E corre en limpio y la prueba pasa. `PLAN.md` actualizado en el mismo commit.

## Fase 1 · Lógica de cálculo pura y formato

**Objetivo:** implementar y probar la lógica de negocio (cálculo + formateo) separada del DOM, cubriendo interés positivo y 0 %.

**Tareas:**
- [ ] Implementar funciones puras para: conversión anual→mensual, cuota (incl. caso `i === 0`), total pagado e intereses totales.
- [ ] Implementar/definir formateo monetario `es-ES` (EUR, 2 decimales) y fijar la regla de redondeo coherente con los casos de referencia.
- [ ] Añadir pruebas automatizadas de la lógica (rápidas y deterministas) para los casos de `docs/project-info.md`:
  - Caso 1: 200.000 €, 3 %, 30 años → cuota ≈ 843,21 €, total ≈ 303.555,20 €, intereses ≈ 103.555,20 €.
  - Caso 2: 120.000 €, 0 %, 10 años → cuota = 1.000,00 €, total = 120.000,00 €, intereses = 0,00 €.
- [ ] Definir cómo se ejecutan estas pruebas junto a la suite (integradas en `e2e/` o comando explícito documentado).

**Pruebas:**
- Casos 1 y 2 de la spec, pasando con la regla de redondeo/formato fijada.

**Hecho cuando:** la lógica produce los resultados esperados, las pruebas pasan y `PLAN.md` se actualiza en el mismo commit.

## Fase 2 · UI: formulario, cálculo y resultados

**Objetivo:** conectar la interfaz al cálculo y mostrar resultados correctos en pantalla.

**Tareas:**
- [ ] Conectar el botón "Calcular" (y/o submit sin recarga) para leer entradas, invocar la lógica y renderizar cuota mensual, total pagado e intereses totales formateados en EUR.
- [ ] Asegurar que el parseo de entradas usado por la UI es coherente con la decisión de parseo (coma/punto según lo definido) y no rompe los casos de referencia.
- [ ] Añadir/ajustar pruebas E2E que verifiquen en la UI los Casos 1 y 2 (interacción real con el formulario).

**Pruebas:**
- E2E Caso 1 y Caso 2 desde la interfaz.
- Suite completa en verde.

**Hecho cuando:** al calcular con los datos de referencia se muestran los 3 valores correctos, todas las pruebas pasan y `PLAN.md` se actualiza en el mismo commit.

## Fase 3 · Validación y gestión de errores

**Objetivo:** impedir entradas no válidas y mostrar errores claros sin realizar el cálculo.

**Tareas:**
- [ ] Validar antes de calcular: campos requeridos; importe `> 0`; interés `>= 0`; plazo entero `> 0`. Rechazar negativos, vacíos, no numéricos y años decimales.
- [ ] Mostrar mensajes de error descriptivos en la interfaz (visibles y, cuando sea razonable, accesibles). Al corregir y recalcular, limpiar errores y actualizar/limpiar resultados según corresponda.
- [ ] Añadir pruebas E2E para entradas no válidas: importe negativo (Caso 3), interés negativo, vacíos/no numéricos, plazo decimal y plazo `0`. Verificar que no se muestran resultados calculados.

**Pruebas:**
- E2E Caso 3 y casos de borde de validación.
- Recalculo tras corregir un error.
- Suite completa en verde.

**Hecho cuando:** las entradas inválidas se bloquean con avisos claros, no hay cálculo erróneo, todas las pruebas pasan y `PLAN.md` se actualiza en el mismo commit.

## Fase 4 · Estilos CSS y diseño responsive

**Objetivo:** diseño limpio y adaptable a móvil/escritorio sin romper funcionalidad ni pruebas.

**Tareas:**
- [ ] Aplicar estilos CSS internos en `index.html` (tarjeta/formulario, campos, botón, resultados, errores) con diseño usable en móvil y escritorio.
- [ ] Mantener/ajustar selectores estables (`data-testid`/roles) y accesibilidad básica (labels, visibilidad de errores, contraste razonable).
- [ ] Verificar que no hay regresiones: ejecutar toda la suite E2E y revisar manualmente la vista responsive.

**Pruebas:**
- Suite E2E completa en verde tras los cambios visuales.
- Comprobación manual responsive (móvil/escritorio) sin overflow ni pérdida de funcionalidad.

**Hecho cuando:** la UI es clara y responsive, no hay regresiones, todas las pruebas pasan y `PLAN.md` se actualiza en el mismo commit.
