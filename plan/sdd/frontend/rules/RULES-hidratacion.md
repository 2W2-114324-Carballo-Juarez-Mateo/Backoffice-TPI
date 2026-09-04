# RULES — Hidratación (SSR)

1. El HTML inicial lo genera el **servidor** (Angular SSR); el navegador **hidrata** después. Server y client deben renderizar **el mismo resultado**.
2. Los **datos iniciales vienen del BFF** (misma fuente en server y client). No dejes que el client pida datos distintos a los que el servidor usó para renderizar.
3. **Evita render no-determinista:** fechas, `Math.random()`, ids generados en el template, contenido dependiente del timezone del cliente. Todo eso causa *hydration mismatch*.
4. Si hay mismatch, corregí la fuente (server/client), no "parches" el template.
5. No uses APIs de navegador (`window`, `document`, `localStorage`) en el render del servidor sin guardas (`isPlatformServer`/`isPlatformBrowser`).