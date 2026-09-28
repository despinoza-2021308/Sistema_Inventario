## Sistema Inventario

App de Frappe/ERPNext para controlar inventario por bodegas: entradas, salidas, transferencias y ajustes, con existencia por bodega y kardex (historial de movimientos).

### DocTypes

| DocType | Tipo | Descripción |
|---|---|---|
| Categoria Producto | Catálogo | Agrupa los productos. Se nombra por su propio nombre. |
| Bodega | Catálogo | Lugares donde se guarda el stock (`BOD-.YYYY.-.#####`). |
| Producto | Catálogo | Código, nombre, categoría, precio de venta, stock mínimo y stock total (`PROD-.#####`). |
| Movimiento Inventario | Enviable | Entrada, Salida, Transferencia o Ajuste (`MOV-.YYYY.-.#####`). |
| Detalle Movimiento | Tabla hija | Producto, cantidad, precio unitario y subtotal del movimiento. |
| Stock Bodega | Automático | Existencia actual por producto y bodega (`{producto}-{bodega}`). |
| Kardex | Automático | Historial de cada entrada y salida con su saldo (`KDX-.YYYY.-.#####`). |

El stock solo cambia al **enviar** o **cancelar** un Movimiento Inventario; Stock Bodega y Kardex no se editan a mano.

### Tipos de movimiento

| Tipo | Bodega | Efecto |
|---|---|---|
| Entrada | Destino | Suma al stock |
| Salida | Origen | Resta; se rechaza si no hay stock suficiente |
| Transferencia | Origen → Destino | Resta en origen y suma en destino |
| Ajuste | Destino | Cantidad positiva suma, negativa resta |

### Scripts (incluidos como fixtures)

| Script | Evento | Función |
|---|---|---|
| MOV INV (Client Script) | Formulario | Muestra las bodegas según el tipo, filtra bodegas activas y calcula subtotales y total. |
| MOV INV VAD | Before Validate | Valida bodegas y cantidades y recalcula totales. |
| MOV INV ENV | After Submit | Actualiza Stock Bodega, crea el Kardex y recalcula el stock total del producto. |
| MOV INT CAN | After Cancel | Revierte el movimiento y registra la reversa en el Kardex. |

### Campos personalizados (incluidos como fixtures)

| DocType | Campo | Tipo |
|---|---|---|
| Sales Invoice | `custom_movimiento_inventario` | Link → Movimiento Inventario (después de `due_date`) |

Los Server Scripts requieren tenerlos habilitados en el sitio:

```bash
bench --site <sitio> set-config server_script_enabled 1
```

### Instalación

```bash
cd $PATH_TO_YOUR_BENCH
bench get-app $URL_OF_THIS_REPO --branch main
bench --site <sitio> install-app sistema_inventario
bench --site <sitio> migrate
```

### Licencia

MIT
