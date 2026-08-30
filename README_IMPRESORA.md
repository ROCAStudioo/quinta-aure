# Configuración de impresora y cajón — Módulo Caja (Quinta Aure)

Guía para dejar funcionando la impresión de tickets y la apertura del cajón
de dinero en la **PC de caja** con la impresora **GHIA GTP58**.

## Qué hace el sistema

El módulo de caja (`caja.html`) maneja dos impresiones distintas:

- **Pre-cuenta** (botón "🧾 IMPRIMIR PRE-CUENTA"): imprime el ticket para que el
  cliente vea cuánto debe. **NO abre el cajón.**
- **Cobro** (botón "COBRAR" → "SÍ, IMPRIMIR"): imprime el ticket **y abre el cajón**
  de dinero mediante el comando ESC/POS `ESC p`.

Para lograr esta separación (que la pre-cuenta no abra el cajón y el cobro sí) se
usa **QZ Tray**, un pequeño programa puente que envía comandos crudos a la
impresora. Un navegador por sí solo no puede hacer esto.

## Requisitos

- PC con **Windows**.
- Impresora **GHIA GTP58** conectada por **USB**.
- Cajón de dinero conectado a la impresora por el puerto **RJ11**.
- **QZ Tray** instalado y en ejecución.

## Instalación (una sola vez)

### 1. Instalar QZ Tray
1. Descarga QZ Tray desde su sitio oficial: https://qz.io/
2. Instálalo (el instalador ya trae Java incluido; no hay que instalar nada más).
3. Al terminar, QZ Tray arranca solo y queda como un ícono en la bandeja del
   sistema (junto al reloj). Debe quedar **siempre encendido** cuando se use la caja.
   - Se puede configurar para que inicie automáticamente con Windows.

### 2. Instalar el driver de la GHIA GTP58
1. Instala el driver de la impresora (viene en el CD/USB o en el sitio de GHIA).
2. Verifica que Windows la reconozca en:
   **Configuración → Bluetooth y dispositivos → Impresoras y escáneres.**
3. Anota el **nombre exacto** con el que aparece (por ejemplo `GHIA GTP58` o similar).

### 3. IMPORTANTE — Apagar la apertura automática del cajón en el driver
Para que la **pre-cuenta NO abra el cajón**, el driver debe tener la apertura
automática **desactivada** (que el cajón se abra solo cuando el sistema lo pida).

1. Ve a **Panel de control → Dispositivos e impresoras.**
2. Clic derecho en la GHIA → **Preferencias de impresión** (o **Propiedades**).
3. Busca una pestaña como **"Cash Drawer"**, **"Peripheral"** o **"Utilidad"**.
4. Pon la apertura de cajón en **"No abrir" / "Deshabilitado"**.
5. Guarda los cambios.

> Si esta opción queda en "abrir antes/después de imprimir", el cajón se abriría
> con TODA impresión (incluida la pre-cuenta), que es justo lo que NO queremos.
> El sistema se encarga de abrir el cajón únicamente al cobrar.

### 4. Seleccionar la impresora dentro del sistema
1. Abre `caja.html` e ingresa con la contraseña de caja.
2. En la barra de acciones, haz clic en el botón **"🖨 Impresora"**.
3. Aparecerá la lista de impresoras detectadas. Escribe el **nombre EXACTO** de la
   GHIA (el que anotaste en el paso 2) y confirma.
4. El nombre queda guardado en ese equipo. Solo se hace una vez.

## Cómo se usa día a día

1. El mesero envía la cuenta a caja.
2. El encargado selecciona la cuenta.
3. (Opcional) Da clic en **"🧾 IMPRIMIR PRE-CUENTA"** para entregar el ticket al
   cliente y que vea el total. **El cajón NO se abre.**
4. Elige el método de pago (en efectivo, escribe el monto recibido; el sistema
   calcula el cambio y no deja cobrar si el monto es menor al total).
5. Da clic en **"COBRAR"**.
6. En el aviso, da **"SÍ, IMPRIMIR"**: se imprime el ticket final **y se abre el cajón**.

## Si algo no funciona

- **No imprime nada por QZ Tray:** el sistema imprime de todas formas usando el
  diálogo normal del navegador (respaldo automático). Revisa que QZ Tray esté
  encendido (ícono en la bandeja) y que la impresora esté bien seleccionada
  (botón "🖨 Impresora").
- **El cajón no abre al cobrar:** puede que tu cajón use el otro pin. En
  `caja.html`, dentro de la función `imprimirTicketQZ`, cambia el comando:
  - Actual: `\x1B\x70\x00\x19\xFA`  (pin 0)
  - Alternativa: `\x1B\x70\x01\x19\xFA`  (pin 1)
- **El cajón se abre también con la pre-cuenta:** falta desactivar la apertura
  automática en el driver (paso 3).
- **El texto del ticket se ve corrido o cortado:** en `caja.html`, dentro de
  `construirTicketTexto`, ajusta la variable `W` (ancho en caracteres). El estándar
  para papel de 58mm es `32`.

## Notas

- QZ Tray es gratuito para uso básico; en modo gratuito puede mostrar un pequeño
  aviso de confirmación al imprimir. Existe una licencia de pago para quitarlo.
- Todo esto aplica solo a la PC de caja. Los demás módulos (mesero, cocina,
  bebidas) no necesitan QZ Tray.
