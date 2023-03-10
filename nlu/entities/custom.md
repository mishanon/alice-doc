# Кастомные сущности

Разработчик сценария может создать свои именованные сущности, это полезно, потому что остальные технологии NLU, например [Granet](../granet/index.md) и AliceTagger, будт иметь к ним доступ и смогут выделять в запросах.

# Добавление/изменение именованных сущностей

Сейчас за распознавание именованных сущностей отвечает правило бегемота CustomEntities, туда и будем их подкладывать. Ниже описывается процесс добавления именованных сущностей. Аналогичные действия предполагаются и при последующем их изменении. Отдельно процесс изменения мы здесь описывать не будем.

* Добавить объявление сущности в [VinsProjectfile.json](https://a.yandex-team.ru/arc/trunk/arcadia/alice/vins/apps/personal_assistant/personal_assistant/config/scenarios/VinsProjectfile.json) в формате:
```json
{
    "entity": "имя_сущности",
    "path": "путь_к_определению_сущности"
}
```
Путь к определению сущности задается как `personal_assistant/config/scenarios/entities/имя_сущности.json`. Также можно указать флаг `"inflect": true|false` (склонять сущности по падежам, по умолчанию `true`) и `"inflect_numbers": true|false` (склонять сущности по числу, по умолчанию `false`). В описании существующих сущностей можно встретить флаг `"use_as_template"`, он поддерживается только для обратной совместимости и в новых сущностях не используется. Примеры объявления:
```json
{
    "entity": "сущность1",
    "path": "personal_assistant/config/scenarios/entities/сущность1.json",
    "inflect": false
}
```
```json
{
    "entity": "сущность2",
    "path": "personal_assistant/config/scenarios/entities/сущность2.json",
    "inflect_numbers": true
}
```
* Добавить путь к определению сущности в файл [entity_files.inc](https://a.yandex-team.ru/arc/trunk/arcadia/alice/vins/apps/personal_assistant/entity_files.inc).
Это необхдимо для того, чтобы система сборки могла реагировать на изменения в определении сущности.
* Добавить определение сущности в файл, который должен находиться по определенному выше пути. Формат описания:
```json
{
    "значение1": [
        "синоним1",
        "синоним2"
    ],
    "значение2": [
        "синоним1"
    ]
}
```
* [Переобучить](../tagger.md#obuchenie-teggera) использующие добавленные/измененные сущности теггеры.
