# PuDiN — página pública del proyecto

Página única que documenta el sistema de medición de dilatación pupilar sobre
Meta Quest Pro: el montaje, el pipeline de procesamiento, los resultados y la
descarga de los pesos del modelo de segmentación.

Corresponde al entregable "página web de acceso público que documenta el
montaje y permite replicar el sistema completo".

## Estructura

```
pudin-web/
├── index.html                     ← la página (autocontenida: imágenes embebidas)
├── modelo/
│   └── yolo26l-seg.pt             ← pesos entrenados, 63,5 MB
├── assets/                        ← imágenes fuente en resolución original
│   ├── soporte-01-isometrica.jpg
│   ├── soporte-02-frontal.jpg
│   ├── soporte-03-frontal-camaras.jpg
│   ├── soporte-04-planta.jpg
│   ├── soporte-05-isometrica-posterior.jpg
│   └── segmentacion-panel.png
└── README.md
```

`index.html` no depende de `assets/`: las imágenes van embebidas en base64
dentro del propio archivo, así que abre bien con doble click y no se rompe al
moverlo de carpeta. `assets/` está para futuras ediciones — si querés cambiar
una vista o recortarla, partí de ahí.

Lo único que `index.html` sí carga de afuera son las tipografías (Google
Fonts) y el link de descarga de `modelo/yolo26l-seg.pt`.

## Verla

Doble click en `index.html`. No hace falta servidor.

## Publicarla

### GitHub Pages (automático)

Cada push a `main` dispara `.github/workflows/deploy-pages.yml`, que publica
el sitio con [GitHub Pages](https://pages.github.com/).

1. Crear el repo (si aún no existe) y pushear esta carpeta a la raíz de `main`.
2. Settings → Pages → **Source: GitHub Actions** (no "Deploy from a branch").
3. El primer deploy corre solo; la URL queda en Settings → Pages
   (`https://<org>.github.io/pudin-web/` o el custom domain si lo configurás).

También se puede re-disparar a mano: Actions → Deploy GitHub Pages → Run workflow.

**Ojo con los pesos.** El `.pt` pesa 63,5 MB. GitHub lo acepta (el límite duro
por archivo es 100 MB) pero infla el repo y cada `clone` se lo lleva entero.
Conviene colgarlo de un Release y apuntar el link ahí:

1. Releases → Draft a new release → adjuntás `yolo26l-seg.pt`.
2. En `index.html`, buscás `href="modelo/yolo26l-seg.pt"` y lo reemplazás por
   la URL del asset del Release.
3. Borrás `modelo/` del repo (y del artifact de Pages).

### Cualquier hosting estático

Netlify, Vercel, S3 + CloudFront, o el servidor de la universidad. Es HTML
plano: subís la carpeta y listo.

## Pendientes

### 1. Modelo 3D

Hay un espacio reservado para el visor rotable del soporte. En `index.html`
buscá:

```
<!-- ============  SLOT MODELO 3D — REEMPLAZAR  ==========  -->
```

El comentario de ahí trae el snippet de `<model-viewer>` listo para pegar:
exportás el soporte a `.glb`, lo dejás en la carpeta y reemplazás el
placeholder. La alternativa sin exportar nada es pegar un `<iframe>` de
Sketchfab.

El informe promete el modelo 3D publicado junto al trabajo, así que además
del visor conviene un link de descarga del `.stl` o `.step` — quien quiera
replicar el montaje necesita el archivo imprimible, no solo poder girarlo en
pantalla.

### 2. Gráficos del informe (opcional)

Las ilustraciones 17 y 18 (mAP50-95 por clase, y la serie temporal real de
ambos ojos durante el estímulo fotomotor) contarían la sección de resultados
mejor que la prosa actual.

### 3. Módulo de visualización

Está nombrado en la sección "El vacío" como el tercer componente del sistema,
pero no tiene sección propia ni capturas.

## Código del sistema

- Captura sobre el visor — https://github.com/pdt-pupilometry/RPi
- Pipeline de procesamiento — https://github.com/pdt-pupilometry/AWS_fluxx

## Sobre los pesos

`modelo/yolo26l-seg.pt` es YOLO26l-seg entrenado sobre video de este montaje
(cámaras GC0308 en NIR, el ángulo que impone el soporte, escala de grises).
Dos clases: `0 = pupila`, `1 = iris`, anotadas como polígono de elipse
completa.

En producción no se usa el `.pt`: se exporta a ONNX con
`scripts/export_model.py` del repo AWS_fluxx y el `.onnx` viaja horneado en la
imagen de la Lambda.
