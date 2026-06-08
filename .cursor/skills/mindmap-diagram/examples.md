# Ejemplos — Mindmap Estudio

## JSON mínimo válido

```json
{
  "name": "Plan de proyecto",
  "rootId": "n_root",
  "exportedAt": "2026-05-21T12:00:00.000Z",
  "nodes": {
    "n_root": {
      "id": "n_root",
      "text": "Plan de proyecto",
      "x": 0,
      "y": 0,
      "parent": null,
      "children": ["n_a", "n_b"],
      "depth": 0
    },
    "n_a": {
      "id": "n_a",
      "text": "Investigación",
      "x": 0,
      "y": -300,
      "parent": "n_root",
      "children": ["n_a1"],
      "depth": 1,
      "color": "#3d6b4f",
      "bgColor": "#e3eee5"
    },
    "n_a1": {
      "id": "n_a1",
      "text": "Entrevistas\nusuarios",
      "x": 340,
      "y": -340,
      "parent": "n_a",
      "children": [],
      "depth": 2
    },
    "n_b": {
      "id": "n_b",
      "text": "Entrega",
      "x": 300,
      "y": 0,
      "parent": "n_root",
      "children": [],
      "depth": 1,
      "color": "#4a6c8c",
      "bgColor": "#e3eaf0"
    }
  }
}
```

## Cómo importar

1. Guarda el JSON como `mi-mapa.mindmap.json`
2. Abre Mindmap Estudio (`index.html`)
3. **Menú (☰) → Importar**
4. Selecciona el archivo
5. Ajusta posiciones o colores si hace falta; **Export PNG** para compartir imagen

## Ejemplo real en el repo

`abogado-laboral.mindmap.json` — mapa de 3–4 niveles con colores por rama y layout radial + columnas.

## Errores frecuentes

| Error | Consecuencia |
|-------|----------------|
| `parent` no coincide con `children` | Importación OK pero árbol incoherente al editar |
| Dos raíces (`parent: null`) | Comportamiento impredecible |
| `depth` incorrecto | Colores de línea por profundidad desalineados |
| Nodos en `(0,0)` todos | Mapa ilegible; hay que separar coordenadas |
| JSON con comentarios `//` | Falla al importar |
