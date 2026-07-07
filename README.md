# Alerta de Ajustes de Inventario — Odoo + n8n

Flujo de n8n que consulta Odoo cada 15 minutos y envía un correo con el detalle de cualquier ajuste de inventario validado en ese periodo.

## Cómo funciona

```
[Cada 15 min] → [Odoo: ajustes "done"] → [¿Hay nuevos?]
                                                 │ Sí
                                         [Odoo: movimientos de stock]
                                                 │
                                         [Construir tabla HTML]
                                                 │
                                         [Enviar correo SMTP]
```

## Requisitos

### 1. Odoo via JSONRPC (HTTP Request)
El flujo usa el mismo patrón que tus otros workflows: llamadas directas a `https://supricom2.odoo.com/jsonrpc` con las credenciales ya embebidas en el nodo. **No necesita credencial separada de n8n** — ya está configurado con tu db, uid y api_key.

### 2. Gmail OAuth2
El nodo de correo usa tu credencial existente `Gmail account` (id: `km7PyKWLEZgHHMzw`). Si n8n la detecta por ID, no necesitas hacer nada. Si no, reasígnala manualmente en el nodo **"Enviar Alerta Gmail"**.

### 3. Configurar el correo destino
Abre el nodo **"Enviar Alerta Gmail"** y edita:
- `sendTo` → el correo o correos donde quieres recibir las alertas (separados por coma)

## Instalación

1. Abre n8n.
2. Ve a **Workflows → Import from file**.
3. Selecciona `odoo_inventory_alert.json`.
4. Configura las credenciales en los nodos marcados.
5. Activa el workflow.

## Campos que reporta el correo

| Campo | Descripción |
|-------|-------------|
| Ajuste | Nombre/referencia del ajuste en Odoo |
| Fecha | Fecha y hora del ajuste |
| Responsable | Usuario que realizó el ajuste |
| Producto | Producto ajustado |
| Cantidad Esperada | Stock teórico antes del ajuste |
| Cantidad Real | Cantidad contada/ajustada |
| Diferencia | Real − Esperado (verde = sobrante, rojo = faltante) |
| Ubicación | Ubicación de origen → destino |

## Ajustar la frecuencia

Por defecto el flujo corre cada 15 minutos. Para cambiarlo abre el nodo **"Cada 15 minutos"** y modifica el intervalo.
