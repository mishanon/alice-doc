# Диспетчер

## Описание

Диспетчер — основной класс сценария и его главная функция. Получает управление каждый раз, когда пользователь обращается к Алисе с запросом.

В задачи диспетчера входят следующие действия:
* проанализировать входные параметры запроса;
* определить релевантность запроса;
* определить, содержит ли запрос нужные данные.

## Аргументы функции

Диспетчер является функцией-членом класса `TMyScenario : public NAlice::NHollywoodFw::TScenario`.

```cpp
class TMyScenario : public TScenario {
public:
    TMyScenario();
    TRetScene Dispatch(const TRunRequest& runRequest, const TStorage& storage, const TSource& source) const;
};
```

* `const TRunRequest` — указатель на общий блок входных данных. Содержит всю информацию о входном запросе.
* `const TStorage` — указатель на долговременное хранилище. Отсюда можно извлечь данные `memento` и `scenario state`.
* `const TSource` — данные ответа источников.

## Результат диспетчера

Метод `Dispatch()` анализирует запрос и возвращает результат через `TRetScene` (`std::variant`).

| Результат | Описание |
| ----------- | ----------- | 
| [TReturnValueScene](#valuescene)  | Диспетчер обработал входящие параметры, выбрал сцену для дальнейшей работы и готов передать ей управление. |
| [TReturnValueRenderIrrelevant](#valuerenderirrelevant) | Диспетчер не смог распознать входящие параметры как корректные, будет возвращен нерелевантный ответ. |
|[TError](#terror)  | В ходе работы диспетчера произошла невосстановимая ошибка. |
| [TReturnValueDo](#valuedo) | Надо переключиться на старый вариант работы сценария. |

### Релевантный ответ {#valuescene}

В случае, если диспетчер проанализировал данные из `TRunRequest` и классифицировал запрос как релевантный, то далее он:
1. Выбирает сцену для дальнейшей обработки запроса.
2. Формирует протобаф с аргументами, которые будут переданы в сцену. Как правило, протобаф включает препроцессированные данные из семантик фреймов, свойства поверхности и другие параметры.
3. Возвращает код `TReturnValueScene(pointerToSceneFunction, SceneArgumentsProto, SemanticFrameName);`.

{% cut "Пример" %}

Исходная сцена:

```cpp
class TMyScene1 : public TScene<TMySceneProto> {
public:
    TMyScene1(const TScenario* owner);
    TRetMain Main(const TMySceneProto& sceneArgs, const TRunRequest& request, TStorage& storage, const TSource& source) const override;
};
```

Чтобы передать управление сцене из диспетчера, надо написать:

```cpp
TRetScene TMyScenario::Dispatch(const TRunRequest&, const TStorage&, const TSource&) const {
    ...
    TMySceneProto mySceneProto;
    ... // заполняем mySceneProto данными, которые нужны сцене
    return TReturnValueScene(&TMyScene1::Main, mySceneProto, "my_semantic_frame_name");
    
}
```

Аргументы, которые были заполнены в `mySceneProto`, передаются первым аргументом в функцию `Main()`.

Третий аргумент функции опционален и предназначен только для использования в стандартной [аналитике](../main/analitics.md).

{% endcut %}


### Нерелевантный ответ {#valuerenderirrelevant}

Если диспетчер не смог классифицировать запрос как корректный, то он возвращает нерелевантный ответ одним из [рендеров](renderer.md).:
* стандартным рендером;
* функцией своего рендера.

#### Стандартный рендер

```cpp
TReturnValueRenderIrrelevant(const TString& nlgName, const TString& phrase, const google::protobuf::Message& context = {});
```

Вызывает стандартный рендер фреймворка. Он готовит текстовый или голосовой ответ из скомпилированного NLG и возвращает ответ пользователю. Обычно используется один из стандартных ответов, например «Вы нашли во мне ошибку», но можно передать контекст для подстановки во фразы. Подробнее см. в разделе [NLG](../nlg/intro.md).

#### Свой рендер

```cpp
TReturnValueRenderIrrelevant(pointerToSceneFn, Protobuf)
```

Для нерелевантного ответа со своим рендером возвращается:
* указатель на функцию рендера;
* аргументы рендера в протобафе.


{% note warning %}

Даже если ваш сценарий отвечает иррелевантом, предоставьте текст для ответа. Этот ответ будет использован, если ни один из других сценариев не дал релевантного ответа.

Получить `Irrelevant`-результат в Hollywood Framework можно только через `TReturnValueRenderIrrelevant`. Прямой доступ к протобафам ответа закрыт.

{% endnote %}

### Ошибка {#terror}

`TError` (или бросающий исключение макрос `HW_ERROR()`) позволяет вернуть из любых функций сценария ошибку. Сообщение об ошибке передается в ММ, сценарий автоматически отвечает иррелевантом.

Применяйте `TError` и `HW_ERROR` только в крайних случаях, когда нет возможности восстановить работу сценария другими способами. Например, если глубоко внутри стандартных библиотек `alice/library` обнаружена невосстановимая ошибка.

### Переключение на традиционный способ {#valuedo}

Код возврата используется только в процессе переноса традиционных сценариев на Hollywood Framework, подробнее см. в разделе [Прямая и обратная совместимость сценариев](../compatibility/intro.md).