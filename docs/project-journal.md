# SPI Replacement / MVP КИПиА — журнал работы

## 2026-07-01 11:30 Asia/Magadan — подготовлен список изменений для `v0.8.5` по отчётам без изменения кода

**Статус:** выполнено. Код не изменялся. Ветка не создавалась. Новая версия не выпускалась.

Цель `v0.8.5`:
- сделать первые реальные отчёты из PostgreSQL на базе уже существующей инженерной модели;
- не пытаться полностью повторить графические SPI-отчёты;
- подготовить демонстрационный результат, который можно показать как движение от карточки прибора к реальным отчётам.

Ожидаемый результат `v0.8.5`:
- в интерфейсе появляется раздел `Отчёты`;
- пользователь может открыть отчёты по выбранному прибору/контуру;
- отчёты берут данные из PostgreSQL, а не из статических PDF;
- минимум один отчёт — `Instrument Specification` — должен выглядеть как реальный инженерный отчёт, а не просто как таблица.

Альтернативы:
- вариант 1: сразу делать SmartLoop / wiring diagrams — отклонено для `v0.8.5`, потому что нужны полноценные кабели, жилы, клеммы и связи;
- вариант 2: сделать только простые таблицы — недостаточно убедительно для демонстрации руководству;
- вариант 3: сделать один полноценный `Instrument Specification` и несколько простых табличных отчётов — выбранный вариант.

Список изменений для `v0.8.5`:

1. Добавить раздел `Отчёты` в интерфейс.
   - Отображение отчётов должно быть связано с выбранным объектом: прибор, контур, I/O assignment.
   - Меню и доступность отчётов должны проектироваться через PostgreSQL metadata, не жёстко в React.

2. Реализовать отчёт `Instrument Specification`.
   - Источник-эталон: `instrument specification page1.pdf`, `instrument specification page2.pdf`.
   - Данные: Tag No., Service, Location, P&ID, Function, Manufacturer, Model, диапазон, статус, тип прибора, примечания.
   - Для `v0.8.5` допускается упрощённая форма, но она должна быть похожа на инженерную спецификацию.
   - Это главный демонстрационный отчёт версии.

3. Расширить правую панель свойств прибора как источник данных для отчёта.
   - Источник-эталон: `prop.png` / окно SPI `Tag Number Properties`.
   - Проверить наличие полей: Loop number, Number, Service, Instrument type, Status, Location, I/O type, Line, P&ID, Manufacturer, Model, Signal type.
   - Недостающие поля добавить через БД/metadata, если они отсутствуют в текущей модели.

4. Реализовать простой табличный отчёт `I/O Map`.
   - Источники-эталоны: `i_o map report.pdf`, `io map report.pdf`, `maprep.pdf`.
   - Для `v0.8.5`: без полного повторения печатной формы SPI.
   - Минимальные поля: Control System, Node, Cabinet/Panel, Rack, Slot, Card, Channel, I/O Type, Tag Number.

5. Реализовать простой табличный отчёт `I/O Tag Assignment`.
   - Источники-эталоны: `io tag assignment.pdf`, `iotagassignmentrep.pdf`.
   - Минимальные поля: Tag Number, Control System Tag, Cabinet/Panel, Terminal Strip, Terminal, Channel, I/O Type.
   - Клеммную детализацию делать только в пределах уже имеющихся данных.

6. Подготовить простой `Cable / Wiring Schedule`, если текущая БД уже содержит достаточные данные.
   - Если данных недостаточно, оставить как заготовку/пустой отчёт с понятным сообщением.
   - Не пытаться в `v0.8.5` генерировать Point-to-Point Wiring Diagram.

7. Backend API.
   - Добавить endpoints для отчётов:
     - `GET /api/v1/reports/instruments/{id}/specification`;
     - `GET /api/v1/reports/io-map`;
     - `GET /api/v1/reports/io-tag-assignment`;
     - опционально `GET /api/v1/reports/cable-schedule`.
   - API должен возвращать структурированные данные, пригодные для HTML-представления.

8. БД и Alembic.
   - Проверить текущие таблицы: `instruments`, `loops`, `instrument_types`, `specifications`, `process_data`, `documents`, `panels`, `terminals`, `cables`, `io_cards`, `io_channels`, `instrument_io_assignments`.
   - Если таблиц/полей не хватает — добавить только минимально необходимые изменения через Alembic.
   - SQLite не использовать.

9. Seed/demo data.
   - Добавить демонстрационные данные для одного-двух приборов, чтобы отчёты сразу открывались после запуска.
   - Минимальный набор: один прибор, один контур, одна спецификация, один I/O channel, один I/O assignment.

10. Frontend.
   - Добавить страницу/панель отчётов.
   - Добавить просмотр `Instrument Specification`.
   - Добавить табличные просмотры `I/O Map` и `I/O Tag Assignment`.
   - Сохранить SPI-подобную логику: выбранный объект слева → свойства/отчёты справа или в центральной области.

11. Проверки.
   - Проверить backend API.
   - Проверить открытие отчётов в UI.
   - Проверить, что отчёты берут данные из PostgreSQL.
   - Проверить Docker Desktop на Windows.
   - Проверить, что `main` не ломается.

12. Документация.
   - Обновить `README.md` после подтверждения работоспособности.
   - Обновить `docs/project-journal.md` при каждом важном шаге.
   - Зафиксировать, что SmartLoop, Enhanced SmartLoop, Loop Signal Diagram, Tag Signal Diagram и Point-to-Point Wiring Diagram остаются эталонами будущих версий, но не входят в полный объём `v0.8.5`.

Не входит в `v0.8.5`:
- полноценная генерация SmartLoop;
- Enhanced SmartLoop;
- Loop Signal Diagram;
- Tag Signal Diagram;
- Point-to-Point Wiring Diagram;
- полноценная модель трассировки сигналов;
- полный редактор шаблонов отчётов;
- экспорт в PDF, если это задержит рабочий прототип.

Критерий готовности `v0.8.5`:
- пользователь запускает прототип через Docker Desktop;
- открывает прибор;
- видит SPI-подобные свойства прибора;
- открывает `Instrument Specification` из PostgreSQL;
- открывает простые `I/O Map` и `I/O Tag Assignment`;
- версия видна как `0.8.5`;
- `main` после проверки остаётся рабочим только после подтверждения пользователя.

Следующая команда, которую ждём от пользователя:
- `Утверждаю v0.8.5. Создай ветку и начинай реализацию.`

## 2026-07-01 11:15 Asia/Magadan — сделана таблица разбора SPI-отчётов

**Статус:** выполнено.

По команде пользователя сделана первичная таблица разбора загруженных SPI-отчётов из `docs/spi-source-materials/reports/`. Цель — понять, какие отчёты можно включать в `v0.8.5`, какие сущности PostgreSQL нужны, и какие отчёты надо отложить до полноценной модели соединений.

| SPI report / материал | Назначение | Основные данные отчёта | Нужные сущности PostgreSQL | Предлагаемая версия | Приоритет | Решение |
|---|---|---|---|---|---|---|
| `instrument specification page1.pdf`, `instrument specification page2.pdf` | Полная спецификация одного прибора / tag number. | Tag No., Service, Location, P&ID, Function, Area Classification, Ambient Temperature, Humidity, Type of Operation, Latching, IP, Supply Voltage, Electrical Enclosure Class, Mounting, Cable Entry, Contacts, Rating, Manufacturer, Model, PO, CMMS/P&ID Tag, Notes, revision block. | `instruments`, `instrument_types`, `specifications`, `specification_fields`, `process_data`, `manufacturers`, `models`, `documents`, `units`, `areas`, `lines`, `pid_documents`, `audit_log`. | `v0.8.5` | Высокий | Делать первым реальным отчётом. Это лучший кандидат для демонстрации руководству. |
| `prop.png` / окно SPI `Tag Number Properties` | Экранная карточка свойств прибора. | Loop number, Number, Service, Instrument type, Status, Location, I/O type, Line, P&ID, Manufacturer, Model, Intrinsically safe circuit type, Signal type. | `instruments`, `loops`, `instrument_types`, `instrument_properties`, `lines`, `pid_documents`, `manufacturers`, `models`, `io_types`, `statuses`, `locations`. | `v0.8.5` | Высокий | Использовать как эталон для правой панели свойств и источника данных для спецификации. |
| `i_o map report.pdf`, `io map report.pdf`, `maprep.pdf`, `specification forms analizer BS_W.pdf` | PLC/DCS I/O Map — карта размещения сигналов по аппаратуре I/O. | Document Number, Revision, Net, Node, Module, Cabinet, Rack, Slot, I/O Type, I/O Card, Model, Channel, CS Tag, Tag Number. | `control_systems`, `control_nodes`, `panels`, `cabinets`, `io_cards`, `io_channels`, `instrument_io_assignments`, `instruments`, `documents`. | `v0.8.5` упрощённо; полный вариант позже | Высокий | В `v0.8.5` сделать табличный отчёт без полного повторения печатной формы. |
| `io tag assignment.pdf`, `iotagassignmentrep.pdf` | Назначение тегов на I/O-каналы и клеммы. | Net, Node, Module, Profibus info, Cabinet, Rack, Slot, Sys cable, Terminal, Channel, Channel Address, Control System Tag, Tag Number, Wiring equipment, Terminal strip, I/O Type. | `panels`, `terminal_strips`, `terminals`, `io_cards`, `io_channels`, `instrument_io_assignments`, `instruments`, `cables`, `wiring_equipment`. | `v0.8.5` упрощённо; полный вариант `v0.8.6+` | Высокий | В `v0.8.5` сделать простую таблицу назначений; клеммную детализацию развивать позже. |
| `smartlooprep.pdf` | SmartLoop — графическое представление контура от поля до системы управления. | Loop Name, Loop Number, приборы, junction box, marshalling cabinet, system cabinet, клеммы, кабели, связи, title block. | `loops`, `instruments`, `signal_paths`, `panels`, `terminal_strips`, `terminals`, `cables`, `cable_cores`, `connections`, `generated_drawings`. | `v0.8.7+` | Высокий, но не для `v0.8.5` | Использовать как эталон будущей генерации схем. Полностью в `v0.8.5` не делать. |
| `enchansed smartloop report.pdf` | Enhanced SmartLoop — расширенная схема контура с зонами Field / Junction Box / Marshalling Cabinet / System Cabinet. | Разделение схемы по физическим зонам, связи между оборудованием, title block, loop name. | Все сущности SmartLoop плюс классификация участков трассы: `signal_path_segments`, `locations`, `panels`, `junction_boxes`, `cabinets`. | `v0.8.7+` | Средний/высокий | Делать после появления нормальной модели соединений и сегментов сигнального пути. |
| `loop signal diagram.pdf` | Loop Signal Diagram — сигнальная схема на уровне контура. | Loop Name, Loop Service, Signal/Tag Number, функциональные блоки, сигнальные связи, условные обозначения, title block. | `loops`, `instruments`, `signals`, `signal_paths`, `connections`, `generated_drawings`, `drawing_templates`. | `v0.8.8+` | Средний | Пока использовать как образец для будущего генератора схем. |
| `tag signal diagram.pdf` | Tag Signal Diagram — сигнальная схема на уровне отдельного тега. | Tag Number, Loop Name, Signal path, функциональные блоки, связи, title block. | `instruments`, `loops`, `signals`, `signal_paths`, `connections`, `generated_drawings`. | `v0.8.8+` | Средний | Не включать в `v0.8.5`; делать после модели сигналов. |
| `ptopwiringdiagram.pdf`, `112.pdf` | Point-to-Point Wiring Diagram — соединения от точки до точки. | Loop Name, Loop Service, Signal/Tag Number, панели, junction box, terminal strips, terminals, polarity, cable name, pair/core, cross wire, shieldbar, последовательность соединений. | `panels`, `junction_boxes`, `terminal_strips`, `terminals`, `cables`, `cable_cores`, `connections`, `shields`, `loops`, `instruments`, `signal_paths`. | `v0.8.8+` / `v0.9.0` | Очень высокий, но позже | Это один из ключевых инженерных отчётов, но он требует полноценной модели кабелей, жил, клемм и соединений. |
| `Sc4.docx` | Служебный материал/скриншоты/описание для анализа SPI-экранов и отчётов. | Уточняется при детальном разборе DOCX. | Зависит от содержимого: вероятно `ui_modules`, `ui_property_schemas`, `report_templates`, `documents`. | Аналитика перед `v0.8.5` | Средний | Использовать как вспомогательный источник для уточнения UI и отчётов. |

Вывод по `v0.8.5`:
- в `v0.8.5` включать только первые реальные отчёты из PostgreSQL, без сложной графической генерации;
- основной кандидат №1 — `Instrument Specification`;
- дополнительные кандидаты — простые табличные `I/O Map`, `I/O Tag Assignment`, возможно простой `Cable/Wiring Schedule`;
- SmartLoop, Enhanced SmartLoop, Loop Signal Diagram, Tag Signal Diagram и Point-to-Point Wiring Diagram пока не реализовывать полностью, а использовать как эталоны будущих версий.

Следующее действие:
- подготовить согласованный список изменений для `v0.8.5` на основе этой таблицы;
- код не менять до подтверждения пользователя.

## 2026-07-01 10:55 Asia/Magadan — подгружены актуальные SPI source materials и Reports, выполнено первичное ознакомление

**Статус:** первичное ознакомление выполнено, детальный разбор содержимого отчётов нужен следующим шагом.

Пользователь сообщил, что подгрузил актуальные материалы в репозиторий и создал подпапку `Reports` с образцами отчётов, которые сейчас есть в SPI/SP.

Обнаруженные новые материалы в репозитории `ivpetr168-hue/spi-replacement-kipia-mvp`:
- `docs/spi-source-materials/` — основные документы, ERD, концепции, скриншоты SPI;
- `docs/spi-source-materials/reports/` — образцы отчётов SPI.

Основной вывод:
- `Reports` нужно рассматривать не как набор PDF для хранения, а как эталонные печатные/экспортные формы;
- отчёты SPI являются представлениями над инженерной моделью, а не первичным источником данных;
- новая система должна хранить нормализованные данные и связи в PostgreSQL, а отчёты формировать из БД.

## 2026-07-01 09:45 Asia/Magadan — проверено наличие документов с отчётами и скриншотами SPI

**Статус:** проверено.

После повторной загрузки материалы появились в `docs/spi-source-materials/` и `docs/spi-source-materials/reports/`. Для архитектурных решений использовать `Архитектура_MVP_КИПиА_v3_1.docx` как уже переработанный результат анализа, а для детального разбора — новые исходные PDF/DOCX/PNG.

## 2026-07-01 09:35 Asia/Magadan — зафиксированы актуальные версии документов

**Статус:** выполнено.

Актуальные проектные документы:
- `Концепция_замены_SPI_v0.4.docx`;
- `Архитектура_MVP_КИПиА_v3_1.docx`;
- `Архитектура_MVP_КИПиА_v3.docx` — история, актуальнее `v3_1`;
- `SPI Replacement Architecture v0.1 - 2026.06.19.docx` — исторический источник;
- `ERD_MVP_КИПиА_v3_1.drawio`;
- `ERD_MVP_КИПиА_v3_1.png`.

Текущая рабочая версия прототипа:
- `0.8.4_fix2`;
- `main = 0.8.4_fix2`;
- фиксированная ветка: `v0.8.4_fix2`;
- следующий этап: `v0.8.5`.

## 2026-07-01 09:25 Asia/Magadan — `docs/project-journal.md` объявлен единым источником информации

**Статус:** обязательное правило проекта.

Решение пользователя:
- единый источник информации для пользователя по проекту SPI Replacement / MVP КИПиА — этот файл: `docs/project-journal.md`;
- всё важное по проекту записывать сюда сплошным журналом;
- каждая запись должна содержать дату, время и содержание события;
- в журнал писать выполненные действия, задачи, планы, отчёты, проверки, решения, ошибки, риски, изменения правил, изменения версий, fix, GitHub-операции и другие существенные события;
- ChatGPT-ответы, Gmail-письма и `index.html` являются вспомогательными каналами, но не заменяют этот журнал;
- если информация не записана в `docs/project-journal.md`, она не считается зафиксированной как итоговая история проекта.

## 2026-07-01 09:25 Asia/Magadan — правило утренних планов для журнала

**Статус:** правило уточнено.

Новое правило:
- каждый утренний план по проекту SPI Replacement / MVP КИПиА должен добавляться в `docs/project-journal.md`;
- Gmail для утреннего плана не отправляется, если пользователь отдельно не попросит;
- `index.html` обновляется только если меняется публичный статус, версия, план, проверки или следующий шаг.

## 2026-07-01 08:00 Asia/Magadan — утренний план после закрытия дня

**Статус:** план сформирован.

Рабочая база:
- текущая версия: `0.8.4_fix2`;
- следующий этап: `v0.8.5` — реальные отчёты Specification, Process Data, Cable Schedule и простые табличные отчёты из PostgreSQL.

Задачи на утро:
1. Проверить автоматический запуск `SPI Вечерний Контроль v4` в 22:00 Asia/Magadan.
2. Проверить Gmail-письмо.
3. Проверить уведомление на телефон/браузер.
4. Проверить новую запись в `docs/project-journal.md`.
5. Если контур подтверждён, перейти к подготовке списка изменений для `v0.8.5`.

## 2026-06-30 21:07 Asia/Magadan — день закрыт, задачи обновлены

**Статус:** выполнено.

Что сделано за день:
- подтверждено правило произвольного завершения дня;
- обновлены две задачи: `SPI Вечерний Контроль v4` и `SPI Утренний План v3`;
- создан и проверен текстовый журнал `docs/project-journal.md`;
- `index.html` используется как публичная витрина, `docs/project-journal.md` — как основной журнал.

Актуальное состояние проекта:
- текущая рабочая версия прототипа: `0.8.4_fix2`;
- `main = 0.8.4_fix2`;
- фиксированная ветка: `v0.8.4_fix2`;
- следующий этап: `v0.8.5`.

## 2026-06-30 19:00 Asia/Magadan — вечерний контроль 0.8.4_fix2

**Статус:** выполнено.

Зафиксировано:
- актуальная рабочая версия прототипа: `0.8.4_fix2`;
- следующий этап: `v0.8.5`;
- код прототипа, workflow, ветки, версии и SMTP-секреты не изменялись;
- Gmail-письмо пришло пользователю;
- уведомление ChatGPT/browser/phone пришло на телефон после ручного запуска;
- главный оставшийся тест — автоматический запуск в 22:00 Asia/Magadan без ручного запуска.

## 2026-06-30 18:47 Asia/Magadan — реструктуризация dashboard

**Статус:** выполнено.

Dashboard переведён в формат публичного журнала работы. Актуальная версия зафиксирована как `0.8.4_fix2`, следующий этап — `v0.8.5`.

## 2026-06-30 18:42 Asia/Magadan — dashboard обновлён под 0.8.4_fix2

**Статус:** выполнено.

Публичный dashboard обновлён под актуальную рабочую базу `0.8.4_fix2`, добавлены дата/время, режим журнала и статус уведомлений.

## 2026-06-30 18:30 Asia/Magadan — обновлены запланированные задачи

**Статус:** выполнено.

Обновлены:
- `SPI Вечерний Контроль v4`;
- `SPI Утренний План v3`.

В задачи внесено:
- актуальная база `0.8.4_fix2`;
- следующий этап `v0.8.5`;
- запрет использовать `v0.8.1 / SYNC-001 / DEV-005` как актуальный контекст;
- правило работы по одному шагу;
- правило обновления публичного dashboard при версии/fix;
- состояние Gmail, browser и phone уведомлений.
