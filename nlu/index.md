# NLU Алисы

Natural language understanding предоставляет разборы (классификация и тегирование) и фичи. Разборы используются для
походов в сценарии, их наличие необходимо для формаирования запроса в сценарий. Фичи несут в себе дополнительную
информацию о запросе, которая не является активационной для сценариев, но которую они могут использовать.

Пример разбора: `'включи'(action) 'рок'(genre) 'вперемешку'(order)` для музыкального сценария.
Пример фичи: является ли запрос порнушным.


## Технологии NLU

Основной спектр задач решается следующими технологиями:
* [Granet](granet/index.md) - одновременная классификация и выделение слотов, правила описываются руками;
* [AliceTagger](tagger.md) - выделение слотов, необходимо собирать разметку запросов со слотами для обучения;
* AliceBinaryIntentClassifier - классификация интента против остальных запросов, необходимо собирать бинарную разметку;
   * [Beggins](intents/beggins.md) - классификация интентов с помощью эмбеддингов на основе Берта
   * [LSTM](intents/lstm.md) - то же, но на основе LSTM.
* AliceMultiIntentClassifier - классификация интентов друг против друга, необходимо собирать разметку на классы.
   * Для мультиклассификации используется [LSTM](intents/lstm.md).

Агрегировать базовые правила можно двумя способами:
* [AliceParsedFrames](frame_aggregator.md) - позволяет объединить разные технологии NLU в итоговый разбор для сценария;
* AliceNluFeatures - позволяет докинуть любой имеющийся запросный классификатор в сценарий.

Также есть инструмент майнинга похожих запросов из потока в интерактивном режиме: [verstehen](verstehen.md).
Может быть полезен для написания инструкций для толоки или сбора ханипотов для них.


## Методика выбора подходящей технологии

Как для любой задачи машинного обучения, для понимания запроса не существует верного решения. Общие рекомендации
примерно такие:
1. Разбор для сценария проще всего получать с помощью granet'а, этот способ стоит попробовать как минимум для отладки.
1. Для нечетких конструкций, похожих на запросы в поиск или адреса, granet вряд ли справится, лучше использовать
пару AliceTagger и AliceBinaryIntentClassifier/AliceMultiIntentClassifier, объединяя их с некотором порогом в
AliceParsedFrames.
1. Если для правильной классификации не хватает запроса и нужны данные от сценариев, стоит воспользоваться предыдущим
пунктом с достаточно слабым порогом + обучить постклассификатор (путь сложный, приходите за комментариями к
[@olegator](https://staff.yandex-team.ru/olegator)).

В случае возникновения вопросов, задавайте их в [чате поддержки](https://t.me/+DJryz0GP71djN2Ri).
