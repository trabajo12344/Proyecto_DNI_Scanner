# FrontEnd #

## Cambiar puerto de la aplicación ##

```bash
uvicorn frontend:app --reload --port 3001
```




```js

# frontend.py
from fastapi import FastAPI, Request, Form
from fastapi.responses import HTMLResponse, RedirectResponse
import httpx

app = FastAPI()

BACKEND_URL = "http://localhost:8000"  # tu backend

@app.get("/", response_class=HTMLResponse)
async def index():
    async with httpx.AsyncClient() as client:
        r = await client.get(f"{BACKEND_URL}/personas/")
        personas = r.json()

    html = """
    <html>
        <head><title>Personas</title></head>
        <body>
            <h1>Listado de Personas</h1>
            <ul>
    """
    for p in personas:
        html += f"<li>{p['id']} - {p['nombre']} {p['apellido']} (DNI: {p['dni']})</li>"

    html += """
            </ul>
            <h2>Agregar Persona</h2>
            <form action="/add" method="post">
                Nombre: <input type="text" name="nombre"><br>
                Apellido: <input type="text" name="apellido"><br>
                DNI: <input type="text" name="dni"><br>
                Dirección: <input type="text" name="direccion"><br>
                Género: <input type="text" name="genero"><br>
                Fecha Nac: <input type="date" name="fecha_nacimiento"><br>
                <input type="submit" value="Crear">
            </form>
        </body>
    </html>
    """
    return HTMLResponse(content=html)

@app.post("/add")
async def add_persona(
    nombre: str = Form(...),
    apellido: str = Form(...),
    dni: str = Form(...),
    direccion: str = Form(""),
    genero: str = Form(""),
    fecha_nacimiento: str = Form("")
):
    data = {
        "nombre": nombre,
        "apellido": apellido,
        "dni": dni,
        "direccion": direccion or None,
        "genero": genero or None,
        "fecha_nacimiento": fecha_nacimiento or None,
    }
    async with httpx.AsyncClient() as client:
        await client.post(f"{BACKEND_URL}/personas/", json=data)

    return RedirectResponse("/", status_code=303)


```
