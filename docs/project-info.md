# Información del proyecto

## Qué es
Una aplicación web sencilla en un único archivo (`index.html`) que calcula la cuota mensual, el importe total pagado y los intereses totales de una hipoteca mediante el sistema de amortización francés.

## Para quién
Personas que están evaluando o comparando opciones de hipoteca y necesitan calcular rápidamente las cuotas y los intereses de su préstamo.

## Funciones
- Formulario para introducir:
  - Importe del préstamo (€, permite decimales).
  - Tipo de interés anual (%, permite decimales).
  - Plazo del préstamo (años enteros).
- Botón "Calcular" para procesar y mostrar los resultados.
- Cálculo según el sistema de amortización francés:
  - Cuota mensual.
  - Importe total pagado al finalizar el préstamo.
  - Intereses totales a pagar.
- Soporte correcto para tipo de interés del 0 % (cuota mensual = importe / total meses).
- Validación de entradas:
  - Bloqueo/aviso ante valores negativos, vacíos o no numéricos.
  - Plazo restringido a números enteros positivos.
  - Avisos visuales de error claros en pantalla.

## Fuera de alcance
- Tabla / desglose mes a mes de amortización.
- Tipos de interés variables o mixtos.
- Comisiones, gastos notariales, impuestos o seguros.
- Cuentas de usuario o guardado de datos en servidor/base de datos.
- Uso de librerías, frameworks o CSS externos.

## Tecnología
- HTML5, CSS3 y JavaScript vanilla en un único archivo `index.html`.
- Se ejecuta directamente en cualquier navegador web moderno.

## Cómo se arrancará
- Abrir directamente el archivo `index.html` en el navegador o ejecutar un servidor de archivos estáticos local (por ejemplo `npx serve` o `python3 -m http.server`).

## Datos y claves
- **Datos de entrada:** Importe (€), Interés anual (%), Plazo en años (enteros).
- **Claves:** No requiere claves de API ni variables de entorno.

## Cómo se prueba
- **Caso 1 (Interés positivo estándar):**
  - Entrada: 200.000 €, 3 % anual, 30 años.
  - Resultado: Cuota mensual ≈ 843,21 €, Importe total ≈ 303.555,20 €, Intereses totales ≈ 103.555,20 €.
- **Caso 2 (Interés al 0 %):**
  - Entrada: 120.000 €, 0 % anual, 10 años.
  - Resultado: Cuota mensual = 1.000,00 €, Importe total = 120.000,00 €, Intereses totales = 0,00 €.
- **Caso 3 (Entradas no válidas / negativas):**
  - Entrada: Importe -50.000 € o Interés -1 %.
  - Resultado: Muestra mensaje de error y no realiza el cálculo.
- **Pruebas e2e:** Pruebas automatizadas con Playwright en la carpeta `e2e/`.
