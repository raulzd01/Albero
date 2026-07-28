# Albero

Portal de estadísticas del fútbol regional andaluz. Clasificaciones, calendario completo, goleadores y tarjetas compartibles para las categorías de Sevilla, de Tercera Andaluza a División de Honor.

Web independiente. Datos no oficiales.

## Qué hay aquí

| Archivo | Qué es |
|---|---|
| `index.html` | El portal: selector de competición, clasificación, jornada, calendario, goleadores y tarjeta compartible |
| `club.html` | Plantilla de web de club, por si algún club la quiere |
| `aviso-legal.html` | Aviso legal, privacidad y supresión de datos |

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

## Conectar los datos reales

Todo el portal lee de una sola función, `cargarCompeticion(id)`, al principio del `<script>` de `index.html`:

```js
async function cargarCompeticion(id){
  // const res = await fetch(`/api/competiciones/${id}`); return normalizar(await res.json());
  return construir(CATALOGO.find(c=>c.id===id));
}
```

Descomenta el `fetch`, apúntalo a tu API y devuelve este formato:

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
