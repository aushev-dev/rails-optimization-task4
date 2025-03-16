# Задание 4

## Актуальная проблема
В проекте dev.to выявлена проблема производительности главной страницы:
- Медленный рендеринг главной страницы (StoriesController#index) (Особенно затратный рендеринг partial-ов _single_story.html.erb)
- Отсутствие кэширования страниц

## Формирование метрик
Для оценки эффективности оптимизации определены следующие метрики:

- Время полной загрузки главной страницы
- Время рендеринга partial _single_story.html.erb
- Количество запросов к БД при рендеринге страницы
- Использование CPU и памяти
- Включил кеширование на локальном окружении
- Использование `benchmark` с помощью `ab` (`ab -n 100 -c 5 http://localhost:3000/`)
- Добавил local_production окружение

## Feedback-Loop
Построен быстрый цикл обратной связи:

- NewRelic APM для мониторинга метрик
- rack-mini-profiler для профилирования рендеринга
- Поиск точек роста

## Использованы инструменты профилирования:

- NewRelic для анализа узких мест
- rack-mini-profiler для детального профилирования рендеринга
- Логи Rails для анализа SQL-запросов

## Результаты оптимизации:

### 1. Отсутствие кеширования partial-ов _single_story.html.erb
- Readme задания и rack-mini-profiler
- Добавил кеширование partial'а, учел, что в него входят счётчики лайков и комментариев.
### Замеры до добавления кеширования:
```
Concurrency Level:      5
Time taken for tests:   14.988 seconds
Complete requests:      100
Failed requests:        0
Total transferred:      13161800 bytes
HTML transferred:       13115100 bytes
Requests per second:    6.67 [#/sec] (mean)
Time per request:       749.387 [ms] (mean)
Time per request:       149.877 [ms] (mean, across all concurrent requests)
Transfer rate:          857.59 [Kbytes/sec] received
```

### Замеры после добавления кеширования:
```
Concurrency Level:      5
Time taken for tests:   7.179 seconds
Complete requests:      100
Failed requests:        0
Total transferred:      13028000 bytes
HTML transferred:       12981300 bytes
Requests per second:    13.93 [#/sec] (mean)
Time per request:       358.953 [ms] (mean)
Time per request:       71.791 [ms] (mean, across all concurrent requests)
Transfer rate:          1772.19 [Kbytes/sec] received
```
- Время обработки запросов сократилось в два раза


