# python-web-app

Aplicación web en Flask desplegada en un servidor Ubuntu 24.04 de Google Cloud, servida con Gunicorn detrás de Nginx.

**En vivo:** http://34.27.206.248

## Arquitectura

```
Usuario
  v
IP pública :80
  v
Nginx (reverse proxy)
  v
Gunicorn :8000
  v
Flask (app.py)
```

## Estructura

| Archivo | Descripción |
| --- | --- |
| `app.py` | Aplicación Flask |
| `templates/index.html` | Vista que renderiza Flask |
| `requirements.in` | Dependencias principales |
| `requirements.txt` | Versiones exactas generadas con pip-compile |
| `.github/workflows/format.yml` | Ruff en cada Pull Request |

## Desarrollo local

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python app.py   # http://127.0.0.1:8000
```

Para agregar una dependencia se edita `requirements.in` y se regenera el lock:

```bash
pip-compile requirements.in
```

## Calidad de código

Cada Pull Request hacia `main` ejecuta Ruff:

```bash
ruff format --check .
ruff check .
```

## Deployment

El código del servidor proviene de este repositorio:

```bash
git clone https://github.com/Atru29/python-web-app.git ~/app
cd ~/app
python3 -m venv .venv
./.venv/bin/pip install -r requirements.txt
```

Gunicorn corre como servicio systemd en `/etc/systemd/system/pythonapp.service`, escuchando **solo** en `127.0.0.1:8000`. Nginx, configurado en `/etc/nginx/sites-available/pythonapp`, recibe el puerto 80 desde Internet y lo reenvía hacia Gunicorn.

Para desplegar un cambio ya mergeado en `main`:

```bash
cd ~/app && git pull
./.venv/bin/pip install -r requirements.txt
sudo systemctl restart pythonapp
```
