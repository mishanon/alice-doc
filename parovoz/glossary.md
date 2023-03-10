# Глоссарий

**Паровоз**

Технология, с помощью которой разные сценарии Алисы можно объединить в одну цепочку воспроизведения. Для пользователя это выглядит как единый сценарий. Паровоз является как частью [Мегамайнда](../megamind/index.md), так и частью клиента.

**Directive/Директива**

Инструкция, которую нужно выполнить в приложении. На слэнге авторов и на некоторых вики-страничках можно встретить слово "вагон".

**Channel/Канал**

Контекст исполнения. У каждого канала есть приоритет и тип, который зависит от контекста. Подробнее в разделе [Каналы](components-client.md).

**Directive Sequencer/ Распределитель директив**

Точка синхронизации директив. Распределеяет директивы по каналам. На слэнге авторов и на некоторых вики-страничках можно встретить слово "стрелочник". Подробнее в разделе [Directive Sequencer](components-client.md).

**StackEngine**

Компонент, который реализует обмен сообщениями между сценариями. Управление StackEngine осуществляется через специальные команды. Подробнее в разделе [StackEngine](components-backend.md).

**Context Storage**

База, в которую записывается состояние диалога ("контекст") в виде пары "user_id, megamind_state". user_id является ключом, megamind_state — состояние диалога (в него входят состояния сценариев и стек).

**Begemot/Бегемот**

Компонент Алисы, который отвечает за NLU (natural language understanding).

**Megamind/Мегамайнд**

Выполняет диспетчерскую функцию, делегируя дополнительным компонентам разбор реплик (NLU), выбор оптимального сценария разговора и обработку конкретных данных, полученных от пользователя. Поэтому Мегамайнд — центральная часть архитектуры сценариев Алисы. Подробнее в разделе [Что такое Мегамайнд](../megamind/index.md)

**Uniproxy**

Компонент, который обеспечивает взаимодействие между клиентом и всеми backend. Является входной точкой во все backend.

**Frame/Фрейм**

Структурированное представление содержания диалога. Фрейм является результатом разбора реплики в Begemot и передается в запросе от Мегамайнда к сценарию.


 
