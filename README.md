# Stack Overflow
Проектирование высоконагруженных систем - курсовая работа

## Содержание
1. [Тема и целевая аудитория](#1-тема-и-целевая-аудитория)
2. [Расчет нагрузки](#2-расчет-нагрузки)
3. [Глобальная балансировка нагрузки](#3-глобальная-балансировка-нагрузки)


## 1. Тема и целевая аудитория 

### 1.1 Тема 
[Stack Overflow](https://stackoverflow.com/) — система вопросов и ответов о программировании

### 1.2 MVP

- Регистрация / Авторизация
- Теги вопросов
- Создание вопроса
- Добавление ответа
- Комментирование ответов и вопросов
- Оценка (лайки и дизлайки)
- Картинки

### 1.3 Целевая аудитория 

Посещений в месяц - 100 млн

| Страна            | Процент пользователей |
| ----------------- | --------------------- |
| Соединенные Штаты | 20.31%                |
| Индия             | 9.57%                 |
| Великобритания    | 3.50%                 |
| Германия          | 3.29%                 |
| Другие            | 59.33%                |


## 2. Расчет нагрузки 

### 2.1 Продуктовые метрики 

Данные взяты с StackOverflow[[1]](https://stackoverflow.com/) и SignHouse[[2]](https://www.usesignhouse.com/blog/stack-overflow-stats)

- В среднем один вопрос или один ответ имеют размер 1000 символов - 1 КБ
- В среднем один вопрос или один ответ оценивают 50 раз
- В среднем один вопрос или один ответ комментируют 2 раза, комментарий в среднем занимает 0.25 КБ
- Картинки встречаются редко, поэтому примем 1 картинку на 50 вопросов и ответов, картинка в среднем занимает 0.5 МБ

| Метрика                               | Показатель |
| ------------------------------------- | ---------- |
| Посещений в месяц                     | 100 млн    |
| Посещений в день                      | 8.8 млн    |
| Средняя продолжительность посещения   | 7:21 мин   |
| Среднее количество посещенных страниц | 2.47       |
| Количество вопросов                   | 23 млн     |
| Количество вопросов в минуту          | 4          |
| Вопросы / Ответы                      | 0.67       |
| Количество ответов в минуту           | 6          |
| Количество ответов                    | 34 млн     |
| Количество комментариев               | 114 млн    |
| Количество оценок                     | 2'850 млн  |
| Количество картинок                   | 1.15 млн   |

### 2.2 Технические метрики

- Создание вопроса: 4 / 60 = 0.066 RPS
- Добавление ответа: 6 / 60 = 0.1 RPS
- Добавление комментариев: (4 + 6) * 2 / 60 = 0.33
- Лайки и дизлайки: (4 + 6) * 50 / 60 = 8.33 RPS
- Просмотр страниц с вопросами: 8.8 * 10<sup>6</sup> * 2.47 / (24 * 60 * 60) = 252 RPS

| Тип нагрузки      | RPS   |
| ----------------- | ----- |
| Просмотр вопросов | 252   |
| Добавление оценки | 8.33  |
| Добавление ответа | 0.1   |
| Создание вопроса  | 0.066 |

### 2.3 Размер хранения данных

- Объем хранения вопросов: 23 млн КБ = 21.93 ГБ
- Объем хранения ответов: 34 млн КБ = 32.42 ГБ
- Объем хранения ответов: (23 + 34) * 10<sup>6</sup> * 0.25 КБ = 14'250'000 КБ = 13.59 ГБ
- Объем хранения картинок: (23 + 34) * 10<sup>6</sup> / 50 * 0.5 МБ = 570'000 МБ = 556.64 ГБ
- Объем хранения оценок: 2'850 * 10<sup>6</sup> * 12 Б = 31.85 ГБ 

### 2.4 Cетевой трафик

Обычно просматривают страницы с большим рейтингом, где ответов в среднем больше в 6 раза = 9 ответов на вопрос 

- Трафик вопросов: RPS * 1 * 8 Кбит = 2016 Кбит/с = 2 Мбит/с
- Трафик ответов: RPS * 9 * 8 Кбит = 18'144 Кбит/с = 18 Мбит/с
- Трафик комментариев: RPS * (1 + 9) * 2 Кбит = 5'030 Кбит/с = 5 Мбит/с
- Трафик картинок: RPS * ((1 + 9) / 50) * 4 Мбит = 201.6 Мбит/с
- Трафик статики страницы(html + css + js + img): RPS * (40 КБ + (85 + 58 + 5) КБ + (34 + 26 + 19 + 90 + 21 + 81 + 5 + 58) КБ + 400 КБ) * 8 = 1'858'752 Кбит/с = 1'815 Мбит/с
- Трафик оценок = 252 * (1 + 9) * 50 * 12 Б = 1477 Кбит/с = 1.5 Мбит/с

Точный коэффициент в пике найти не удалось, поэтому примем его равным 1.7

| Тип данных  | Средний трафик, Мбит/с | Пиковый трафик, Мбит/с |
| ----------- | ---------------------- | ---------------------- |
| Вопросы     | 2                      | 3.4                    |
| Ответы      | 18                     | 30.6                   |
| Комментарии | 5                      | 8.6                    |
| Картинки    | 201.6                  | 342.7                  |
| Статика     | 1'815                  | 3'085.5                |
| Оценки      | 1.5                    | 2.6                    |


## 3. Глобальная балансировка нагрузки

### 3.1 Физическое расположение датацентров
Северная Америка - 3 ДЦ (1 в Канаде, 2 в Соединенных Штатах)
Южная Америка - 1 ДЦ
Европа - 4 ДЦ
Азия - 2 ДЦ (1 в Индии)
Австралия - 1ДЦ
Африка - ?


### 3.2 Схема балансировки
- Определяем регион через Geo-based DNS
- В регионе определяем ближайший ДЦ через BGP Anycast


## Источники 

1. https://stackoverflow.com/
2. https://www.usesignhouse.com/blog/stack-overflow-stats
