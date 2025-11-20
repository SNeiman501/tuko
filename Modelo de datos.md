# Payment API Endpoint

Endpoints orientativos para el UI presentado y las interacciones web2 en general. El dimensionamiento del componente web3 queda para una etapa posterior

| # | Endpoint | Resumen |
| - | --- | --- |
| 1 | `POST /payments` | Punto de entrada: recibe `payment_id`, `amount`, `currency`, `metadata` y devuelve el evento `created`. |
| 2 | `GET /payments/{payment_id}` | Lee el estado más reciente sin recorrer todo el historial; expone `last_event`, `blockchain_hash` y posibles errores. |
| 3 | `POST /payments/{payment_id}/funds-sent` | Confirma que el dinero salió del origen y bloquea el monto; incluye timestamp o comprobante. |
| 4 | `POST /payments/{payment_id}/process` | Dispara validaciones, KYC, FX o broadcast on-chain manteniendo el paso explícito. |
| 5 | `POST /payments/{payment_id}/retry` | Permite reintentar la etapa de procesamiento con metadata adicional cuando algo falla. |
| 6 | `POST /payments/{payment_id}/confirm` | Marca `money_received`, obliga `blockchain_hash` y `settlement_timestamp`. |
| 7 | `POST /payments/{payment_id}/cancel` | Cancela antes de la confirmación final; requiere `cancel_reason` y `canceled_by`. |
| 8 | `GET /payments/{payment_id}/receipt` | Devuelve un resumen firmado con todos los timestamps relevantes y el hash final. |
| 9 | `GET /payments/{payment_id}/events` | Línea de tiempo ordenada de eventos, base para auditoría y depuración. |
|10 | `GET /events{timestamp}` | Feed global para reconciliar múltiples pagos; desde una fecha|
|11 | `GET /payments` | Listado rápido por `status`, fechas o metadata; útil en dashboards. |
|12 | `POST /payments/{payment_id}/webhooks` | Registra notificaciones push por pago o por patrón de metadata para evitar polling. |
|13 | `DELETE /payments/{payment_id}/webhooks/{hook_id}` | Limpia suscripciones cuando caducan o ya no se necesitan. |

## Registro de eventos

Evento estándar, así cada `step` tiene su propio timestamp y metadata.
```
{
  "event_id": "evt_01H...",
  "payment_id": "pay_123",
  "step": "processing",
  "status": "success",
  "actor": "system|user|webhook",
  "timestamp": "2025-11-20T01:12:23.456Z",
  "metadata": {
    "reason": "insufficient_liquidity",
    "blockchain_hash": "0xabc..."
  },
  "prev_event_id": "evt_01G..."
}
``` 