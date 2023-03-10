# Разработка новых сценариев

Чтобы обеспечить устойчивость системы при выкатках, пользуйтесь имеющейся функциональностью Hollywood Framework для обновления и развития сценариев.

## Добавить сцену {#add_scenes}

Добавление новых сцен в сценарий на Hollywood Framework не требует дополнительных операций.
Новые сцены могут быть добавлены в любой момент:

1. Добавьте новую сцену [под флагом эксперимента](../../hollywood/main/flags.md) `IsExperimentEnabled()`.
2. После окончания разработки и проведения тестирования раскатите флаг на 100% пользователей.
3. После раскатывания удалите из исходного кода условия, использующие флаг эксперимента. Сценарий идет в полноценный продакшен.


{% cut "Пример кода новой сцены в диспетчере" %}

```cpp
TRetScene TMyScenario::Dispatch(const TRunRequest& runRequest, const TStorage&, const TSource&) const {
    //
    // Тут располагается старый код по выбору существующих сцен
    //
    ....
    //
    // Добавляем логику обработки новой сцены
    //
    if (runRequest.Flags().IsExperimentEnabled("my_scenario_new_scene_flag")) {
        ... Проверяем условия для выбора новой сцены (семантик фреймы и т.п.)
        
        return TReturnValueScene<TMyNewScene>(MyNewSceneArgs, intentName);
    }
    
    // Не нашли ничего, возвращаем иррелевант
    return TReturnValueRenderIrrelevant(&RenderIrrelevant, {});
}
```

Далее можно писать код в `TMyNewScene` и тестировать её под флагом эксперимента `my_scenario_new_scene_flag`.

{% endcut %}


## Удалить сцену {#del_scenes}

Удаление сцен из действующих сценариев может понадобиться, например, при разбиении сценария на несколько частей.

Удаление сцены похоже на [добавление сцены](#add_scenes), выполняемое в обратном порядке.

1. Заведите эксперимент и раскатайте его на всех пользователей.
1. Обеспечьте выбор сцены в диспетчере только при условии наличия флага эксперимента.
1. Выключите эксперимент на продакшене.
1. Удалите код ненужных сцен и условия, использующие этот флаг эксперимента, из сценария.


## Переименовать сцену {#rename_scenes}

Не переименовывайте сцены в Hollywood Framework. Вместо этого [добавьте новую сцену](#add_scenes), а затем [удалите существующую сцену](#del_scenes).

При изменении сцен изменятся счетчики генераторов случайных чисел, и соответственно, потребуется реканонизация IT2 и правки по EVO.


## Работа с протобафами {#protos}

Работа с протобафами в Hollywood Framework ведется по общим правилам Алисы:

* Не допускаются изменения классов, которые описывают аргументы сцен и рендеров.
* При необходимости можно добавлять новые поля.
* При необходимости можно убрать в резерв ставшие ненужными поля при помощи описания прото-файлов:
	```
	reserved 1;
	reserved "MyFieldName";
	```
