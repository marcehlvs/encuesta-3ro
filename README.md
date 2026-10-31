# Encuesta de convivencia · 3.° A

Encuesta anónima (12 preguntas, 3 bloques: roles, reglas, valores) para la actividad de Matemática
del proyecto «Construyendo Nuestra Comunidad Aula». Los chicos responden desde el celular y después
**tabulan y grafican ellos**; el panel docente tiene un modo que muestra solo los conteos crudos.

1. `npm install`
2. Copiá `.env.example` a `.env` y completá las credenciales de Firebase.
3. `npm run dev`

La encuesta está en `/` y el panel docente en `/panel`.

## Proyecto de Firebase propio

Usá un **proyecto de Firebase nuevo**, distinto del de 1.° y del de 2.° (una base de datos tiene un solo conjunto de reglas
y el botón «Reiniciar encuesta» elimina todas las respuestas). Reemplazá el ID en `.firebaserc`.

## Puesta en marcha (una sola vez)

1. **Firestore**: creá la base y publicá `firestore.rules` (`firebase deploy --only firestore:rules`).
2. **Authentication** > Método de acceso: activá **Correo electrónico/contraseña**.
3. **Alta de docentes**: Authentication > Usuarios: agregá correo y contraseña y copiá el **UID**.
   Después, en Firestore, colección `docentes`: creá un documento cuyo **ID sea ese UID** (con un campo
   cualquiera, p. ej. `nombre`). Sin ese documento, la cuenta no puede leer respuestas.
4. **App Check**: registrá la app web con **reCAPTCHA Enterprise** (tipo web, por puntuación) y pegá la clave
   en `VITE_RECAPTCHA_SITE_KEY`. Activá «Exigir» para Firestore solo después de ver solicitudes verificadas.
   En desarrollo aparece un token de depuración en la consola: registralo en App Check > Tokens de depuración.
5. **Clave de API**: en Google Cloud > Credenciales, restringila por referente HTTP a tu dominio.
6. **Enlace corto**: `firebase hosting:sites:create encuesta-3a` (queda en `https://encuesta-3a.web.app`).
   Ponelo en `VITE_URL_PUBLICA` **antes** de compilar; el panel genera el QR.
7. **Publicar**: `npm run build && firebase deploy --only hosting`.

## Cómo usarlo en clase

1. Entrá a `/panel` y tocá **Abrir encuesta** (la encuesta no acepta respuestas hasta entonces).
2. Proyectá el QR; los chicos responden desde el celular.
3. Cuando llegaron todas, tocá **Cerrar encuesta**: el total queda fijo.
4. Con **Modo pizarrón (solo conteos)** los chicos copian los números, calculan porcentajes y arman sus gráficos.
5. Al final, **Mostrar gráficos y comparación yo / curso** para contrastar con lo que hicieron en el pizarrón.

## Cambiar las preguntas

Todo está en **`src/preguntas.js`**: textos, opciones, bloques y temas.

- Cambiar texto u opciones: solo ese archivo.
- Cambiar el **orden**: solo ese archivo.
- Cambiar la **cantidad** de preguntas: ese archivo + la lista `p01…p12` en `firestore.rules` (publicá las reglas antes de desplegar).

Cada tema se pregunta dos veces (`sujeto: 'yo'` y `'curso'`). `positiva: false` marca conductas donde lo deseable
es responder «nunca» (así la comparación yo/curso siempre mide lo mismo).

## Privacidad

- No se pide nombre, edad ni curso, y no se guarda fecha ni ningún identificador: cada respuesta son solo 12 números del 1 al 4.
- No hay cortes por rol ni por subgrupo.
- El panel no muestra nada con menos de 5 respuestas (`MIN_RESPUESTAS` en `src/agregacion.js`). El CSV solo trae conteos agregados.
- Este filtro se aplica en el navegador del docente: quien tenga acceso a la consola de Firebase ve las respuestas individuales (anónimas).
- El navegador recuerda que ya se respondió (`localStorage`), pero no hay forma de impedir del todo que alguien responda dos veces
  (borrando datos del navegador, otro celular). Tenelo en cuenta al interpretar los números.

## Reiniciar la encuesta

La tarjeta roja del panel elimina todas las respuestas (hay que escribir `BORRAR`) y **habilita de nuevo a quienes ya respondieron**. No se puede deshacer.

Cómo funciona: cada celular recuerda en qué «ronda» respondió (`localStorage`). La ronda vigente está en el documento `config/estado` de Firestore, junto a `abierta`; al reiniciar, el panel guarda un número nuevo y, la próxima vez que alguien abra el enlace, el celular ve que la ronda cambió y muestra el formulario. No hace falta cambiar `firestore.rules`. Sigue siendo un control «una vez por dispositivo»: no impide responder desde otro navegador o en modo incógnito.

## Aviso a la dirección

Borrador en `docs/nota-direccion.md`.

## Diagnóstico de errores

Abrí la encuesta con `?debug` al final de la dirección para ver el motivo técnico. El error completo queda en la consola del navegador.
