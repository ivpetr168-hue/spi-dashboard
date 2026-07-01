# 2026-07-01 — Oracle SPI DB structure handoff

Создан единый handoff-документ для передачи структуры Oracle SPI в следующий чат:

```text
ivpetr168-hue/spi-replacement-kipia-mvp/docs/oracle-spi-db-structure-handoff.md
```

Commit:

```text
115f8f58a8f4e8d7a2cc96fd099d33ed0b4ebd91
```

Назначение: сохранить подтверждённую структуру Oracle SPI и правила построения SQL-запросов, чтобы при потере контекста следующий чат мог продолжить исследование без повторного восстановления цепочек.

В документе зафиксированы: `SAK.OBJECT_REGISTRY`, `COMPONENT`, `LOOP`, `DRAWING`, `PD_GENERAL`, `SPEC_SHEET_DATA`, `SPEC_FIELD_MAP`, `CABLE`, `CABLE_SET`, `WIRE`, `WIRE_GROUP`, `PANEL`, `PANEL_STRIP`, `PANEL_STRIP_TERM`, `WIRE_TERMINAL`, I/O-цепочка и правила дальнейших SQL-запросов.
