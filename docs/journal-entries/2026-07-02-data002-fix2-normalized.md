# 2026-07-02 — DATA-002 / QAY52174 fix2 подтверждён

**Статус:** выполнено. DATA-002 fix2 проверен пользователем локально через Docker Desktop, PostgreSQL, API и UI.

## Рабочая ветка MVP

- Репозиторий: `ivpetr168-hue/spi-replacement-kipia-mvp`
- Ветка: `v0.8.5_data002_qay52174_pg_load_fix2`
- Версия: `0.8.5-data002-qay52174-pg-load-normalized`
- `main` не изменялся и не должен изменяться без явной команды пользователя.

## Что сделано в fix2

Ветка fix2 перевела DATA-002 от демонстрационных подстановок к нормальной рабочей структуре БД.

Добавлена Alembic-миграция:

- `backend/alembic/versions/20260702_0005_data002_instrument_reference_links.py`

Она добавила в `instruments` нормальные поля связи:

- `manufacturer_id`
- `instrument_model_id`
- `function_type_id`
- `location_id`
- `system_io_type_id`
- `pid_document_id`
- `pid_number`

И связи на справочники:

- `instruments.manufacturer_id → manufacturers.id`
- `instruments.instrument_model_id → instrument_models.id`
- `instruments.function_type_id → instrument_function_types.id`
- `instruments.location_id → instrument_locations.id`
- `instruments.system_io_type_id → system_io_types.id`
- `instruments.pid_document_id → documents.id`

Добавлен API-слой:

- `backend/app/instrument_card_repository.py`

Он собирает карточку прибора через JOIN из PostgreSQL, а не из JSON-подстановок.

Обновлены:

- `backend/app/loaders/qay52174_fixture_loader.py`
- `backend/app/fixtures/qay52174_oracle_spi_data.json`
- `backend/app/main.py`
- `frontend/src/main.jsx`
- `VERSION`

## Результат Docker-проверки

Пользователь запустил:

```powershell
docker compose up --build
```

После удаления старых контейнеров система стартовала успешно.

Подтверждено в логах:

```text
Running upgrade 20260702_0004 -> 20260702_0005, DATA-002 normalized instrument reference links.
```

Loader завершился успешно:

```text
DATA-002 QAY52174 fixture load complete: {
  'instruments': 3,
  'connections': 15,
  'process_data': 3,
  'instrument_specifications': 3,
  'io_assignments': 3,
  'instrument_reference_links': 3
}
```

`instrument_reference_links = 3` означает, что все три прибора QAY52174 получили нормальные связи на справочники.

## SQL JOIN-проверка PostgreSQL

Пользователь выполнил контрольный SQL JOIN по таблицам:

- `instruments`
- `instrument_locations`
- `system_io_types`
- `manufacturers`
- `instrument_models`
- `instrument_function_types`

Результат:

```text
        tag_number         | location | io_type | manufacturer |                          model                          | function_type |          pid_number
---------------------------+----------+---------+--------------+---------------------------------------------------------+---------------+------------------------------
 RURN-NC-QAY52174 -FIT  01 | Field    | AI-PCS  | Rosemount    | 3051S2CD3A2A11A1BKMM5P1P0Q4Q8T1A9285 c/w0305RC52B11B4L8 | FIT           | RURN-FDE-NC-ID-521-D52-74F01
 RURN-NC-QAY52174 -PIT  08 | Field    | AI-PCS  | Rosemount    | 3051S1TG5A2A11A1BB4C1EMM5P1Q4Q8                         | PIT           | RURN-FDE-NC-ID-521-D52-74P08
 RURN-NC-QAY52174 -TIT  01 | Field    | AI-PCS  | Rosemount    | 3144PD2A2EMB5M5T1C1Q4                                   | TIT           | RURN-FDE-NC-ID-521-D52-74T01
(3 rows)
```

Вывод: данные реально связаны через ключи PostgreSQL, а не подставлены вручную в интерфейсе.

## UI-проверка

Пользователь проверил интерфейс в браузере:

- `http://localhost:8080`

По скриншоту и сообщению пользователя подтверждено:

- свойства всех трёх приборов заполняются;
- карточка показывает `Field`, `AI-PCS`, `P&ID`, `Rosemount`, модель, `Manufacturer ID`, `Model ID`;
- правая панель свойств синхронизирована с выбранным прибором;
- данные проходят полный вертикальный срез: PostgreSQL → API → UI.

## Итог DATA-002 fix2

DATA-002 / QAY52174 fix2 подтверждён как успешный вертикальный срез:

```text
PostgreSQL справочники
→ FK-связи в instruments
→ API JOIN
→ UI карточка прибора
```

## Обязательное архитектурное правило с этого момента

Пользователь отдельно подтвердил правило дальнейшей разработки:

```text
Проект КИПиА / SPI Replacement дальше ведём как взрослую рабочую систему, а не как демонстрационный макет.

PostgreSQL — источник истины.

Все бизнес-данные, справочники, связи, структура объектов, свойства карточек, дерево, действия и отображаемые поля должны идти из базы данных через backend/API.

Frontend не должен содержать жёстко прошитую инженерную модель, справочники, свойства приборов, связи или демонстрационные подстановки.

Никаких заплаток, временных пришлёпок, JSON-костылей и ручных UI-подстановок вместо нормальной структуры БД.

Если есть справочник — создаётся справочник в PostgreSQL.
Если есть связь — используется ключ / foreign key.
Если нужен быстрый доступ — добавляется индекс.
Если данные пришли из SPI — сохраняется трассировка source_*.
Импорт/экспорт могут меняться, но рабочая модель БД должна оставаться нормализованной и пригодной для промышленной системы.
```

Это правило считать обязательным для всех следующих изменений проекта.

## Следующий этап

После фиксации DATA-002 fix2 пользователь планирует отдать проект на внешний ИИ-рефакторинг для получения замечаний по архитектуре.

Дальше обсуждение должно идти по взрослой архитектуре проекта:

- структура PostgreSQL;
- справочники;
- FK-связи;
- индексы;
- Alembic;
- backend/API;
- frontend без жёстких прошивок;
- импорт/экспорт SPI;
- сохранение вертикального среза DATA-002.

Рефакторинг не должен разрушить подтверждённую цепочку DATA-002 fix2.
