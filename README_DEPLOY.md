
# Despliegue de `paleoclimadatav2` (Vite + React + PWA minimal)

## Requisitos
- Node 18+ (recomendado Node 20)
- NPM, Yarn o PNPM
- (Opcional) Docker

## Variables de entorno
Cree un archivo `.env.local` (o use `.env`) basado en `.env.example`. 
> Nota: Para exponer variables al cliente con Vite deben empezar por `VITE_`.
Actualmente no hay referencias a claves en el código. Si más adelante usa claves públicas, renómbrelas como `VITE_ALGO=...` y consúmalas con `import.meta.env.VITE_ALGO`.

## Desarrollo local
```bash
npm i
npm run dev
```

## Build de producción
```bash
npm run build
npm run preview # prueba local de la carpeta dist
```

## Despliegue (Vercel / Netlify)
- **Vercel**
  - Proyecto Nuevo → Framework: Vite
  - Comando de build: `npm run build`
  - Directorio de salida: `dist`
  - Incluya `vercel.json` (rutas SPA incluidas)
- **Netlify**
  - Comando: `npm run build`
  - Public: `dist`
  - `netlify.toml` ya incluye redirección SPA.

## Despliegue con Docker
```bash
docker build -t paleoclimadatav2 .
docker run -p 8080:8080 paleoclimadatav2
```
Abra http://localhost:8080

## PWA / Service Worker
Incluimos un `public/sw.js` **mínimo y seguro** que cachea los assets públicos base. 
Si desea cachear el bundle de Vite, recomendamos usar `vite-plugin-pwa` en lugar de listar manualmente archivos con hash.

## Cambios realizados para producción
- `index.html`: se eliminaron importmaps de CDN y se corrigieron rutas a `/manifest.json`, `/icons/*` y `/sw.js`.
- `public/icons`: se añadieron iconos placeholder 192/512.
- `public/sw.js`: reescrito a una versión mínima compatible con assets generados por Vite.
- Archivos de despliegue añadidos: `Dockerfile`, `nginx.conf`, `vercel.json`, `netlify.toml`, `.env.example`.

## PWA con `vite-plugin-pwa`
- El plugin ya está configurado en `vite.config.ts` con `registerType: 'autoUpdate'` e `injectRegister: 'auto'`.
- No necesitas registrar manualmente el Service Worker.
- En desarrollo: `npm run pwa:dev`. En producción, `npm run pwa:build` y `npm run pwa:preview`.
- El `manifest` se genera desde la config del plugin y también hay un `public/manifest.json` de respaldo para hosts estáticos.
- Caché: `StaleWhileRevalidate` para assets del mismo origen e imágenes externas, con limpieza de caches antiguos.
