# Основные определения и описание протоколов

IP — Internet Protocol.
TCP — Transmission Control Protocol.
UDP — User Datagram Protocol.

Протокол IP является протоколом сетевого уровня.
Протоколы TCP и UDP являются протоколами транспортного уровня.

**Сетевой уровень** (Network layer) — 3-й уровень сетевой модели OSI, предназначается для определения пути передачи данных. Отвечает за трансляцию логических адресов и имён в физические, определение кратчайших маршрутов,маршрутизацию, отслеживание неполадок и заторов в сети. На этом уровне работает такое сетевое устройство, как маршрутизатор. 

**Транспортный уровень** (Transport layer) — 4-й уровень сетевой модели OSI, предназначен для доставки данных. При этом неважно, какие данные передаются, откуда и куда, то есть, он предоставляет сам механизм передачи. Блоки данных он разделяет на фрагменты, размеры которых зависят от протокола: короткие объединяет в один, а длинные разбивает. Протоколы этого уровня не знают ничего про различия в узлах сетей. Всё, что им требуется знать про адресацию, это то, что есть приложение, отсылающее сообщение, и оно для отправки использует какой-то порт. И приложение, которое получает сообщение, тоже использует какой-то порт. Основная «адресация внутри Интернета» же реализована на межсетевом уровне.

## TCP/IP

TCP/IP являются двумя разными протоколами, которые работают вместе, чтобы обеспечить доставку данных в предполагаемое место назначения в сети. IP получает и определяет адрес (IP-адрес) приложения или устройства, на которое должны быть отправлены данные. TCP отвечает за передачу данных и обеспечение их доставки на определенное IP-устройство или целевое приложение. Два протокола часто используются вместе, чтобы данные имели место назначения и безопасно достигали его, поэтому этот процесс зачастую называют TCP/IP.

## TCP — описание
Перед тем, как начать передачу данных, клиент и сервер должны подключиться друг к другу и договориться о том, как они будут осуществлять эту самую передачу.

У протокола TCP есть несколько особенностей:

- Система нумерации сегментов. TCP отслеживает передаваемые и принимаемые сегменты, присваивая номера каждому из них. Байтам данных, которые должны быть переданы, присваивается определенный номер байта, в то время как сегментам присваиваются порядковые номера.
- Управление потоком. Функция ограничивает скорость, с которой отправитель передает данные. Это делается для обеспечения надежности доставки, в том числе чтобы компьютер не генерировал пакетов больше, чем может принять другое устройство. Если говорить простым языком, то получатель постоянно сообщает отправителю о том, какой объем данных может быть получен.
- Контроль ошибок. Функция реализуется для повышения надежности путем проверки байтов на целостность.
- Контроль перегрузки сети. Протокол TCP учитывает уровень перегрузки в сети, определяемый объемом данных, отправленных узлом.

### Структура пакета TCP/IP

![изображение](https://habrastorage.org/getpro/habr/upload_files/2d2/917/081/2d2917081ec1e6a8dc94d9b0d037d8f7.png)
В каждый пакет данных TCP добавляет заголовок общим объемом в 20 байт, в котором содержатся 10 обязательных полей:

- Исходный номер порта — порт устройства-отправителя.
- Целевой номер порта— порт принимающего устройства.
- Порядковый номер. Устройство, инициирующее TCP-соединение, должно выбрать случайный начальный порядковый номер, который затем увеличивается в соответствии с количеством переданных байтов.
- Номер подтверждения. Принимающее устройство увеличивает этот номер с нуля в соответствии с количеством полученных байтов.
- Длина заголовка. Данный параметр определяет размер заголовка, чтобы система могла понять, где начинаются данные.
- Зарезервированные данные — зарезервированное поле, значение которого всегда равно нулю.
- Флаги. TCP использует девять флагов для управления потоком данных в определенных ситуациях — например, при инициировании сброса сессии.
- Размер окна — самая важная часть заголовка TCP. Это поле используется получателем для указания отправителю объема данных, которые он может принять.
- Контрольная сумма. Отправитель генерирует контрольную сумму и передает ее в заголовке каждого пакета. Принимающее устройство может использовать контрольную сумму для проверки ошибок в полученном файле.
- Срочный указатель — это предлагаемая протоколом возможность помечать некоторые байты данных тегом «Срочно» для их пересылки и обработки вне очереди.
- Опции/отступы. Может использоваться для расширения протокола или его тестирования.


Некоторые флаги управления:

**SYN** — начинает соединение и синхронизирует порядковые номера. Первый пакет, отправленный с каждой стороны, должен в обязательном порядке иметь установленным этот флаг.

**FIN** — одна из конечных точек отправляет пакет с установленным флагом FIN для другой конечной точки, чтобы сообщить, что все пакеты были отправлены, и соединение пора завершить.

**RST** — сброс данного соединения. Отправкой пакета RST одна из сторон сообщает о немедленном разрыве соединения. При этом соединение обрывается, а буфер очищается. Самые распространенные причины отправки пакета с установленным флагом RST — ответ на пакет, полученный для закрытого сокета; пользователь сам прервал соединение (например, закрыв браузер, не дожидаясь ответа); соединение не было нормально закрыто, но находится в неактивном состоянии некоторое время.

### Механизм передачи сообщений TCP

Перед тем, как данные могут быть переданы между двумя узлами, в TCP, в отличие от UDP, предусмотрена стадия установки соединения.Эта «установка» обычно называется рукопожатием. 

Условно рукопожатие можно представить так:

![изображение](https://habrastorage.org/getpro/habr/upload_files/68c/63d/de1/68c63dde1db9d5b3cdc4840c95bea3f9.png)

1) Клиент посылает серверу пакет, который называется SYN.
2) Сервер отвечает пакетом SYN-ACK.
3) Клиент отвечает серверу пакетом ACK.
SEQ - показывает, сколько данных отправитель уже послал в сторону получателя.
ACK - показывает сколько данных отправитель этого пакета получил на текущий момент.

Эти числа важны, потому что позволяют обеим сторонам понимать статус передачи данных. И клиент, и сервер, оба ожидают получить определённые значения порядкового номера и номера подтверждения от противоположной стороны. А если вдруг действительно полученные значения расходятся с ожидаемыми, значит где-то произошла ошибка, и возможно кто-то из них должен переотправить какие-то пакеты.
И когда рукопожатие закончено, сервер как раз и понимает, что клиент готов передавать данные, и начинает их от него ожидать.
В процессе обмена данными протокол TCP гарантирует отправку и получение сегментов в точном порядке. Каждому пакету присваивается номер, который указывает на позицию данных в потоке.

Также, после того, как все данные были переданы, наступает стадия завершения соединения. Таким образом, осуществление каждого TCP-соединения можно условно разделить на три фазы:

1) Инициализация соединения.
Установка соединения осуществляется с помощью, так называемого трехстороннего рукопожатия TCP. Инициатором соединения может выступать любая сторона.

2) Загрузка данных.
После инициализации соединения полезная нагрузка будет перемещаться в обоих направлениях TCP-соединения.Данные разбиваются на сегменты (пакеты), каждый из которых имеет заголовок с информацией о портах, номерах последовательности и других параметрах; Происходит обмен пакетами между клиентом и сервером; Принимающая сторона отправляет подтверждение за каждым принятым сегментом. Важно отметить, что присутствует механизм обнаружения и коррекции ошибок.

3) Завершение соединения.
При нормальном завершении TCP-соединения в большинстве случаев инициализируется процедура, называемая двухсторонним рукопожатием, в ходе которой каждая сторона закрывает свой конец виртуального канала и освобождает все задействованные ресурсы. Обычно эта фаза начинается с того, что один из задействованных процессов приложения сигнализирует своему уровню TCP, что сеанс связи больше не нужен. Со стороны этого устройства отправляется сообщение с установленным флагом FIN (отметим, что этот пакет не обязательно должен быть пустым, он также может содержать полезную нагрузку), чтобы сообщить другому устройству о своем желании завершить открытое соединение. Затем получение этого сообщения подтверждается (сообщение от отвечающего устройства с установленным флагом ACK, говорящем о получении сообщения FIN). Когда отвечающее устройство готово, оно также отправляет сообщение с установленным флагом FIN, и, после получения в ответ подтверждающего получение сообщения с установленным флагом ACK или ожидания определенного периода времени, предусмотренного для получения ACK, сеанс полностью закрывается. 

## TCP — примеры использования

Протокол TCP гарантирует доставку, а также обеспечивает целостность данных, передаваемых в сети. Поэтому он применяется для передачи данных, которые чувствительны к нарушению целостности, — например, текстов, файлов и т.п. Вот несколько протоколов, которые работают по TCP: 

- SSH, FTP, Telnet: в данных протоколах TCP используется для обмена файлами.
- SMTP, POP, IMAP: протоколы, где TCP отвечает за передачу сообщений электронной почты.
- HTTP/HTTPS: протоколы, где TCP отвечает за загрузку страниц из интернета.

Эти примеры работают на уровне приложений стека TCP/IP и передают данные вниз к TCP, на транспортный уровень.

## UDP — описание

Если нам очень важна скорость передачи, а вот потеря пакетов не так критична (как, например, в голосовом или видеотрафике), то лучше использовать UDP, или User Datagram Protocol. В отличие от TCP он обеспечивает передачу данных без получения подтверждения от пользователя. Проще говоря, просто отправляет пакеты и не ждет ничего в ответ. Из-за этого достигается высокая скорость в ущерб надежности. Пакеты, передаваемые с помощью UDP, иногда называются датаграммами - блоки информации, передаваемые напрямую, без создания выделенного виртуального канала.

### Структура пакета UDP

![изображение](https://user-images.githubusercontent.com/62937015/212537765-3f7a6fcd-36b7-4197-b563-8a4509320bed.png)
Как можно увидеть на картинке, заголовок UDP-пакета устроен намного проще заголовка TCP-пакета и содержит:
- Порт источника — порт устройства-отправителя.
- Порт назначения — порт принимающего устройства.
- Длина — длина сообщения, включая заголовок и данные.
- Контрольная сумма — контрольная сумма заголовка и данных для проверки.

### Механизм передачи данных UDP

UDP использует простую модель передачи, без явных «рукопожатий» для обеспечения надёжности, упорядочивания или целостности данных. Датаграммы могут прийти не по порядку, дублироваться или вовсе исчезнуть без следа, но гарантируется, что если они придут, то в целостном состоянии.

Эта технология предлагает более простой алгоритм передачи данных. Он состоит всего из 4 базовых этапов:
1) Упаковка данных в дейтаграммы — общий объем информации для передачи разбивается на сегменты (дейтаграммы).
2) Отправка дейтаграмм — начинается сразу же после их формирования. Отправитель отсылает дейтаграммы на целевой адрес и порт получателя.
3) Передача дейтаграмм — данных передаются по сети разными путями, поэтому могут быть доставлены в разное время и в хаотичном порядке.
4) Получение пакетов — получатель принимает дейтаграммы и извлекает данные.

Для защиты такого вида передачи данных используют дополнительные меры. Например, прокси или туннельное соединение между пользователем и серверами компании. 


## UDP — примеры использования

Чаще всего UDP применяется в чувствительных ко времени службах, где потерять пакеты лучше, чем ждать. Звонки в Skype или Google Meet, стриминг видео, онлайн-трансляции используют этот протокол из-за того, что они чувствительны ко времени и рассчитаны на определенный уровень потерь. Вся голосовая связь через интернет работает по протоколу UDP. Также UDP очень часто используется в онлайн-играх. Аналогичная история с DNS-серверами, поскольку они должны быть быстрыми и эффективными.

# Сравнение TCP и UDP
Ключевым различием между TCP и UDP является скорость, поскольку TCP сравнительно медленнее UDP. В целом, UDP является быстрым, простым и эффективным протоколом, однако повторная передача потерянных пакетов данных возможна только в TCP. 

Еще одно заметное различие между TCP и UDP заключается в том, что первый обеспечивает упорядоченную доставку данных от пользователя к серверу (и наоборот). UDP, в свою очередь, не проверяет готовность получателя и может доставлять пакеты вразнобой.

| | TCP | UDP |
|----|---|----|
| Состояние соединения |Требуется установленное соединение для передачи данных (соединение должно быть закрыто после завершения передачи) | Протокол без соединения, без требований к открытию, поддержанию или прерыванию соединения|
|Гарантия доставки|Может гарантировать доставку данных получателю|Не гарантирует доставку данных получателю|
|Повторная передача данных|Повторная передача нескольких кадров в случае потери одного из них|Отсутствие повторной передачи потерянных пакетов|
|Проверка ошибок|Полная проверка ошибок|Базовый механизм проверки ошибок. Использует вышестоящие протоколы для проверки целостности|
|Метод передачи|Данные считываются как поток байтов; сообщения передаются по границам сегментов|UDP-пакеты с определенными границами; отправляются по отдельности и проверяются на целостность по прибытии|
| Сферы применения|Используется для передачи сообщений электронной почты, HTML-страниц браузеров |Видеоконференции, потоковое вещание, DNS, VoIP, IPTV |

| TCP протокол | UDP протокол |
|---|----|
| Преимущества |
| Возможность установить связь между разными типами устройств | Низкая задержка и минимальная нагрузка на сеть |
| Работа осуществляется независимо от ОС | Доступны трансляция и многоадресная передача данных | 
| Поддержка разных протоколов маршрутизации | Возможность управления потоком данных для определения первостепенно важных пакетов для доставки |
| Возможность взаимодействия в сети между организациями | Более точный контроль над ошибками |
| Имеет архитектуру, пригодную к активному масштабированию | Не требует подтверждения доставки |
| Возможность самостоятельного управления | Более компактный формат заголовков (меньше символов) |
| Недостатки |
| Больше символов в заголовке | Невозможность повторного запроса на отправку при потере данных |
| Невозможность использования для многоадресной передачи данных и их трансляции | Сложность контроля ошибок из-за отсутствия механизмов подтверждения и повторной отправки |
| Сложная замена процедуры протокола | Отсутствие уведомлений о состоянии соединения |
| Требует подтверждения каждого отправленного пакета | Высокий риск потери данных |
| Блокирует пакеты с ошибками |  |
| Снижение эффективности при высокой загрузке сети |  |

Нельзя сказать, что один протокол хуже или лучше другого. Для достижения лучшего качества и скорости передачи данных, а соответственно и работы онлайн-сервисов, протоколы используются в тандеме. Например, для передачи мультимедийных файлов, потокового видео и аудио или стриминга лучше использовать технологию UDP. Тут на первый план выходит его малая скорость задержки. В свою очередь протокол TCP применяют там, где важна гарантия и подтверждение доставки пакетов. Например, для осуществления транзакций.

## Что почитать по теме:
[Отличия TCP- и UDP-протоколов — определяем разницу на примерах (must have)](https://selectel.ru/blog/tcp-vs-udp/)

[Протоколы TCP и UDP (тут больше интересной информации про UDP)](https://professorweb.ru/my/csharp/web/level1/1_4.php)

[Простое пособие по сетевой модели OSI для начинающих](https://selectel.ru/blog/osi-for-beginners/)
