# Modelo de Datos

## ER 4.10

```text
   ┌──────────────────────────────────────────────────────────────┐
   │ eventos_auditoria                                           │
   ├──────────────────────────────────────────────────────────────┤
   │ PK  id              uuid                                    │
   │     routing_key     text         NOT NULL                   │
   │ UK  evento_id       uuid         NOT NULL                   │
   │     usuario_sub     text         NULL                       │
   │     payload         jsonb        NOT NULL                   │
   │     emitido_en      timestamptz  NOT NULL                   │
   │     recibido_en     timestamptz  NOT NULL                   │
   └──────────────────────────────────────────────────────────────┘


   ┌──────────────────────────────────────────────────────────────┐
   │ notificaciones                                               │
   ├──────────────────────────────────────────────────────────────┤
   │ PK  id              uuid                                    │
   │     para            text         NOT NULL                   │
   │     asunto          text         NOT NULL                   │
   │     estado          text         CHECK (enviada | fallida) │
   │     evento_id       uuid         INDEX                      │
   │     creada_en       timestamptz  NOT NULL                   │
   └──────────────────────────────────────────────────────────────┘


   ┌──────────────────────────────────────────────────────────────┐
   │ mensajes_muertos                                             │
   ├──────────────────────────────────────────────────────────────┤
   │ PK  id              uuid                                    │
   │     cola_origen     text         NOT NULL                   │
   │     routing_key     text         NOT NULL                   │
   │     motivo          text         NOT NULL                   │
   │     intentos        int          CHECK (>= 1)               │
   │     payload         text         NOT NULL                   │
   │     recibido_en     timestamptz  NOT NULL                   │
   └──────────────────────────────────────────────────────────────┘


   eventos_auditoria.evento_id ───────── notificaciones.evento_id
                  relación lógica, sin FK


## Dueño: prestamos.mjs · esquema prestamos

```text
   ┌──────────────────────────────────────────────────────────────┐
   │ prestamos.prestamos                                          │
   ├──────────────────────────────────────────────────────────────┤
   │ PK  id              integer      IDENTITY                    │
   │     libro_id        integer      NOT NULL                    │
   │     usuario_sub     text         NOT NULL         INDEX      │
   │     estado          text         CHECK (vigente | devuelto) │
   │     desde           date         NOT NULL                    │
   │     hasta           date         NOT NULL                    │
   │     creado_en       timestamptz  NOT NULL                    │
   │     devuelto_en     timestamptz  NULL                        │
   └──────────────────────────────────────────────────────────────┘


   prestamos.usuario_sub + prestamos.libro_id
   └── UNIQUE cuando estado = 'vigente'


| Restricción o índice | Por qué |
|---|---|
| `UNIQUE (usuario_sub, libro_id) WHERE estado = 'vigente'` | Un lector no tiene dos préstamos vigentes del mismo libro, y sí puede volver a pedirlo después de devolverlo |
| `ix_prestamos_usuario_sub` | El panel lista los préstamos de un lector |
| Sin FK en `libro_id` | El libro lo sirve `libros.mjs`, que es otro servicio |
```