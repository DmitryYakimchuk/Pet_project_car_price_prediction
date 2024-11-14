# Прогнозирование стоимости автомобиля по характеристикам (Car Price prediction)

## Цель pet-проекта: 
Создать модель, которая будет предсказывать стоимость автомобиля по его характеристикам, в результате чего отработать навыки предобработки данных и их анализа.


## Задачи проекта:
1. Собрать данные с внешних источников (см. отдельный Парсер Auto-ru.ipynb);
2. Обработать собранный датасет;
3. Сделать разведовательный анализ данных (EDA);
4. Подобрать наилучшую модель, предсказывающую стоимость автомобиля, а также оптимальные параметры модели.


## Краткая информация о данных
- brand - Брэнд автомобиля
- car_url - Адрес страницы, с которой взята информация
- color - Цвет автомобиля
- complectation_dict - Различные характеристики авто
- description - Описание обьявления на сайте
- engineDisplacement - Объем двигателя
- enginePower - Мощность двигателя
- equipment_dict - Характеристики атомобиля
- fuelType - Тип топлива
- image - Изображение авто
- mileage - Пробег
- modelDate - Год модели
- model_info - Информация о модели авто
- model_name - Модель
- name - Мощности и обьема двигателя
- numberOfDoors - Количество дверей
- parsing_unixtime - Время парсинга
- priceCurrency - Валюта стоимости
- productionDate - Год выпуска
- sell_id - id
- super_gen - Технические характеристики авто
- vehicleConfiguration - Характеристики коробки передач и мощности
- vehicleTransmission - Трансмиссия автомобиля
- vendor - Продавей
- Владельцы - Количество владельцев
- Владение - Время владения
- ПТС - Характеристика ПТС
- Привод - Привод
- Руль - Расположение руля
- Состояние - Состояние авто
- Таможня - Растаможен ли автомобиль
- Price - Цена автомобиля в объявлении


## Информация об обработке данных по следующим признакам:
- brand - оставлен как есть
- car_url - удален
- color - в тестовой части оставлен как есть, в тренировочных данных перекодирован с цифрового формата в строковый
- complectation_dict - Различные характеристики авто
- description - удален
- engineDisplacement - выделили из строки объем двигателя
- enginePower - выделили из строки мощность двигателя
- equipment_dict - удален
- fuelType - оставлен как есть
- image - удален
- mileage - оставлен как есть
- modelDate - оставлен как есть
- model_info - удален
- model_name - оставлен как есть
- name - удален
- numberOfDoors - оставлен как есть
- parsing_unixtime - удален
- priceCurrency - удален
- productionDate - оставлен как есть
- sell_id - оставлен как есть
- super_gen - выделили из данного столбца 6 новых столбцов с характеристиками техническими авто
- vehicleConfiguration - удален
- vehicleTransmission - оставлен как есть в трейне, в тесте переведены характеристики на английский язык
- vendor - оставлен как есть
- Владельцы - выделены числовые данные из данного признака
- Владение - удален
- ПТС - переведены характеристики на английский язык
- Привод - переведены характеристики на английский язык
- Руль - переведены характеристики на английский язык и значения сменили на 1 и 0
- Состояние - удален
- Таможня - удален
- Price - оставлен как есть


## Созданные новые признаки (Feature engineering):
- Сильно коррелировали признаки productionDate и modelDate. Для того что бы оставить оба, создали новый признак из разницы этих двух и результат поместили в столбец  modelDate (по сути когда после выхода модели она была куплена).
- Сильно коррелирующие признаки enginePower и engineDisplacement не удалось обработать по схожему принципу, были попытки создать что-то на подобие удельной мощности на обьем двигателя, но все ухудшало модель и пришлось оставить как есть.
- Опытным путем было выявлено, что признак color никак не влияет на целевую переменную, также как и признак vendor, они не вошли в финальную таблицу для обучения.
- Не удалось достаточно обработать признак model_name, так как в данных было более 500 различных моделей, сделать из этого признака dummie переменную не лучший выход, были попытки разделить все марки в зависимости от цены на 4 категории, что привело к ухудшению метрики, так как была задействована целевая переменная в создании такого признака. В итоге единственно возможным вариантом стало заменить данные в столбце на количество моделей в выборке.
- Были попытки сократить количество уникальных значений в столбце brand, но также разделение машин на классы ухудшило метрику и лучшим вариантом для данного столбца оказались dummie переменные из всех уникальных значений. 
- Столбец bodyType наоборот показал лучшее влияние на целевую переменную после сокращения уникальных значений.
- Предпринята попытка заменить год в столбце prodactionDate на разницу между годом сбора данных и фактическим, что казалось достаточно логичным решением, но такие действия ухудшили метрику, и оптимальным вариантом оказалось отнимать фактический год производства от 2020 года. 
- Столбец equipment_dict содержал много полезной информации, но к сожалению, присуствовало очень много пропусков, и все попытки как-то использовать его данные привели к ухудшению метрики, и было принято решение отказаться от данного признака.

Можно сделалать промежуточный вывод по генерации новых признаков в данном проекте, касаемо нашего случая, наших данных и наших моделей. Практически никакие новые признаки не улучшали модель, но при этом могли сильно ухудшить. Для генерации новых признаков в основном мы использовали методы группировок или соединения признаков в один. Лучший результат на тесте был получен на базовых признаках и их dummies переменных.


## Разведывательный анализ данных
### Выводы после быстрого осмотра данных

1. По типу кузова большинство машин относится к 5-дверному типу и аналогу - седану
2. По марке авто имеем несбалансированную выборку: MERCEDES представлена в бОльшем количестве (5317), чем остальные; LEXUS, HONDA и INFINITI имееются в количестве менее 1000 шт., остальные больше 1000 шт.
3. Значительно преобладают машины с чёрным цветом. Примерно в два раза меньше машин белого цвета. Реже встречаются автомобили других цветов.
4. Объем двигателя в объявлениях сконцентрирован в промежутке от 1.5 до 3. Остальные значения более редки.
5. Признак мощности двигателя напоминает Пуассоновское распределение. Возможно, стоит прологарифмировать данный признак.
6. В датасете представлено мало автомобилей с гибридным типом топлива, электромашин, а также типа LPG. Преобладают автомобили на газу, бензине и дизеле.
7. Обычно пробег машин не превышает 400000 км, однако имеются и слишком изношенные. Возможно, стоит прологарифмировать признак, чтобы избавится от "жирого хвоста".
8. По количеству лет со дня выпуска большинство автомобилей не старше 30-40 лет, однако есть исключения до 80 лет. Возможно, стоит прологарифмировать данный признак.
9. Европейских автомобилей представлено в 1.6 раза больше, чем японских.
10. Признак "разгон" имеет вид нормального распределения, однако имеется "жирный хвост". Возможно, стоит прологарифмировать данный признак.
10. Признак "клиренс" имеет вид нормального (или бимодального?) распределения, однако имеется жирный хвост. Возможно, стоит прологарифмировать данный признак.
11. "Потребление топлива" на 100 км обычно не превышает 10 л, однако имеется "жирный хвост". Возможно, стоит прологарифмировать данный признак.
12. По типу привода большинство машин полноприводные или переднеприводные, остальные представлены реже.
13. У большинства авто трансмиссия автоматическая. 
14. Количество владельцев у представленых автомобилей не превышает 4.
15. Большинство авто имеют оригинальный ПТС.
16. Как и ожидалось, левостороннее расположение руля превалирует в выборке, однако присутствуют и правосторонние в количестве 1108, что примерно в 30 раз меньше. В этом отношении выборка сильно несбалансирована.
17. Цена имеет "жирный хвост" и данный признак нуждается в логарифмировании.

### Выводы по результатам корреляционного анализа:
1. Сильно скоррелированных признаков (0.9 и более) не представлено
2. Наибольшая связь представлена между признаками "Объем двигателя", "Мощность двигателя" и "Потребление топлива", что логично.
3. Присутствует заметная отрицательная корреляция между "Разгоном" и ("Объем двигателя", "Мощность двигателя")
4. Касательно связи признаков с целевой переменной (ценой), то все признаки, за исключением "Количества дверей" и "Потреблением топлива" имеет выраженную корреляцию, как положительную, так и отрицательную.
5. Отметим, что признак "Количество дверей" имеет нелинейную связь с ценой.

### Анализ номинативных признаков
1. Обычно наиболее дорогие авто - авто класса "LUXURY", что отражает это название (логично). Самые бюджетными классами являются "WAGON" и "HATCHBACK". Но это разделение не носит ярковыраженного характера.
2. Тройка дорогих марок - BMW, MERCEDES и LEXUS. Бюджетные - VOLKSWAGEN и HONDA.
3. Предсказуемо чёрные авто обычно самые дорогие, но также и белые недалеко по медианной цене. Последнее удивило.
4. Автомобили на дизиле как правило более дорогие.
5. За японские машины обычно просят меньшую цену.
6. Полноприводные автомобили как правило имеют более высокую цену, в сравнении с другими.
7. Машины с механической коробкой передач заметно дешевле других типов.
8. Авто с 3 дверьми стоят недорого по сравнению с другими вариантами. Интересно, что двухдверные машины обычно имеют самую высокую цену. Скорее всего, двухдверные авто являются спорткарами или им подобными.
9. Цена авто обратнозависима от количества владельцев, что логично.
10. Машины с отсутствием оригинального ПТС заметно дешевле.
11. Расположение руля также вносит серьёзный вклад в цену. Левостороннее расположение как правило дороже.

### t-test номинативных и смешанных переменных
С помощью теста Стьюдента проверено есть ли статистическая разница в распределении оценок по номинативным признакам (проверена нулевая гипотеза о том, что распределение цены автомобиля в зависимости от уровней категорий неразличимы):

Найдены статистически значимые различия для признаков: bodyType, brand, color, fuelType, vendor, gearType, transmission, numberOfDoors, owners_number, original_registration, steering_wheel.


## Подготовка данных к машинному обучению
* Данные для обучения были разделены на обучающие и валидационные с применением метода train_test_split. Валидационная выборка составила 15%.
* Все категориальные признаки переведены в числовой вид.
* Стандартизация и/или нормализация к улучшению не приводила, потому что использовались модели, основанные на деревьях решений.


## Признаки, которые использовались для построения модели после отбора:
 * engineDisplacement          
 * enginePower                
 * mileage                     
 * numberOfDoors               
 * productionDate                   
 * acceleration              
 * clearance_min            
 * fuel_rate                 
 * owners_number              
 * original_registration
 * steering_wheel 
 * sample
 * price
 * bodyType
 * fuelType
 * brand
 * transmission


## Обучение
Машинное обучение (ML) проводилось с применением широкого ряда моделей: LinearRegression, DecisionTreeRegressor, BaggingRegressor, RandomForestRegressor, AdaBoostRegressor, GradientBoostingRegressor, CatBoostRegressor, LGBMRegressor.  Установлено, что наилучший результат по метрике MAPE (14.67510) на тестовой выборке показывает модель CatBoostRegressor с параметрами (iterations = 5000,random_state=42, custom_metric=['R2', 'MAE'],silent=True)

### Стэкинг
Было перепробовано множество вариаций стекинга, представленных выше моделей, но по всей видимости из-за того, что большинство моделей (за исключением линейной регрессии) одной природы, т.е. построены на деревьях, они не выдают результат лучше, чем отдельные модели. Сама же линейная регрессия выдаёт очень плохой результат. Соответственно, её наличие в базисных моделях ни к чему хорошему не приводит.

### Подбор параметров для моделей
Для моделей проводился подбор наилучших параметров по сетке GridSearchCV. К сожалению, данный способ занимал слишком много времени и самое неприятное то, что он не привнёс положительных результатов. Гораздо быстрее и эффективнее был ручной перебор параметров для всех моделей. Также использовалась optuna для подбора параметров, но и в этом случае результат приходилоьс ждать слишком долго и эффект был скорее отрицательным. В ноутбуке представлен код перебора параметров по сетке GridSearchCV с использованием небольшого числа узлов сетки на примере модели GradientBoostingRegressor. Другие модели при подборе параметров также не продемонстрировали удовлетворительного результата.


## Выводы:
1. Собраны и предварительно проанализированы данные с сайта  auto.ru (смотреть файл Парсер Auto-ru.ipynb);
2. Собранные и тестовые данные одинаково предобработаны;
3. Проведен разведовательный анализ обработанных данных (EDA), по результатам которого было выявлено, что практически все признаки являются значимыми (подробнее в разделе EDA);
4. Машинное обучение (ML) проводилось с применением широкого ряда моделей: LinearRegression, DecisionTreeRegressor, BaggingRegressor, RandomForestRegressor, AdaBoostRegressor, GradientBoostingRegressor, CatBoostRegressor, LGBMRegressor. Разнообразный стекинг не показал улучшений по целевой метрике. Установлено, что наилучший результат по метрике MAPE (14.67510) на тестовой выборке показывает модель CatBoostRegressor с параметрами (iterations = 5000,random_state=42, custom_metric=['R2', 'MAE'],silent=True)
5. Работа с выбросами не привела к улучшению итоговой метрики. Улучшения появлялись только на алгоритмах с изначально неудовлетворительной метрикой.
6. Таким образом, выявлено, что с применением наилучшего алгоритма для данной задачи `CatBoostRegressor` возможно предсказать стоимость автомобиля со средней точностью до 15%.
