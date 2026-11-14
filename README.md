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

## Versión web de la app (carpeta `/app`)

La app también se publica como web en `/app` (para iPhone y para quien no la tiene
instalada). Mismo código y misma base de datos (Supabase) que la app del móvil.

Para actualizarla:

```
npm i --no-save --legacy-peer-deps react-native-web@~0.21.0
EXPO_OFFLINE=1 npx expo export --platform web --output-dir dist-web
```

y copiar `dist-web` a la carpeta `app/` del repositorio `oscaza/oscaza.github.io`
(sin `metadata.json`), añadir `<meta name="robots" content="noindex">` al `index.html`
y copiar ese `index.html` también como `404.html` en la raíz (así funcionan las
direcciones tipo `/app/mesa/...` al recargar).

De momento la web solo deja entrar a quien llega con el enlace de una mesa
(`src/lib/webAccess.ts`, `OPEN_TO_EVERYONE`).

## Si el enlace de invitación no abre la app

1. **La causa principal**: `.well-known/assetlinks.json` tiene que llevar la huella
   SHA-256 de la llave con la que Google Play firma la app (Play Console › Prueba y
   publica › Configuración › Integridad de la app › Firma de aplicaciones › «Huella digital
   del certificado SHA-256» de la *clave de firma de la app*). Sin ella, en los móviles que
   instalan la app desde Google Play Android no da la web por verificada y el enlace se
   queda en el navegador.
2. Si aun así se queda en el navegador, el botón «Abrir en Tardegelus» de `unirse/` abre la
   app con `intent://` y, además, copia la invitación: al abrir Tardegelus a mano, la app la
   ve en el portapapeles y pregunta si quieres entrar en esa mesa.

## Cambiar de dominio (por ejemplo, tardegelus.com)

- GitHub Pages › Custom domain, y los DNS de `rondas/web/pasos-dominio.md`.
- App: `WEB_HOST` en `src/lib/joinLink.ts` y añadir el dominio nuevo (sin quitar
  `oscaza.github.io`, para que sigan valiendo los enlaces viejos) en
  `android.intentFilters` de `app.json`.
- Copiar `.well-known/assetlinks.json` tal cual al dominio nuevo.
