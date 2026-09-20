# 🎯 Guía Operativa Pro: Trading Advisor Bard.fx & Vigilante Autónomo

Este sistema implementa un asistente de trading institucional multiactivo para **Forex, Criptomonedas, Commodities y Acciones**, integrado a **n8n Cloud** y conectado en tiempo real con tu bot de Telegram [**@Tradinn8n_bot**](https://t.me/Tradinn8n_bot).

El sistema cuenta con un **Vigilante Autónomo (Cron cada 5m)** que funciona con una regla estricta: **SOLO vigila tus compras registradas y tus activos configurados**. Si tu portafolio está vacío, el vigilante entra en pausa automática y no emite alertas innecesarias.

---

## 1. Comandos de Gestión y Configuración (Telegram)

Puedes enviar estos comandos directamente al chat de tu bot [**@Tradinn8n_bot**](https://t.me/Tradinn8n_bot):

### 💰 A. Registrar Compras / Acciones con Monto Invertido
Permite registrar tu precio de entrada y el dinero invertido para que el Vigilante supervise tus ganancias, tu Stop Loss y tus metas de salida:

> **Formato:** `COMPRAR <TICKER> <PRECIO_ENTRADA> <MONTO_USD>`
- **Acciones:** `COMPRAR NVDA 120 1000` *(NVDA comprado a $120 invirtiendo $1,000 USD)*
- **Criptomonedas:** `COMPRAR BTC-USD 60000 500` *(BTC a $60,000 con $500 USD)*
- **Forex:** `COMPRAR EURUSD=X 1.0850 2000` *(EUR/USD a 1.0850 con $2,000 USD)*
- **Commodities:** `COMPRAR GC=F 2500 1500` *(Oro a $2,500 con $1,500 USD)*

### 👁️ B. Agregar Activos solo para Vigilancia de Gatillos (Sin compra previa)
Si no has comprado aún pero quieres que el bot te avise cuando aparezca una oportunidad de entrada Bard.fx:
- `VIGILAR TSLA` o `AGREGAR TSLA`
- `VIGILAR GC=F`
- `VIGILAR GBPUSD=X`

### ⚙️ C. Configurar Capital de Cuenta y Riesgo por Operación
- `CAPITAL 10000` ➔ Fija el saldo de tu cuenta en **$10,000 USD** (o el monto que desees).
- `RIESGO 1` ➔ Fija el riesgo por operación en **1%** (el bot calculará exactamente cuántos títulos o contratos comprar según la distancia al Stop Loss para no arriesgar más de ese porcentaje).

### 📊 D. Consultar tu Portafolio y Estado en Tiempo Real
- `PORTAFOLIO` o presiona el botón **💼 Mi Portafolio & Compras**:
  - Muestra tus compras activas, precio actual, **PnL en $ y %**, valor actual de la inversión y estado del plan de salida (SL, Break Even, TP).
  - Si está vacío, te indica que el Vigilante está en pausa y te recuerda los comandos para agregar activos.

### 🗑️ E. Eliminar Activos o Vaciar Portafolio
- `QUITAR NVDA` ➔ Retira NVDA de tus compras y de la vigilancia.
- `LIMPIAR` o `VACIAR` ➔ Elimina todos los activos y compras, dejando el Vigilante Autónomo en **pausa inmediata**.

---

## 2. Metodología Bard.fx: Compensation Play (No Wick Strategy)

La estrategia opera en temporalidad de **5 minutos** buscando huellas institucionales:

```
[ Vela Institucional Sin Mecha ] ➔ [ Retroceso / Mitigación (50% o Apertura) ] ➔ [ Gatillo 5m (Rechazo) ] ➔ [ ENTRADA ]
```

1. **Detección de la Vela "No Wick" (Sin Mecha):**
   - Vela impulsiva con mecha inferior (en compras) o superior (en ventas) menor al **8% del rango total**.
   - Indica que las instituciones entraron con volumen masivo de mercado sin retroceso previo.
2. **Mitigación / Compensación del Vacío:**
   - Entre 3 y 15 velas posteriores, el precio regresa a testear el precio de apertura de la vela sin mecha.
3. **Gatillo de Entrada a 5 Minutos:**
   - Vela de rechazo o pinbar a favor de la tendencia dentro de la zona de mitigación.

---

## 3. Matemática del Trade (1:1) y Plan de Salida en 3 Fases

El sistema programa automáticamente la salida en 3 etapas calculadas al milímetro:

| Fase | Nivel Objetivo | Acción Recomendada |
| :--- | :--- | :--- |
| **Fase 1: Break Even (1:1)** | Distancia idéntica al Stop Loss de 5m | **Mover Stop Loss al precio de entrada (BE)**. La operación queda **100% libre de riesgo**. |
| **Fase 2: Take Profit 2** | **En lo alto de la vela impulsiva previa** (o bajo en ventas) | **Cierre parcial del 70%** de la posición y asegurar ganancias. |
| **Fase 3: Trailing Stop** | Estructura de mínimos ascendentes en 5m | Dejar correr el 30% restante con Stop Loss dinámico para capturar grandes tendencias. |

---

## 4. Reglas del Vigilante Autónomo (Cron cada 5m)

1. **Exclusividad Total:** El escáner programado **SOLO** analiza los activos que el usuario tiene en sus compras o en su lista de vigilancia.
2. **Cero Ruido / Cero Spam:**
   - Si el portafolio está vacío ➔ **No hace nada** (0 ejecuciones aguas abajo, 0 alertas).
   - Si no hay ningún evento relevante en la ronda de 5m ➔ **No envía mensajes**.
3. **Eventos que Disparan Alertas Inmediatas:**
   - 🚨 **Descenso bajo el Stop Loss a 5m:** Alerta urgente para cortar pérdida según tu gestión.
   - 🛡️ **Alcance del Ratio 1:1:** Alerta para mover Stop Loss a Break Even.
   - 🎯 **Alcance del Take Profit 2 (Alto de Vela):** Alerta para tomar beneficios parciales (70%).
   - 🎯 **Detección de nuevo Gatillo Bard.fx:** En activos vigilados, aviso de oportunidad con SL, TP y tamaño de posición sugerido.

---

## 5. Enlaces y Estado del Despliegue

- **Workflow en n8n Cloud:** [https://irvinrom.app.n8n.cloud/workflow/zd0pfJcGScp90mQ1](https://irvinrom.app.n8n.cloud/workflow/zd0pfJcGScp90mQ1)
- **Bot de Telegram:** [@Tradinn8n_bot](https://t.me/Tradinn8n_bot)
- **Token Telegram:** `8923613669:AAG8CHP09iybu_B47XUPsMeeMdj-rURX9Ds`
- **Chat ID:** `5900371156`