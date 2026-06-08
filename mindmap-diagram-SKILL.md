---
name: mindmap-diagram
description: >-
  Genera mapas mentales en JSON importable por Mindmap Estudio (index.html).
  Usa cuando el usuario pida un diagrama, mapa mental, esquema visual o JSON
  para importar en mindmap, o cuando quiera que Claude/IA cree la estructura
  del mapa.
---

# Mindmap Estudio — Generar diagramas para importar

## Objetivo

Producir un archivo `.mindmap.json` que el usuario importe en **Menú → Importar** de Mindmap Estudio.

## Qué pedir al usuario (si falta contexto)

1. Tema o título del mapa
2. Profundidad deseada (2–4 niveles suele bastar)
3. Si quiere colores por rama o solo estructura
4. Idioma de las etiquetas

## Formato de salida

Entregar **solo JSON válido** (o un bloque ```json``` + indicar que lo guarde como `nombre.mindmap.json`).

```json
{
  "name": "Título del mapa",
  "rootId": "n_root",
  "exportedAt": "2026-05-21T12:00:00.000Z",
  "nodes": { }
}
```

### Nodo (obligatorio)

Cada entrada en `nodes` debe tener:

| Campo | Tipo | Reglas |
|-------|------|--------|
| `id` | string | Único. Prefijo `n_` recomendado |
| `text` | string | Etiqueta visible. `\n` para varias líneas |
| `x`, `y` | number | Centro del nodo en px. Raíz en `(0, 0)` |
| `parent` | string \| null | `null` solo en la raíz |
| `children` | string[] | IDs hijos, ordenados |
| `depth` | number | `0` raíz; hijo directo `1`, etc. |

### Nodo (opcional)

| Campo | Uso |
|-------|-----|
| `color` | Borde del nodo (hex) |
| `bgColor` | Fondo del nodo (hex) |
| `lineColor` | Color de la línea al padre (hex) |

## Reglas de consistencia (críticas)

- `rootId` debe existir en `nodes` con `parent: null` y `depth: 0`
- Si `A.children` incluye `B`, entonces `B.parent === A.id`
- Todo `children[]` y `parent` deben ser bidireccionales
- No dejar nodos huérfanos ni referencias rotas
- `depth` del hijo = `depth` del padre + 1

## Layout recomendado

Coordenadas en píxeles; rejilla útil: **24 px** (snap de la app).

### Raíz → hijos directos (depth 1)

Distribución radial alrededor de `(0, 0)`, radio ~**240–360 px**:

```
angle_i = (i * 2π / max(n, 6)) - π/2
x = cos(angle_i) * radius
y = sin(angle_i) * radius
```

### Ramas profundas (depth ≥ 2)

Extender **hacia fuera** del padre, en columna vertical:

- Separación horizontal padre→hijo: **~340 px** (`COL_GAP`)
- Separación vertical entre hermanos: **~76 px** (`ROW_GAP`)
- Si el padre está a la derecha del abuelo, hijos a la **derecha** del padre; si está arriba, hijos **arriba**, etc.

### Texto

- Máx. ~40 caracteres por línea; partir con `\n`
- Raíz: 1–3 palabras clave o título corto
- Hojas: frases concretas, no párrafos

## Paleta sugerida (depth / ramas)

| Uso | Borde | Fondo suave |
|-----|-------|-------------|
| Raíz | `#1c1b18` | `#ffffff` |
| Rama 1 | `#3d6b4f` | `#e3eee5` |
| Rama 2 | `#4a6c8c` | `#e3eaf0` |
| Rama 3 | `#a8763e` | `#f5ead8` |
| Rama 4 | `#7a4e7c` | `#f0e3f0` |
| Acento | `#c8553d` | `#fce8e3` |

Aplicar `color` + `bgColor` en nodos de depth 1 (ramas principales). Hijos pueden heredar o ir sin color.

## Flujo de trabajo

1. Definir jerarquía en texto (árbol) antes de asignar coordenadas
2. Crear raíz y ramas principales con layout radial
3. Colocar subnodos en columnas hacia fuera
4. Validar referencias `parent` / `children` / `depth`
5. Entregar JSON + instrucción: **Menú → Importar** en Mindmap Estudio

## Checklist antes de entregar

- [ ] JSON parseable sin comentarios ni trailing commas
- [ ] `name` descriptivo
- [ ] Un solo nodo con `parent: null`
- [ ] Sin solapamientos obvios (hermanos separados ≥ 76 px en Y)
- [ ] Etiquetas en el idioma acordado

## Prompt de ejemplo para el usuario

Copia y adapta:

```
Crea un mapa mental en JSON compatible con Mindmap Estudio.
Tema: [tu tema]
Profundidad: 3 niveles
Idioma: español
Colorea cada rama principal.

Sigue el skill mindmap-diagram: entrega solo el JSON listo para
importar (Menú → Importar). Usa layout radial en la raíz y columnas
hacia fuera en subramas.
```

## Ejemplo mínimo

Ver `examples.md` en `.cursor/skills/mindmap-diagram/examples.md`.
