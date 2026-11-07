# Web de Tardegelus

Estos archivos se publican tal cual en GitHub Pages, en el repositorio público
`oscaza/oscaza.github.io` (así la web queda en https://oscaza.github.io).

- `unirse/index.html`: la página del enlace de invitación (`/unirse/?c=ABC234`).
  Abre la app si la tienes y, si no, lleva a Google Play.
- `.well-known/assetlinks.json`: le dice a Android que la app es la dueña de estos
  enlaces, para que se abran directamente en la app sin pasar por el navegador.
  Lleva la huella de la llave de subida (APK de pruebas) y debe llevar también la de
  la llave de firma de Google Play (Play Console › Integridad de la app).
- `.nojekyll`: sin él, GitHub Pages no publica la carpeta `.well-known`.
