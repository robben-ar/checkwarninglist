# IOC // Warnlist Check — BASE4 CyberSOC

Portal HTML standalone para validar IOCs (IPs, dominios, hashes, URLs) contra las
[MISP Warning Lists](https://github.com/MISP/misp-warninglists) oficiales y descartar
falsos positivos antes de escalar un indicador.

Corre **100% en el navegador**: no hay backend propio. El JS del portal consulta en
vivo `api.github.com` y `raw.githubusercontent.com` directamente desde el cliente.
Por eso se puede alojar tal cual en GitHub Pages — es solo un archivo estático.

**Demo:** `https://<usuario-u-org>.github.io/<este-repo>/`

## Publicar en GitHub Pages

1. Pusheá este repo a GitHub (ver comandos abajo).
2. En el repo: **Settings → Pages → Build and deployment → Source** → `Deploy from a branch`.
3. Elegí la rama `main` y carpeta `/ (root)` → **Save**.
4. En 1-2 minutos queda publicado en `https://<usuario-u-org>.github.io/<repo>/`, con HTTPS automático.

### Dominio propio (opcional)

Para servirlo bajo un dominio de BASE4 (ej. `tools.base4sec.com`):

1. Creá un archivo `CNAME` en la raíz del repo con una sola línea: el dominio elegido.
2. En tu DNS, agregá un registro `CNAME` de ese subdominio apuntando a `<usuario-u-org>.github.io`.
3. En **Settings → Pages** confirmá el dominio y activá "Enforce HTTPS" (tarda unos minutos en emitir el certificado).

## Repo público vs. privado

- **Público**: Pages funciona sin plan adicional. El HTML no tiene credenciales
  embebidas, así que no hay problema en que sea visible — lo único "sensible" que
  toca el navegador de cada usuario son los IOCs que ese usuario pega, y eso nunca
  sale del navegador salvo hacia GitHub para el lookup de warninglists.
- **Privado**: Pages en repos privados requiere GitHub Pro, Team o Enterprise (o
  una organización con esos planes). Si BASE4 no quiere que el portal quede
  público/indexable, esta es la vía.

## Rate limit de la API de GitHub

Cada visitante consulta `api.github.com` sin autenticar desde su propia IP
(**60 solicitudes/hora por IP**, compartidas entre todos los que lo usen desde la
misma red/oficina). El portal incluye un panel **"⚙ Configuración"** donde cada
usuario puede pegar su propio
[token de acceso personal](https://github.com/settings/tokens?type=beta) de
GitHub (sin scopes, alcanza el más básico) para subir su límite a 5000/hora. El
token se guarda solo en el almacenamiento local de su propio navegador — nunca
viaja a ningún servidor que no sea `api.github.com`, y no queda en este repo.

## Desarrollo local

No hay build ni dependencias — es un único `index.html`. Para probarlo local:

```bash
python3 -m http.server 8080
# abrir http://localhost:8080
```

(Si preferís correrlo como servicio en un servidor propio en vez de GitHub Pages,
ese flujo con `systemd` + venv aislado también está disponible — pedilo aparte.)

## Estructura

```
.
├── index.html                      # el portal completo (HTML+CSS+JS)
├── .github/workflows/validate.yml  # valida el HTML en cada push (htmlhint)
├── LICENSE
└── README.md
```

## Actualizar el portal ya publicado

```bash
git add index.html
git commit -m "update: <qué cambiaste>"
git push
```

GitHub Pages redeploya solo en 1-2 minutos tras el push a la rama configurada.
