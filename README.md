# Albero

Portal de estadísticas del fútbol regional andaluz. Clasificaciones, calendario completo, goleadores y tarjetas compartibles para las categorías de Sevilla, de Tercera Andaluza a División de Honor.

Web independiente. Datos no oficiales.

## Qué hay aquí

| Archivo | Qué es |
|---|---|
| `index.html` | El portal: selector de competición, clasificación, jornada, calendario, goleadores y tarjeta compartible |
| `club.html` | Plantilla de web de club, por si algún club la quiere |
| `panel.html` | Panel de administración: generar calendario, cargar jornadas, aprobar peticiones y exportar `datos.json` |
| `aviso-legal.html` | Aviso legal, privacidad y supresión de datos |
| `equipos.json` | Registro de equipos: escudo, localidad y campo |
| `escudos/` | Imágenes de los escudos |

Sin dependencias, sin compilación, sin servidor. Tres archivos estáticos.

## Publicar en GitHub Pages

1. Crea un repositorio nuevo. Ponlo **público** (Pages gratis solo funciona en repos públicos).
2. Sube los tres `.html` y este `README.md` a la raíz. Vale arrastrarlos en *Add file → Upload files*.
3. Ve a **Settings → Pages**.
4. En *Source* elige **Deploy from a branch**, rama `main`, carpeta `/ (root)`. Guarda.
5. En dos o tres minutos estará en `https://TU-USUARIO.github.io/NOMBRE-DEL-REPO/`.

### Dominio propio

En **Settings → Pages → Custom domain** escribe tu dominio. Luego, en el panel de tu registrador:

- Para `albero.es` (dominio raíz), cuatro registros `A` apuntando a `185.199.108.153`, `185.199.109.153`, `185.199.110.153` y `185.199.111.153`.
- Para `www.albero.es`, un `CNAME` a `TU-USUARIO.github.io`.

Marca **Enforce HTTPS** cuando GitHub te deje (tarda un rato en emitir el certificado).

## Antes de publicar

- [ ] Rellenar los datos del titular en `aviso-legal.html` (nombre, NIF, domicilio, correo). Lo exige el artículo 10 de la Ley 34/2002 y ahora mismo están como `[...]`.
- [ ] Cambiar los correos de ejemplo `hola@albero.example` y `datos@albero.example` por los reales.
- [ ] Poner la fecha de última actualización del aviso legal.
- [ ] Revisar que el aviso de "datos no oficiales" del pie del portal sigue visible.
- [ ] Poner tu WhatsApp o tu correo en la constante `CONTACTO` de `index.html`, para que el formulario público te llegue.

## Equipos y escudos

Tu club se configura en la constante `MI_CLUB` al principio del script de `index.html`. Sale resaltado en verde en la clasificación.

Para poner un escudo, deja la imagen en `escudos/` y añade la ruta en `equipos.json`:

```json
"C.D. Carrión": {
  "escudo": "escudos/carrion.png",
  "localidad": "Carrión de los Céspedes",
  "campo": "Campo Municipal de Carrión de los Céspedes"
}
```

Los equipos que no tengan escudo salen con uno generado a partir de sus iniciales, con un color estable derivado del nombre. No queda como un hueco: queda como una decisión.

**Un escudo es una marca de su titular.** El de tu club puedes usarlo; el de los rivales necesita su permiso. Mientras no lo tengas, el escudo generado cumple y no infringe nada.

## Filtrar por equipo

El cuarto desplegable de la barra superior filtra por equipo. Al elegir uno, el calendario pasa a mostrar **todos sus partidos de la temporada** con el número de jornada, en lugar de una jornada suelta, y el equipo queda marcado en la clasificación.

## Cómo se actualizan los datos

El portal busca un `datos.json` en la raíz. Si lo encuentra, lo usa; si no, enseña datos de muestra en lugar de salir en blanco. Ese archivo lo produce el panel.

Circuito de cada jornada:

1. Abre `panel.html` (en local o en tu web, da igual).
2. Pestaña **Resultados**: carga los marcadores de la jornada. También puedes pegar el texto de un WhatsApp.
3. Pestaña **Peticiones**: las que te hayan llegado de fuera. **Nada se publica sin que le des a aprobar.**
4. Pestaña **Publicar**: descarga `datos.json` y súbelo al repositorio.

La primera vez, empieza por la pestaña **Competición** para generar el calendario a partir de la lista de equipos, mientras la RFAF no publique el oficial.

### Por qué el panel puede estar a la vista

`panel.html` no tiene contraseña y no le hace falta: no guarda nada en ningún servidor. Quien lo abra podrá teclear en su navegador, pero para que algo salga en la web hay que subir `datos.json` al repositorio, y eso exige tu cuenta de GitHub. El control de acceso es el commit.

## Conectar una API

Cuando tengas licencia de datos, sustituye el `fetch("datos.json")` de `buscarDatosReales()` por la llamada a tu endpoint. El resto del portal no cambia.

**Importante:** la clave de la API no puede ir en el JavaScript, porque cualquiera la ve en el inspector aunque el repo sea privado. Hace falta un intermediario (una Cloudflare Worker o una función de Netlify, gratis las dos) que guarde la clave y llame por ti.

El formato que espera el portal:

```js
{
  id, competicion:{provincia,categoria,grupo,equipos,asc,pro,des},
  jornada, jugadas, totalJornadas, actualizado,
  partidos:[{jornada,local,visitante,gl,gv,estado,autor,hace,fecha,campo}],
  resultados:[...],   // los partidos de la última jornada disputada
  proxima:[...],      // los de la siguiente
  goleadores:[{nombre,equipo,goles}]
}
```

Dos cosas que conviene no romper:

- **`gl` y `gv` a `null`** cuando el partido no se ha jugado. De ahí sale que el calendario muestre hora y campo en lugar de marcador.
- **La clasificación no se envía.** Se calcula agregando `partidos`. Si mandas puntos hechos, tarde o temprano la tabla contradirá a los resultados.

Valores de `estado`: `acta`, `confirmado`, `pendiente`, `discrepancia`, `programado`.

## Datos de muestra

Lo que se ve hoy son datos generados: el calendario sale de un sorteo por el método del círculo y los resultados de un simulador con semilla fija, así que son estables entre recargas. **Los nombres de equipos y jugadores son inventados**, para no atribuir resultados falsos a clubes reales.
