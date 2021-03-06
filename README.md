# Программный комплекс "Система управления организациями"

## О программном комплексе

Программный комплекс из трёх слабо связанных компонент: лицевой части, серверной части и базы данных. С особенностями программных модулей можно ознакомиться в хранилищах их исходного кода: [хранилище лицевой части](https://github.com/pamugk/org-info-client.git "тут") и [хранилище серверной части](https://github.com/pamugk/org-info-server.git "там") соответственно.  
Так как комплекс развёрнут на платформе [Heroku](https://heroku.com "URL Heroku") с использованием только бесплатных возможностей, после 25-30 минут простоя он переходит в спящий режим, поэтому при обращении к модулям часть времени (как правило, около минуты) выполнения запроса может быть потрачена на запуск модулей - будьте терпеливы.

## Использование развёрнутой в Интернете версии  

Если вы хотите просто воспользоваться системой управления организациями, лицевая часть комплекса уже размещена [вот этому адресу](https://pamugk.github.io/org-info-client/ "URL лицевой части"), достаточно лишь туда зайти.  
Серверная часть, предоставляющая программный интерфейс для взаимодействия с базой данных, находится [вот тут](https://org-info-server.herokuapp.com "URL серверной части"). Подробное описание интерфейса можно найти на странице хранилища кода серверной части (см. выше).

## Самостоятельное развёртывание комплекса

Хоть Интернет в настоящее время и воспринимается многими как нечто естественное, доступное всегда и каким-то чудом позволяющее решать все проблемы, это не так - банальный обрыв провода в одном-единственном месте может утащить за собой в бездну небытия работоспособность системы, полагающейся на Интернет. Что уж говорить о влиянии внезапного закрытия организаций, предоставляющих услуги развёртывания программных компонентов и баз данных, на работоспособность зависящих от них систем.  
Хорошим решением для снижения влияния Интернет-неприятностей на работу системы управления организациями может стать развёртывание модулей комплекса на личных устройствах.  
Рассмотрим действия, которые необходимо предпринять для развёртывания каждого модуля. Предполагается, что на устройствах, используемых для развёртывания программных модулей, в момент развёртывания есть доступ в Интернет.  
Предлагается рассмотреть развёртывание комплекса с использованием только консольных утилит, так как описание их использования более ёмкое, чем описание использования программ с графическим интерфейсом.

### Развёртывание лицевой части

Для развёртывания модуля лицевой части, предоставляющего графический интерфейс для взаимодействия с системой, обязательно наличие на целевом устройстве развёртывания программной платформы [Node.js](https://nodejs.org/en/ "Сайт Node.js") версии 12. Также используется npm - система управления пакетами Node.js, которая обычно поставляется вместе с Node.js  
Развёртывание лицевой части выполняется по следующему алгоритму:

1.	Клонировать хранилище исходного кода лицевой части, расположенное по ссылке https://github.com/pamugk/org-info-client.git.
2.	Открыть терминал и перейти в корневую директорию клонированного хранилища.
3.	Выполнить в терминале команду `npm install` для локальной установки всех зависимостей, необходимых модулю лицевой части.
4.	Выполнить в терминале команду `npm install serve`, чтобы локально установить сервер, который будет в дальнейшем будет использоваться для раздачи статической части развёртываемого сайта (если есть необходимость/желание использовать Nginx или Apache - флаг вам в руки, но в данной инструкции ***далее будет использоваться именно serve***).
5.	Выполнить в терминале команду `npm run build`, чтобы создать оптимизированную для развёртывания сборку сайта (заметим, что где-то там, в глубинах заготовленного разработчиками React сценария сборки используется Webpack, который, на счастье рядовых веб-разработчиков, никак наружу не торчит).
6.	Выполнить в терминале команду `serve -s build`, чтобы запустить сервер (где "build" - имя директории, в которую сохранён результат сборки сайта).

Для остановки сервера используется комбинация клавиш "Ctrl"+"C".
***Настоятельно** рекомендуется выполнить остановку сервера перед закрытием терминала во избежание ситуаций вида "Остались лишние неуправляемые процессы, продолжающие активно пользовать ресурсы системы".

### Развёртывание серверной части	

Для развёртывания модуля серверной части требуется наличие на целевом устройстве развёртывания установленной среды выполнения Java 8 (или более новая). При разработке была использована JRE, идущая в комплекте с [LibericaJDK](https://bell-sw.com/ "Сайт Bellsoft - разработчиков LibericaJDK").  
Развёртывание серверной части выполняется по следующему алгоритму:

1.	Скачать со jar-файл из последнего релиза на [странице релизов](https://github.com/pamugk/org-info-server/releases "Страница релизов сервера")
2.	Запустить сервер, выполнив команду `java -jar /путь/до/jar` (по умолчанию сервер будет слушать порт 8081).

Чтобы лицевая часть стала обращаться к новоразвёрнутому серверу, необходимо в файле /src/setup.js (путь указан относительно корневой директории хранилища кода лицевой части) поменять значение константы `SERVER` на адрес развёрнутого сервера. После замены необходимо пересобрать лицевую часть перед повторным запуском.

### Использование своей базы данных

Предположим, что у вас имеется доступ к своей базе данных, развёрнутой на сервере БД [PostgreSQL](https://www.postgresql.org/ "Сайт PostgreSQL") версии 12 (теоретически, подходят также более старые версии вплоть до 9.1, но разработка велась именно с использованием PostgreSQL 12) с установленным пакетом, содержащий модули стандартных расширений PostgreSQL (в частности, важно наличие расширения uuid-ossp).  
У учётной записи пользователя, которого предполагается использовать для подключения к БД, должны быть права на создание таблиц и расширений в схеме public (которая обязательно должна присутствовать в используемой БД), а также на создание новых схем.  
Чтобы серверная часть стала использовать требуемую базу данных, необходимо в одну директорию с jar сервера сложить файл с настройками Spring Boot, в котором должна быть указана вся необходимая для подключения к БД информация. Пример файла с настройками SpringBoot можно найти на странице релиза сервера - в нём задано подключение к БД *data*, расположенной на одной с сервером машине, с использованием пользователя *client*, имеющего пароль *password*.  
Так как серверная часть использует систему версионирования баз данных [Liquibase](https://www.liquibase.org "Сайт Liquibase"), при первом подключении к БД сервер сам создаст все необходимые для его функционирования расширения, схемы и таблицы. Никаких тестовых данных с сервером не поставляется, поэтому созданные таблицы будут совершенно пусты.	
