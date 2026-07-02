# marcasmonitor — Vigilancia de marcas del Boletín de Marcas (Panamá) - Versión de Prueba

Por: Osvaldo Quintero

Nota: Por ser versión de pruebas esta limitado a revisar solo 10 marcas del usuario. No se puede cambiar esa limitante.  Si desea la versión sin limites comuníquese con el autor.

Skill para Claude / Cowork que automatiza la **Comparación de marcas contra el boletín oficial de marcas de Panamá**

A partir de dos archivos —el **PDF del boletín que le suba el usuario** que debe ser el de marcas publicado por DIGERPI y un **Excel con las
marcas registradas** del titular— genera un **reporte Excel a color** que resalta las
nuevas solicitudes publicadas que podrían confundirse con las marcas propias, comparando
por **nombre, fonética e imagen del logo**. Después aplica una **revisión de riesgo de
confusión (likelihood-of-confusion) con IA** para reducir los falsos positivos.

> Es un cribado de vigilancia de gaceta para Panamá. **No** es una búsqueda de
> clearance/knockout general ni una opinión legal.

---

## ¿Qué problema resuelve?

DIGERPI publica periódicamente un boletín con **todas** las nuevas solicitudes de
registro de marca. El titular de una marca debe revisar cada boletín para detectar
signos que puedan confundirse con los suyos y, de ser el caso, presentar **oposición
dentro de un plazo**. Hacerlo a mano sobre cientos o miles de solicitudes por boletín es
lento y propenso a errores. Este pipeline lo automatiza.

## ¿Qué hace?

1. **Extrae** del PDF solo las solicitudes nuevas: nombre, número de expediente,
   clase(s) de Niza, solicitante y logo.
2. **Normaliza** el Excel de marcas propias al esquema que espera el comparador
   (autodetección de hoja y columnas).
3. **Compara** cada marca del boletín contra cada marca propia usando similitud de
   **nombre + fonética + imagen**, y escribe un Excel priorizado y a color, ordenado para
   que los pares de mayor riesgo (misma clase de Niza, puntaje alto) aparezcan primero.
4. **Revisión IA (opcional):** una pasada de likelihood-of-confusion que descarta falsos
   positivos mecánicos (puntajes altos por una palabra genérica compartida, un token
   corto común o una colisión fonética casual) y produce un informe curado.

## Salidas

| Archivo | Descripción |
|---|---|
| `<pdf>.json` | Registros crudos extraídos del boletín |
| `<pdf>_revision.xlsx` | Marcas del boletín con logos embebidos |
| `<propias>_adapted.xlsx` | Marcas propias normalizadas al esquema de 4 columnas |
| `marcascompare_resultado.xlsx` | **El reporte final** a color |
| `./images/*.png` | Logos extraídos del boletín |

**Código de color del resultado** (una fila por par similar, misma clase primero, luego
por puntaje descendente):

- 🔴 Rojo — puntaje ≥ 80% (revisar primero)
- 🟠 Naranja — 60–79%
- 🟡 Amarillo — 40–59%
- 🟢 Verde — < 40%

La columna **"Misma clase = SI"** marca la máxima prioridad legal.
**Filtrar, no ordenar:** los logos son imágenes flotantes; al ordenar se desalinean.

---

## Instalación y uso

### Como skill de Claude / Cowork

Instalado como skill, se activa automáticamente cuando el usuario aporta un PDF de gaceta + un Excel de marcas del usuario debidamente formateado (logo, nombre, clase Niza, descripción) y escribe /monitormarcas-trial en Claude Cowork de Antrhopic.  

## Notas

- **Versión de prueba (trial):** esta distribución procesa solo las **primeras 10** de
  las marcas propias por corrida. Es una condición de la licencia.
- Este proyecto es una herramienta de apoyo a la vigilancia, no sustituye el criterio del
  abogado. Confirme cualquier decisión de oposición con un profesional.

---

*Creado por Osvaldo. Vigilancia de marcas (Panamá).*
