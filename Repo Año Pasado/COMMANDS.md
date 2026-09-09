### 1. Generar Javadoc (solo en ramas releases)
```bash
mvn javadoc:javadoc
```
- Genera documentación automática (JavaDoc).
- Archivos generados en `docs/java_doc`.
- Maven modifica o crea archivos, haz un nuevo commit con esos cambios.

---

### 2. Revisar violaciones de reglas PMD
```bash
mvn pmd:pmd
```
- Analiza el código buscando malas prácticas y errores potenciales.
- Reporte detallado en `target/site/pmd.html`. Si hay errores se veran en este archivo.

---

### 3. Detectar código duplicado (CPD)
```bash
mvn pmd:cpd
```

- Busca fragmentos de código duplicado.
- Reporte en `target/site/cpd.html`. Si hay errores se veran en este archivo.

---

### 4. Ejecutar los tests unitarios
```bash
mvn test
```

- Ejecuta todas las pruebas unitarias.
- Resultado en consola y reportes en `target/surefire-reports/`.
- Corrige el código o tests hasta que todas las pruebas pasen correctamente.

---

### 5. Validaciones completas de estilo y reglas (verify)
```bash
mvn verify
```

- Corre todas las validaciones de PMD y otras configuradas.
- Resultados en consola y reportes en `target/site/`.

---

### 6. Generar reporte de estilo (Checkstyle)
```bash
mvn checkstyle:checkstyle
```
- Genera un reporte HTML con los errores de estilo.
- Archivo generado: `target/site/checkstyle.html`.
- Abrir en el navegador y corregir los archivos listados hasta que no haya errores.

---

## Resumen rápido del flujo
1. (Solo releases) Generar documentación → `mvn javadoc:javadoc`
2. Revisar PMD → `mvn pmd:pmd`
3. Revisar duplicaciones → `mvn pmd:cpd`
4. Ejecutar tests → `mvn test`
5. Validaciones completas → `mvn verify`
6. Reporte de estilo → `mvn checkstyle:checkstyle`

Corre todos estos comandos antes de hacer push.
