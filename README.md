# Web de Tardegelus

Estos archivos se publican tal cual en GitHub Pages, en el repositorio público
`oscaza/oscaza.github.io`, con el dominio propio https://tardegelus.eu (archivo `CNAME`).
La dirección vieja https://oscaza.github.io lleva sola al dominio nuevo.

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
Quien llega sin enlace puede pulsar «Pedir acceso» y dejar su correo (tabla
`web_solicitudes`, migración 0014). El administrador (correo en la tabla `web_admins`) las
aprueba en Ajustes › Solicitudes de acceso (o en el menú de la web). Se borran solas a los 30 días.

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

## Dominio (tardegelus.eu)

- El archivo `CNAME` le dice a GitHub Pages el dominio. Los DNS están en
  `rondas/web/pasos-dominio.md` (4 registros A a GitHub + `www` CNAME a `oscaza.github.io.`).
- App: `WEB_HOST` en `src/lib/joinLink.ts`. En `app.json`, `android.intentFilters` lleva
  `tardegelus.eu` con `autoVerify` y `oscaza.github.io` aparte y **sin** `autoVerify`
  (esa dirección ahora redirige y, en Android 11 o menos, un dominio que no verifica
  estropearía la verificación de todos).
- `.well-known/assetlinks.json` se publica tal cual en el dominio.

## Datos personales

- El correo de contacto de la web se monta con JavaScript (`index.html`, `CONTACT_EMAIL`
  en `src/lib/joinLink.ts`) para que no lo recojan los robots.
- El correo del administrador de las solicitudes de acceso solo está en la tabla
  `web_admins` de Supabase, nunca en el código (la web es pública).
