# OralIA v3.0: código para publicar en línea

Esta carpeta trae la versión web de OralIA v3.0. Suma a la v2.1 la navegación que funciona dentro de la app de Claude, el correo con enlaces reales, la autoprueba y los datos compartidos cuando se abre desde su enlace de Claude.

## Archivos

| Archivo | Qué es |
|---|---|
| `index.html` | La app completa: pantallas, estilos y código JavaScript legible (unos 320 KB). |
| `ort_glue.txt`, `ort_wasm.wasm` | Motor onnxruntime-web 1.20.1 (Microsoft, licencia MIT) que ejecuta la IA en el navegador. |
| `modelo_1.txt`, `modelo_2.txt` | Modelo InceptionV3 cuantizado a 8 bits, en base64 y partido en dos. Cada parte pesa menos de 25 MB, el límite para subir archivos desde la página de GitHub. |

## Probarla en el computador

Con doble clic no carga la IA, porque el navegador bloquea la descarga de archivos locales. Hay que abrirla con un servidor:

```
cd carpeta_de_estos_archivos
python -m http.server 8000
```

Luego se abre `http://localhost:8000` en Chrome o Edge. Para usarla con doble clic está el archivo único `OralIA_v3.0.html` de la carpeta oralia.

## Publicarla con GitHub Pages

1. Crear un repositorio público en GitHub.
2. Subir los 5 archivos con «Add file» y «Upload files».
3. En Settings, Pages, elegir «Deploy from a branch», la rama `main` y la carpeta `/ (root)`.
4. En uno o dos minutos queda en `https://USUARIO.github.io/REPOSITORIO/`.

Así el celular y el computador abren la misma app desde internet. La IA sigue funcionando dentro de cada equipo.

## Datos compartidos

OralIA guarda los casos en una base de datos en internet: la API `oraliaapi` en Neon (proyecto `oralia`, tabla `oralia_docs`). Su dirección está en `index.html`, en `window.ORALIA_API`. Así el sitio, el archivo único y cualquier celular o computador ven los mismos casos. Dentro de Claude, el enlace del artefacto usa la base de datos de Claude, que es otra.

La API es pública y sirve para datos de demostración: no guarde datos reales de pacientes. Si la API no responde, la app sigue funcionando con los datos del equipo y sube los cambios cuando vuelve.

En `index.html`, los puntos que hay que cambiar son estos:

- `Nube` sincroniza los registros con la base del artefacto de Claude (`iniciar`, `recibir`, `subir`). Otra base en línea puede reemplazar sus llamadas a `db.collection(...).onSnapshot` y `db.doc(...).set/delete`.
- `Store` guarda y lee el estado `S` en `localStorage` (`load`, `save`, `clear`).
- `Api` reúne las operaciones del backend simulado: `login`, `registrarPaciente`, `enviarAnalisis`, `ejecutarAnalisis`, `derivar`, `agendarControl`, `descartar`, `crearUsuario`.
- `sembrar()` crea las cuentas y los casos de demostración.

## Correo

El botón «Enviar reporte por correo» abre Gmail o la app de correo con el destinatario, el asunto y el texto listos, y el usuario presiona Enviar. En el teléfono, «Compartir el PDF» adjunta el reporte. Para enviar correos de forma automática, sin que el usuario presione Enviar, se necesita un servidor con un servicio de correo.

## Mapa del código en `index.html`

- `Validator`: revisa formato y calidad de la foto.
- `AIEngine`: carga el modelo y calcula la probabilidad y el mapa Grad-CAM.
- `RiskEngine`: junta la IA y el cuestionario en el nivel de riesgo.
- `Report`: arma el PDF y las notificaciones.
- `vLogin`, `vGuia`, `vInicio`, `vCaptura`, `vFicha`, `vAnalisis`, `vResultado`, `vCasos`, `vPacientes`, `vUsuarios`, `vPerfil`: las pantallas.
- `Nav`, `rutas` y `router()`: la navegación en memoria, que no depende de la dirección.
- `autoprueba()`: revisa motor, pantallas, ingreso y base de datos (Perfil, «Ejecutar autoprueba»).

## Cuentas de prueba

| Rol | Correo | Contraseña |
|---|---|---|
| Paciente | paciente@oralia.cl | Paciente#2026 |
| Especialista | especialista@oralia.cl | Especialista#2026 (código MFA 246810) |
| Administrador | admin@oralia.cl | Admin#2026 |

OralIA es un prototipo académico (UVM, Ingeniería de Software). No reemplaza el diagnóstico de un profesional de la salud.
