# Modelo de Datos

## ER 2.8

```text
   ┌──────────────────────────────────────────┐
   │ mensajes_taller                          │
   ├──────────────────────────────────────────┤
   │ PK  id           uuid                    │
   │     routing_key  text         NOT NULL   │
   │     payload      jsonb        NOT NULL   │
   │     recibido_en  timestamptz  NOT NULL   │
   └──────────────────────────────────────────┘
```