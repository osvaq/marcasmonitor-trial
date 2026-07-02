# marcasmonitor — Vigilancia de marcas del Boletín de Marcas (Panamá) - Por Osvaldo Quintero

Skill para Claude / Cowork que automatiza la **Comparación de marcas contra el boletín oficial de marcas de Panamá**

A partir de dos archivos —el **PDF del boletín que le suba el usuario** que debe ser el de marcas publicado por DIGERPI y un **Excel con las
marcas registradas** del titular— genera un **reporte Excel a color** que resalta las
nuevas solicitudes publicadas que podrían confundirse con las marcas propias, comparando
por **nombre, fonética e imagen del logo**. Después aplica una **revisión de riesgo de
confusión (likelihood-of-confusion) con IA** para reducir los falsos positivos.

> Es un cribado de vigilancia de gaceta para Panamá/DIGERPI. **No** es una búsqueda de
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

Requiere **Python 3.10+** (probado en 3.12). Todo corre **offline** — sin API keys ni red.

```bash
pip install pymupdf openpyxl pillow rapidfuzz jellyfish numpy
```

Ejecutar el orquestador interactivo desde un directorio con permiso de escritura:

```bash
python marcasmonitor3.py
```

Pide dos rutas y corre todo el pipeline:

```
1. Ruta del PDF del boletin DIGERPI:           <ruta al .pdf>
2. Ruta del Excel con sus marcas registradas:  <ruta al .xlsx>
```

En el paso 2 ofrece modo de adaptación: `1 - AUTODETECCION` (por defecto) o
`2 - Configurar manualmente`. El umbral de comparación es fijo en **60%**.

### Como skill de Claude / Cowork

Instalado como skill, se activa automáticamente cuando el usuario quiere vigilar un
boletín/gaceta de marcas, "boletín de marcas", "vigilancia de
marcas", o aporta un PDF de gaceta + un Excel de marcas.

## Estructura del repositorio

```
SKILL.md                  Instrucciones del skill (orquestación)
README.md                 Manual del operador (secciones 1–7) + términos de licencia
LICENSE.TXT               Licencia
dependencies.md           Requisitos exactos por módulo
marcasmonitor3.py         Orquestador interactivo (punto de entrada)
marcasboletinfetch.py     Extracción del PDF del boletín
marcasboletinjoiner.py    Unión/armado de registros del boletín
marca_table_adapter.py    Normalización del Excel de marcas propias
marcascompare.py          Motor de comparación (nombre + fonética + imagen)
xlsx_safe.py              Carga segura de .xlsx con logos embebidos
```

Todos los `.py` deben quedar juntos en un mismo directorio plano (se importan como
hermanos), y `README.md` debe permanecer junto a los scripts.

## Notas

- **Versión de prueba (trial):** esta distribución procesa solo las **primeras 10** de
  las marcas propias por corrida. Es una condición de la licencia.
- El hash perceptual del logo se calcula directamente con numpy; **no** se usan
  `imagehash` ni `scipy`.
- Este proyecto es una herramienta de apoyo a la vigilancia, no sustituye el criterio del
  abogado. Confirme cualquier decisión de oposición con un profesional.

## Licencia

Ver `LICENSE.TXT`.

---

*Creado por Osvaldo. Vigilancia de marcas (Panamá).*
