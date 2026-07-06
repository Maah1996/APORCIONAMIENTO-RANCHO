# Control de Aporcionamiento Diario — V División de Ejército

Aplicación web (HTML/CSS/JS vanilla + Firebase Realtime Database) para el control diario
de personal que asiste a rancho (almuerzo), tanto **interno** de la unidad como **externo**
en comisión de servicio. Misma arquitectura que el Sistema MAAH, lista para GitHub Pages.

## Etapa 1 (implementada)
- Una sola pantalla con selector **PERSONAL INTERNO / PERSONAL EXTERNO AL CG V**.
- **Interno**: cabecera (Unidad, Uni/Interna, Actividad, Fecha) + tabla de personal.
  Guarda/carga/elimina el parte por fecha. Precarga automática al recargar.
- **Externo**: cabecera (Unidad a Cargo, Actividad, Desde/Hasta, Total días automático)
  + tabla de personal. Selector para cargar registros existentes.
- Validación de **RUT chileno** (formato + dígito verificador) con marca visual.
- Totales automáticos: total personal, total con racho SÍ y **total por categoría**.
- Control de tamaño de letra (A− / A / A+), tipografía Times New Roman.
- Avisos con toast (sin `alert()`), confirmación al guardar.

## Firebase (ya conectado)

Este sistema usa **Firestore** del proyecto compartido `cheklist-procesos` (mismo proyecto
que el Checklist de Procesos y Riesgos), en 3 colecciones propias:

```
apor_interno/{key}   -> nómina interna por depto (key = depto normalizado)
apor_externo/{key}   -> registro externo por unidad (key = unidad normalizada)
apor_partes/{fecha}  -> marcas de rancho SÍ/NO del Parte Diario, por fecha
```

`localStorage` sigue siendo la fuente de verdad para lectura (la app funciona sin conexión);
Firestore es un espejo de respaldo — cada guardado escribe también a la nube, pero nada se
lee desde ahí todavía (queda para una futura sincronización entre equipos).

Las reglas de Firestore exigen `request.auth != null`. Como esta app no tiene login real de
Firebase, `index.html` abre una **sesión anónima** en segundo plano (`onAuthStateChanged` +
`signInAnonymously`) para cumplir la regla — no cambia nada del acceso por clave de depto.

⚠️ La API key está restringida por dominio (`https://maah1996.github.io/*`), así que la
sincronización a Firestore solo funciona una vez publicado en GitHub Pages; en `localhost`
mostrará "Auth anónima falló" en la consola — es esperado, no afecta el guardado local.

## Publicar en GitHub Pages
1. Crea un repositorio (ej. `Maah1996/APORCIONAMIENTO-RANCHO`).
2. Sube `index.html` y `README.md`.
3. **Settings → Pages → Branch: `main` / root → Save**.
4. Accede en `https://maah1996.github.io/APORCIONAMIENTO-RANCHO/`.

> Tras cada cambio recuerda **Ctrl + Shift + R** para saltar la caché del navegador.

## Modelo de datos (Realtime Database)
```
aporcionamiento/
  interno/{fecha}/            -> { unidad, uniInterna, actividad, fecha, personal:[{categoria,grado,nombre,rut,racho}] }
  externo/{UNIDAD_desde_hasta}/-> { unidadACargo, actividad, desde, hasta, totalDias, personal:[...] }
```

## Etapa 2 (prevista, no desarrollada)
Resumen mensual, gráficos de asistencia, comparación interno vs externo, filtros por
grado/categoría, exportación Excel/PDF e integración de login con el Sistema MAAH.
