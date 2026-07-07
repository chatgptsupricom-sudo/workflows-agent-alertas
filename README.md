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

### 1. Credencial Odoo (`Odoo API`)
En n8n → Credenciales → Nueva → **Odoo**:

| Campo | Valor |
|-------|-------|
| URL | `https://tu-instancia.odoo.com` |
| Database | nombre de tu base de datos |
| Username | tu usuario Odoo |
| Password / API Key | tu contraseña o API key |

### 2. Credencial SMTP (`SMTP Correo`)
En n8n → Credenciales → Nueva → **SMTP**:

| Campo | Valor |
|-------|-------|
| Host | p.ej. `smtp.gmail.com` |
| Port | `465` (SSL) o `587` (TLS) |
| User | tu correo |
| Password | contraseña o app password |

### 3. Configurar el correo destino
Abre el nodo **"Enviar Alerta por Correo"** y edita:
- `fromEmail` → correo remitente
- `toEmail` → tu correo (puede ser una lista separada por comas)

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
