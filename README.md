# Проект: Анализ активности и эффективности работы онлайн-сервиса Mentorhub

## Описание бизнес – задачи
Продакт-менеджер Иван начал подготовку к новому этапу развития приложения онлайн-сервиса «Mentorhub». Это сервис, который помогает менторам и менти найти друг друга. Встречи проходят на площадке сервиса. Назначить встречу можно в личном кабинете после авторизации на сайте. Иван попросил собрать данные о работе сервиса и ответить на следующие вопросы:
1)	Какое количество менторов и менти взаимодействуют каждый месяц на нашей платформе? Как меняется этот показатель из месяца в месяц?
2)	Сколько на платформе менторов и менти, которые еще не приняли участие ни в одной встрече? Учитывать тех пользователей, кто ни разу не назначал себе встречи.
3)	Сколько у каждого ментора в среднем успешных сессий в неделю по месяцам? Как меняется частота встреч в неделю от месяца к месяцу?
4)	Определить ТОП-5 менторов с самым большим числом сессий за последний полный месяц.
5)	Сколько времени в среднем проходит между менторскими встречами у одного менти? Ментора?
6)	Сколько сессий по каждому направлению менторства в месяц обычно отменяется? Как меняется доля отмененных сессий помесячно?
7)	Определить самый загруженный день недели для каждого направления менторства.
8)	Спрогнозировать, сколько новых менторов нужно найти, если в следующем месяце количество активных менти увеличится на 500 человек, учитывая, что занятость новых менторов будет такой же, как у текущих.

## Как я решала эту задачу
Для обработки и анализа данных я использовала SQL, работала в PostgreSQL.

**1 Этап. Подключение к базе данных (Академии Eduson).**
* database: ******
* user: ******
* password: *******
* host: ******
* port: ******  
В базе были четыре таблицы:   
*	sessions со столбцами: session_id, sassion_date_time, mentor_id, mentee_id, session_status, mentor_domai_id;
*	users со столбцами: user_id, reg_date, role, region_id;
*	region со столбцами: id, name;
*	domain со столбцами: id, name.

**2 Этап. Исследование данных.**
С помощью SQL- запросов изучила данные:  
1)	посмотрела, какие в базе есть таблицы и данные в них;
2)	проверила данные таблиц на пустые значения и дубликаты;
3)	проверила, сколько зарегистрированных менти и менторов на платформе;
4)	проверила сессии на уникальность id и посчитала общее количество сессий;
5)	убедилась, что все менти и менторы, участвовавшие в сессиях, есть в таблице пользователей.  
По результатам 1 -го этапа я выяснила, что пустых значений и дубликатов нет. Количество менти – 2509 человек, менторов – 623. Все сессии уникальны, всего 6524 сессии. Все пользователи есть в таблице.

**3 Этап. Проведение анализа (ответы на поставленные вопросы).**  
Для ответов на вопросы продакт – менеджера я использовала в SQL- запросах CTE (временные таблицы), агрегирующие функции: COUNT, SUM, AVG, MIN, оконные функции: LAG, ROW_NUMBER, DENSE_RANK, функции даты и времени: DATE_TRUNC, EXTRACT, логические функции: CASE, COALESCE,   строковую функцию TO_CHAR, числовую функцию ROUND, а также объединение таблиц с помощью JOIN, представления  с помощью CREATE VIEW.

## Код
Код можно посмотреть здесь: [code](./script/code)   

## Результаты

**1.**	Количество менти и менторов каждый месяц имеет разные показатели, например, в первые два месяца работы платформы число пользователей было совсем небольшим:  по 3 ментора и по 3 менти. Но уже через полгода на платформе взаимодействовали 53 ментора и 56 менторов. В последующие периоды также наблюдается тенденция постоянного роста.  
В анализе изменений по относительным и абсолютным показателям стоит отметить, что рост хоть и постоянный, но довольно неравномерный. Например, в августе 2021 года относительный прирост составил 60% менти, затем в сентябре – 25% менти, а в октябре - 27%. Подобные скачки наблюдаются и у менторов. Отрицательные значения по приросту в сентябре 2022 г. связаны с неполными данными за месяц. Полная таблица с данными по каждому месяцу выгружена в формате Excel (quantity_mentors_mentee). Ее можно посмотреть здесь: [quantity_mentors_mentee.xlsx](./results/quantity_mentors_mentee.xlsx) 

**2.**	Количество менторов, которые ни разу не назначали себе встречи – 0, а вот менти: 662 человека.  
**Можно проверить следующие гипотезы, почему менти не принимали участие во встречах:**  
**Гипотеза 1: Недостаток мотивации** 
Пользователи могли зарегистрироваться на платформу, но не нашли достаточно стимулов для активного участия. Например:
*	Недостаточная ценность платформы: Пользователь мог не увидеть достаточной ценности в предложенных услугах или возможностях.
*	Отсутствие четких целей: Пользователь мог не понимать, какие цели он хочет достичь с помощью платформы.
*	Сложность интерфейса: Возможно, интерфейс платформы сложен для понимания, и пользователям трудно ориентироваться.  
**Гипотеза 2: Технические проблемы**  
Пользователи могли столкнуться с техническими проблемами, которые мешают им полноценно использовать платформу. Например:
*	Проблемы с регистрацией: Пользователь мог успешно зарегистрироваться, но возникли трудности с настройкой профиля или доступом к функционалу.
*	Неисправности в приложении: Возможны ошибки в работе приложения, мешающие полноценному использованию.
*	Низкая производительность: Платформа могла оказаться медленной или нестабильной, что отпугнуло новых пользователей.
**Гипотеза 3: Отсутствие подходящего партнера** 
Менторы и менти могут не находить подходящих партнеров для взаимодействия. Например:  
* Маленький пул пользователей: Возможно, на платформе недостаточно активных пользователей, чтобы обеспечить достаточное количество пар.
*	Несоответствие интересов: Менторы и менти могут иметь разные интересы и области знаний, что затрудняет подбор подходящей пары.
*	Географические ограничения: Если платформа ограничена географически, это может уменьшить возможности для общения.  
**Гипотеза 4: Недостаточно информативная коммуникация**  
Пользователи могли не получать достаточного объема информации о том, как начать взаимодействие. Например:
*	Недоступные инструкции: Отсутствует руководство по началу работы или нет понятной системы уведомления о новых возможностях.
*	Непонятные уведомления: Пользователи могли не заметить или не понять важность уведомлений о возможных встречах.
*	Недостаточное вовлечение: Платформа могла не предлагать активное вовлечение через регулярные рассылки или приглашения.  
**Гипотеза 5: Личные причины** 
Некоторые пользователи могли просто потерять интерес или не иметь времени для участия. Например:
*	Изменение приоритетов: Пользователь мог переключиться на другие дела или проекты.
*	Личное время: Пользователь мог временно утратить возможность уделять внимание платформе.
*	Перегрузка информацией: Пользователь мог чувствовать перегрузку информацией и решением проблем, связанных с платформой.
  
**3.**	В среднем у каждого ментора 1 успешная сессия в неделю. У некоторых менторов наблюдается увеличение числа сессий по месяцам. Полная таблица с данными по каждому ментору выгружена в формате Excel (quantity_sessions_mentor). Ее можно посмотреть здесь:  [quantity_sessions_mentor.xlsx](./results/quantity_sessions_mentor.xlsx) 

**4.**	ТОП-5 менторов с самым большим числом сессий за последний полный месяц (mentor_id):   
* 4256 – 14 сессий,
* 2192 – 13 сессий,
* 1903 – 12 сессий, 
* 1552 – 10 сессий,
* 3855 – 9 сессий,
* 4015 – 9 сессий,
* 4798 – 9 сессий,
* 328 – 9 сессий,
* 1357 – 9 сессий.  
 Пять менторов имеют одинаковое число сессий, поэтому всего 9 менторов.
Самое большое число сессий по направлениям DS и  SQL, их преподают 6 менторов, что говорит о популярности этих направлений.  При этом самое большое число сессий на одного ментора приходится по направлениям Python для аналитики данных и  машинное обучение (по 14 и 13 сессий соответственно).

**5.** Разброс среднего времени по каждому менти довольно большой. У кого-то от одной сессии до другой в среднем проходит 3 дня, у кого-то – более 280 дней. Также само количество сессий разное, что влияет на среднее время между встречами.
У менторов ситуация более стабильная, хотя аномальные показатели тоже встречаются, например, 227 дней, на что также влияет разное количество встреч.
Полные таблицы с данными по каждому ментору и менти выгружены в формате Excel (meetings_mentee, meetings_mentors). Их можно посмотреть по ссылкам: [meetings_mentee.xlsx](./results/meetings_mentee.xlsx) , [meetings_mentors.xlsx](./results/meetings_mentors.xlsx)

**6.**	Количество отмененных сессий каждый месяц имеет разные показатели, так как с увеличением числа пользователей растет и число отмененных сессий. Доля отмен в последующие месяцы по сравнению с первыми месяцами уменьшилась. В целом показатель держится на уровне около 10 – 25 % в месяц, довольно нестабилен.
Полная таблица с данными по каждому отмененному направлению выгружена в формате Excel (cancelled_sessions). Ее можно посмотреть здесь: [cancelled_sessions.xlsx](./results/cancelled_sessions.xlsx)

**7.**	В результате анализа выявлены самые загруженные дни по направлениям:  
* SQL – понедельник – 39 встреч;
* Машинное обучение – вторник – 39 встреч;
* Python для аналитики данных – среда – 36 встреч;
* DS – среда – 33 встречи;
* Продуктовая аналитика – воскресенье – 31 встреча;
* Визуализация данных – среда – 27 встреч;
* АВ тестирование – вторник – 25 встреч.  
Таблица с данными выгружена в формате Excel (workload). Ее можно посмотреть здесь: [workload.xlsx](./results/workload.xlsx)

**8.**	Посчитав соотношение менторов и менти по месяцам за весь период, я определила средний коэффициент этого соотношения: 0,86. То есть на 1-го менти в среднем приходится 0,86 менторов.   
500 х 0,86 = 430 – столько менторов потребуется найти в следующем месяце при увеличении менти на 500 человек.

## Выводы
Количество пользователей платформы растет с каждым месяцем, но, несмотря на это, имеется ряд проблем: 
1.	Прирост пользователей крайне нестабилен, наблюдается то резкое увеличение, то значительное снижение. 
2.	Если менторы активно вовлечены в процесс, то количество менти, которые зарегистрировались, но еще ни разу не проводили встреч, достаточно большое – 662 человека. 
3.	У менторов наблюдается редкое число встреч в неделю, как правило одна. Хотя по некоторым направлениям количество встреч увеличивается от месяца к месяцу, этот прирост небольшой. 
4.	Разрыв между встречами также довольно большой и нестабильный.
5.	Число отмененных сессий хоть и сокращается, но в целом тоже имеет большой разброс от месяца к месяцу, что опять же говорит о нестабильности встреч.

## Рекомендации
**1.**	Проверить гипотезы, а именно можно предпринять следующие шаги:  
*	Провести опросы и интервью: обратиться к неактивным пользователям и спросить их мнение о платформе. Это даст ценную информацию о причинах их неактивности.
*	Проанализировать пользовательские данные более глубоко: изучить поведение пользователей, которые начали активно пользоваться платформой после регистрации. Это поможет выявить различия между активными и неактивными пользователями. 
*	Тестирование UX/UI: провести тестирование пользовательского опыта, чтобы выяснить, насколько удобен интерфейс и насколько легко новым пользователям разобраться в функциональности платформы.
*	Мониторинг технических ошибок: проанализировать журналы ошибок и отчеты о производительности, чтобы выявить возможные технические проблемы, влияющие на использование платформы.
*	Изучение профилей пользователей: исследовать, соответствуют ли профили пользователей друг другу. Возможно, проблема кроется в недостаточном количестве совпадений по интересам или специализациям.
*	Исследование коммуникационных каналов: оценить эффективность рассылок, уведомлений и других форм коммуникации. Возможно, пользователи просто не получают достаточно информации о возможностях платформы.
*	Разработка программы лояльности: предложить награды или бонусы за участие в встречах, чтобы стимулировать активность пользователей.  
Данные меры повысят Retention (количество пользователей, вернувшихся после первого посещения платформы) и снизят отток клиентов.

**2.**	Привлекать новую целевую аудиторию с помощью рекламы и акций.


## Дашборд
Для визуализации полученных результатов я создала представления в PostgreSQL по каждому поставленному вопросу, которые загрузила в виде таблиц в Power BI. На основе этих таблиц создала дашборд. Его можно посмотреть здесь: [dashboard_Mentorhub.pbix](./dashboard_Mentorhub.pbix)       
Также можно посмотреть дашборд в формате PDF: [dashboard_Mentorhub.pdf](./dashboard_Mentorhub.pdf)

