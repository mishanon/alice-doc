## Ранжирование построллов
Сейчас для построллов включено ранжирование по приносимому таймспенту, ctr и другим статистикам.
Это означает, что на каждый запрос все доступные построллы (те, у которых выполняются все Condition-ы и прошло достаточно времени с предыдущего показа)
ранжируются по заданной формуле и в ответ отбирается один постролл с максимальным скором.

На данный момент часть статистик в формуле обновляется автоматически, часть -- считается руками и лежит в файлике [info.json](https://a.yandex-team.ru/arc/trunk/arcadia/dj/services/alisa_skills/server/data/proto_items/info.json).

Формула состоит из трех частей, приоритеты которых меняются в зависимости от запроса. Упрощенно она имеет следующий вид:
```
score = isNew * coreSpuScore + (1 - isNew) * timespentScore + W * isMarketingRequest * MarketingScore
```

* **CoreSpu часть.** Вычисляется по ctr и весу сценария в CoreSpu. Имеет наибольший вес на новичковых запросах.
* **Маркетинговая часть.** Вычсиляется по одному числу -- ```MarketingScore``` , которое проставлется руками при заведении постролла. 
Имеет наибольший вес, если запрос попал в маркетинговую квоту. Сейчас это 10% запросов в сервис.
* **Таймспентная часть.** Это основная часть скора, на большинстве запросов (не новичковые запросы и не из маркетинговой квоты) построллы ранжируются по ней.
В ней учитываются ctr и приносимый построллом таймспент. 

### Что нужно для ранжирования вашего постролла
* Для начала нужно понять, какой эффект вы ожидаете от постролла:
   * Рост таймспента (например построллы УШ).
   * Рост CoreSpu (вариант скорее для новичков, например построллы музыкального будильника)
   * Рост аудитории вашего сценария (например требуется порекламировать новый внешний навык или тематические плейлисты в музыке).
* Для ранжирования по таймспенту в постролл нужно проставить ```PriorScore``` и [таймспентный скор](https://a.yandex-team.ru/arc/trunk/arcadia/dj/services/alisa_skills/server/data/proto_items/info.json?rev=r8926413#L971).
Пока что эти числа приходится выбирать руками, чтобы понять, какое нужно вам, можно спросить у [@karina-usm](https://staff.yandex-team.ru/karina-usm).
* Для ранжировния по CoreSpu нужно проставить ```PriorScore``` и coreSpu веса внутри [```CoreSpuData```](https://a.yandex-team.ru/arc/trunk/arcadia/dj/services/alisa_skills/server/data/proto_items/bases.pb.txt?rev=r8947585#L72).
Вес сценария в coreSpu можно посмотреть на [страничке](https://wiki.yandex-team.ru/alice/analytics/corespu/#vesascenariev). 
`CoreSpuData.PriorScore` -- оценка ctr постролла среди новичков, для новых построллов его можно выбирать, ориентируясь на значения у соседних построллов.
* Если у вашего постролла мало шансов на показы в ранжировании по таймспенту и ctr, 
но он нужен для каких-то маркетинговых активностей, то можно добавить постролл в маркетинговую квоту.
    * Для этого достаточно проставить построллу ненулевой `MarketingScore`.
    * Сейчас у всех построллов в квоте одинаковый `MarketingScore=1`, то есть внутри квоты ранжирование рандомное.
    
Важно понимать, что добавление постролла в маркетинговую квоту не исключает его показов вне квоты (при ранжировании по таймспенту или coreSpu), и наоборот,
отсутствие у постролла `MarketingScore` не означает, что постролл вообще не будет показываться в маркетинговой квоте.
Например, если на запросе в маркетинговой квоте не нашллось ни одного доступного маркетингового постролла, 
то будет показан не маркетинговый постролл с `MarketingScore=0`.


