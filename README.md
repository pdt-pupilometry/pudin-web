# pudin-web

Repositorio de la página pública del proyecto de pupilometría sobre Meta
Quest Pro. La descripción del sistema —qué mide, cómo replicarlo, el
pipeline, el modelo— vive en la página misma (`index.html`), no en este
README.

## Estructura

```
pudin-web/
├── index.html          ← la página completa (HTML + CSS + JS inline)
├── assets/              ← imágenes servidas por la página
│   ├── soporte-01-isometrica.jpg
│   ├── soporte-02-frontal.jpg
│   ├── soporte-03-frontal-camaras.jpg
│   ├── soporte-04-planta.jpg
│   ├── soporte-05-isometrica-posterior.jpg
│   └── segmentacion-panel.png
├── LICENSE
├── CITATION.cff
└── README.md
```

`index.html` referencia las imágenes de `assets/` por ruta relativa (con
`loading="lazy"` y dimensiones declaradas) — ya no van embebidas en base64.
Los pesos del modelo tampoco viven en el repo: se descargan desde un
[Release de GitHub](../../releases) (ver la sección "Modelo y datos" de la
página para el enlace vigente).

## Editar la página

Todo el contenido, estilos y el script del medidor animado del encabezado
están en `index.html`. Es un solo archivo; ábrelo en cualquier editor.

Dónde está cada cosa dentro del archivo:

- `<style>` — tokens de color/tipografía en `:root`, luego reglas por
  componente (hero, tarjetas, tablas, `.pstep` de la guía, etc.).
- Marcadores `[FALTA: …]` — información que falta completar en el contenido
  (archivos por publicar, datos por confirmar). Se ven en la página con un
  recuadro punteado (`.falta`) y no deben reemplazarse por texto inventado.
- El slot del modelo 3D está comentado dentro del Paso 1 de la guía
  (`<!-- SLOT MODELO 3D — REEMPLAZAR -->`), con el snippet de
  `<model-viewer>` listo para pegar en cuanto exista `assets/soporte.glb`.

## Verla en local

```
python3 -m http.server 8000
```

y abre `http://localhost:8000/index.html`. (Abrir el archivo con doble clic
también funciona, pero las rutas relativas a `assets/` requieren servirlo
desde un servidor local o Pages para evitar restricciones de `file://` en
algunos navegadores.)

## Publicarla

### GitHub Pages (automático)

Cada push a `main` dispara `.github/workflows/deploy-pages.yml`, que publica
el sitio con [GitHub Pages](https://pages.github.com/). La URL queda en
Settings → Pages.

También se puede re-disparar a mano: Actions → Deploy GitHub Pages → Run
workflow.

### Actualizar los pesos del modelo

Los pesos (`yolo26l-seg.pt`, ~63,5 MB) se publican como asset de un
[Release](../../releases), no como archivo del repo, para no inflar cada
`clone`. Para publicar una versión nueva:

1. Releases → Draft a new release → adjunta el `.pt`.
2. En `index.html`, busca `releases/download/` en la sección "Modelo y
   datos" y actualiza la URL al asset del release nuevo.

### Cualquier hosting estático

Netlify, Vercel, S3 + CloudFront, o el servidor de la universidad. Es HTML
plano: sube `index.html` y `assets/` y listo.

## Licencia y cita

- **Código** (esta página y repos del sistema): [MIT](LICENSE)
- **Documentación, archivos 3D y pesos del modelo**: [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/) (ver nota al final de [`LICENSE`](LICENSE))

Cómo citar: [`CITATION.cff`](CITATION.cff)

## Código del sistema

- Captura sobre el visor — https://github.com/pdt-pupilometry/RPi
- Procesamiento en la nube — https://github.com/pdt-pupilometry/AWS_fluxx
- Visualización — integrada en la plataforma ALFONSO en el despliegue original; no es un repo de PuDiN ni se publica aquí (ver sección “Visualización” de la página)
