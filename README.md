# Consolidado de insumos — Jamar

Tablero web de las solicitudes de insumos que las tiendas registran en Power Apps.
Agrupa por tienda y, dentro de cada tienda, por tipo de insumo: Cafetería, PGP, Pelex y Calidad.

## Archivos

| Archivo | Qué es |
|---|---|
| `index.html` | La página completa. Todo el CSS y el JavaScript van adentro; no hay dependencias que instalar. Lo único que pide a internet es la tipografía de Google Fonts. |
| `datos-insumos.json` | Los datos. **Es el único archivo que se reemplaza mes a mes.** |

## Publicar

En el repositorio: **Settings → Pages → Source: Deploy from a branch → `main` / `(root)`**.
La página queda en `https://<usuario>.github.io/<repositorio>/`.

No hace falta `.nojekyll`: ningún archivo empieza por guion bajo.

## Actualizar los datos cada mes

1. Abre la página publicada.
2. Arrastra el `Solicitud Suministros.xlsx` actualizado sobre la zona **Actualizar datos**.
   Se lee en tu navegador; el archivo no se sube a ningún lado.
3. Revisa que las cifras y el selector de mes estén bien.
4. Pulsa **Exportar JSON**. Baja un `datos-insumos.json`.
5. En GitHub, reemplaza `datos-insumos.json` por el que acabas de bajar y haz commit.

En cuanto GitHub Pages republique, todo el que abra la página ve el mes nuevo.
El pie del panel dice de dónde salieron los datos que estás viendo:
`datos-insumos.json` cuando los leyó del repositorio, o `copia incluida en la página`
cuando abriste el `index.html` con doble clic sin servidor.

**Importar JSON** hace lo contrario: carga un `.json` guardado antes, para revisar un mes
anterior o revertir un cambio sin tocar el repositorio.

## Forma del JSON

```json
{
  "catalogo": [
    { "producto": "GOTA MAGICA", "precio": 5922, "cat": "Calidad", "fila": 20 }
  ],
  "recs": [
    { "per": "2026-08", "t": "Cuatro Vientos", "p": "GOTA MAGICA",
      "q": 3, "s": "henry vergara", "f": "24 agosto 2026 11:52" }
  ],
  "excluidos": { "cant0": 23, "cantVacia": 4, "sinFecha": 0, "sinPrecio": 0 },
  "tiendasCatalogo": ["Principal", "Cuatro Vientos"],
  "generado": "2026-08-27"
}
```

- `cat` solo acepta `Cafeteria`, `PGP`, `Pelex` o `Calidad`.
- `per` es el mes en formato `AAAA-MM`; de ahí sale el selector.
- `q` es la cantidad; `t` la tienda, `p` el insumo, `s` el solicitante, `f` la fecha original.

La página valida el JSON al importarlo y dice exactamente qué campo está mal si algo falla.

## Reglas de cálculo

- Se excluyen las líneas con cantidad **0** o vacía.
- **No se fusionan** insumos de nombre parecido (`CUCHILLA` y `CUCHILLAS PARA BISTURI`
  cuentan como dos, igual que la `LIJA ROJA 150` duplicada).
- El tipo de un insumo nuevo se deduce de su fila en la hoja `Precios`:
  2–13 Cafetería · 14–15 PGP · 16–17 Pelex · 18 en adelante Calidad.
  **Inserta el insumo nuevo dentro del bloque que le corresponde**, no al final.

## Compatibilidad

La lectura del `.xlsx` usa `DecompressionStream`, disponible en Chrome, Edge y Safari
recientes. Si el navegador no lo soporta, la página lo dice y el resto sigue funcionando:
siempre se puede actualizar por JSON.
