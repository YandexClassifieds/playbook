# Управление инцидентами

Инцидент — это __внеплановое прерывание или снижение качества работы сервиса__. Соответственно, управление инцидентами — комплекс мероприятий, __возвращающих сервис в нормальное для пользователей состояние как можно быстрее__. В этом разделе описан фреймворк, который поможет не изобретать свой велосипед для работы с инцидентами.


## Цели
Основная цель процесса — это снижение вреда от инцидентов для пользователей и бизнеса. Всё остальное — это производные от данной цели, адаптированные под специфику Яндекс.Вертикалей. Фреймворк нужен только чтобы упростить достижение основной цели, поэтому не стоит превращать отдельные его составляющие в карго-культ. В конечном счёте важнее стабильная работа сервиса у пользователей, а не аккуратность оформления резюме по разборам инцидентов.

1. *Мета-цель*: Система должна быть простой и создавать минимум накладных расходов на разработчиков;
2. Дежурный разработчик и сотрудник службы поддержки могут быстро и однозначно определить критичность инцидента;
3. Постоянное повышение скорости реагирования и устранения аварии;
    1. Инциденты обнаруживаются собственными алертами, а не по жалобам от пользователей или коллег;
    2. Во время инцидента участники группы реагирования действуют скоординировано, понимают свою роль;
4. Информация об инцидентах открыта на всю организацию и регулярно распространяется как минимум внутри разработки;
5. Системный разбор инцидентов и контроль за устранением причины. Инциденты не теряются и не зависают в воздухе;
6. Открытая и понятная документация о процессе реагирования и дальнейшей обработке инцидента.

## Фреймворк
### Жизненный цикл инцидента
Фреймворк делит жизненный цикл инцидента на этапы. Ключевая идея в том, что каждый инцидент должен последовательно дойти до этапа устранения причин и, как следствие, _не повториться_. Каждый этап сопровождается инструкциями и подсказками, позволяющим участникам работы над инцидентом сконцентрироваться на сути происходящего.

![Жизненный цикл](./attachments/incident_lifecycle_diagram.excalidraw.png)

- Обнаружение
- Классификация
- Устранение    
    - Диагностика (возможно, с привлечением [Incident Manager](incident_manager.md))
    - Восстановление штатной работы
- Распространение информации
- Предотвращение
    - [Анализ](postmortem.md)
    - [Разбор](lsr.md)
    - Устранение причины (контроль со стороны Incident Manager)

Теперь посмотрим, как это будет выглядеть вживую:
- Происходит обнаружение инцидента: дежурный видит его в канале с алертами своей команды, либо ему приносит обращение саппорт или смежник.
- Анализ рисков выполняется дежурным при помощи
     памятки дежурного и классификации инцидентов. 
- Дежурный фиксирует краткий
     Incident Report. 
    Он содержит минимальную информацию, по которой можно оценить критичность инцидента и собрать статистику по ключевым метрикам.
- Дежурный приступает к устранению аварии. В этот момент к нему может подключаться [Incident Manager](incident_manager.md). Он помогает с локализацией инцидента и координацией дежурных.
- После восстановления штатной работы дежурный пишет [Post-Mortem](postmortem.md) для инцидентов первого класса критичности, либо для иных, о которых попросил Incident Manager. IM и тимлид напоминают дежурному о написании post-mortem, если он забыл :).    
- IM, дежурные, участвовавшие в устранении и тимлид ответственной команды проводят [разбор](lsr.md) инцидента. После разбора в post-mortem фиксируются действия по предотвращению подобных инцидентов. При необходимости разбор проводится очно.
- IM следит за тем, что действия по предотвращению выполняются.

### Метрики
Важная составляющая работы с инцидентами — это статистика. Её сбор и анализ нужен для отслеживания здоровья организации и обеспечения позитивной тенденции по авариям для пользователей.

- Распределение инцидентов по способу обнаружения (алерт в ответственной команде / алерт в смежной команде / жалоба от сотрудников компании / жалоба от пользователей);
- Время до обнаружения;
- Время до устранения;
- Количество пострадавших пользователей и финансовый ущерб;
- Доля инцидентов **без устранённых причин**.


### Процедуры
- Вводятся классы критичности инцидентов, по ним классифицируются потенциальные инциденты в продуктах при помощи служб поддержки и продаж.
    - **Первый**. Высший уровень критичности, нарушение ключевых бизнес-процессов. Надо чинить любой ценой вне зависимости от обстоятельств.    
    - **Второй**. Незначительное нарушение ключевых бизнес-процессов или нарушение второстепенных процессов. Устранять в рабочее время, отложив остальные дела;
    - **Третий**. Незначительные проблемы. Всё остальное. Устранять в штатном режиме.
- Вводится роль Incident Manager, которую получают руководители разработки и другие опытные люди, обладающие широкими знаниями системы.    
    Носители этой роли отвечает за минимизацию ущерба от инцидентов в своих подсистемах. В минимизацию ущерба входит как предотвращение аварий, так и помощь в их устранении.
- По инцидентам вводятся метрики, которые отслеживаются командой IM:
- Классификация инцидентов оформляется максимально наглядным и понятным программистам способом, минимизирует сомнения. Помимо неё описывается и поддерживается в актуальном состоянии памятка дежурного.
- Обязательным этапом онбординга становится обучение дежурству. Как программа-минимум — знакомство с памяткой и специфичными для команды вещами. Как максимум — тренинг по реагированию на инцидент.
- Для регистрации инцидентов, прохода по этапам жизненного цикла, написания пост-мортемов создаются формы, боты и шаблоны, позволяющие заполнить их с минимумом когнитивной нагрузки, но сохранить при этом бесценную статистику.



---
### Ссылки
- [Building Secure and Reliable Systems](https://static.googleusercontent.com/media/sre.google/en//static/pdf/building_secure_and_reliable_systems.pdf) (Глава 16)
- [High Severity Incident Management Program](https://www.gremlin.com/community/tutorials/how-to-establish-a-high-severity-incident-management-program/)
- [ITIL](https://wiki.en.it-processmaps.com/index.php/Incident_Management)
- [Наш доклад об управлении инцидентами на CodeFest 2024](https://www.youtube.com/watch?v=aNb1G4qOiNE)
