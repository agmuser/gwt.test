# gwt.test

Необходимо реализовать небольшое приложение позволяющее регистрировать пользователей, заполнять по ним необходимую информацию, просматривать зарегистрированных пользователей, а так же назначать им различные роли и действия. 

Окончательную версию необходимо оформить пул-реквестом в репозиторий. 

Всю информацию приложение должно хранить в БД http://www.h2database.com/html/main.html. Серверная часть должна быть реализовава на spring (mvc, security) + hibernate. Клиент на GWT 2.7.0. На клиенте использовать паттерн MVP.

Предметная область лежит в пакете domain. Состоит из классов:
1. Учетка (User)
2. Персона (Person)
3. Роль (Role)
4. Специальная роль - Администратор (AdminRole)
5. Пользовательская роль - роль настраиваемая в приложении (CustomRole)
5. Действие (Action)
6. Тип контакта (ContactType)
7. Контакт (Contact)

Связи между классами и ограничения на поля описаны в комментариях к классам.

При старте приложения в базу вставляются первоначальные данные. Три персоны (одна из них админ - роль AdminRole). Три любых типа контактов. Три любых роли. 

Если в процессе редактирования данных происходят ошибки, то система должна уведомлять об этом пользователя, однообразным способом. Красота интерфеса не важна. Главное чтобы данные были читабельны (контролы и тексты не наезжали друг на друга). Приложение должно работать в браузере Chrome (поддержка остальных браузеров по желанию). 

Реализация spring-security может быть любой. Самый простой вариант: есть две роли - авторизированный пользователь и анонимный. А весь остальной контроль возложен на контроллеры бизнес логики. Если реализуется более сложный вариант (например через привязку к enum Action), то это только приветствуется.

Любые спорные моменты реализовывать как можно проще для себя. Объекты domain области можно расширять, добавлять новые (если это необходимо), но нельзя сужать. 

Функционал приложения:

1. *Странца логина*. Два поля: имя пользователя (input type="text") и пароль (input type="password") + кнопка "Войти". На ней пользователь заполняет оба поля и нажимает кнопку "Войти". Если такого пользователя нету в системе, то система уведомляет его об этом (любым визуально понятным способом). Если такой пользователь есть, то система пропускает его дальше на основную страницу приложения.
 
2. *Основная станица приложения*. Сверху навигационное меню состоящее из двух пунктов: "Главная", "Администрирование". 
а) Пункт меню *Главная* состоит из двух подпунктов: "О себе", "Пользователи". Для них в системе предусмотренны соответствующие view. Если у залогиненного пользователя нету доступа "BROWSE_USERS", то нункт меню "Пользователи" не отображается. 
б) Пункт меню *Администрирование* состоит из подпунктов: "Роли", "Типы контактов". Пункт меню "Роли" доступен пользователям с доступом EDIT_ROLES (для остальных не отображается). Пункт меню "Типы контактов" доступен пользователям с доступом EDIT_CONTACT_TYPES (для остальных не отображается). Если у текущего пользователя нету ни одного из доступов (т.е. оба пункта не отображаются), то не отображается меню "Администрирование" целиком.    
Нажатие на любой из пунктов: "О себе", "Пользователи", "Роли", "Типы контактов", подгружает в основную часть экрана соответствующий вью. 

3. View *О себе*. В основной части экрана выводится информация о залогиненной персоне (содержимое класса Person без пароля учетки). Если у текущего пользователя есть хоть одна роль с доступом EDIT_OWN_PERSON_DATA или EDIT_OWN_PERSON_DATA, то на интерфейсе присутствует кнопка "Редактировать". При нажатии на которую интерфейс входит в режим редактирования. В режиме редактирования пользователь может поменять все доступные ему поля. Выход из режима редактирования осуществляется по нажатию одной из двух кнопок: "Отмена" или "Сохранить" (кнопки видны только в режиме редактирования). Если пользователь нажмет "Отмена", то система должна выйти из режима реадактирования без сохранения измений. Если пользователь нажимает "Сохранить", то система проверяет данные на предмет существующих ограничений и если они им удовлетворяют, сохраняет их и выходит из режима редактирования. Если же нет, то система уведомляет об этом пользователя (любым визуально понятным способом) и остается в режиме редактирования. Редактирование списка контактов реализовать любым понятным способом. 

4. View *Пользователи*. В основной части экрана выводится таблица с пользователями зарегистрированными в программе. Таблица состоит из столбцов: Имя, Фамилия, Отчетсво. Содержимое столбцов должно быть сделано в виде ссылок, по нажатию на которую система открывает станицу с личными данными персоны (view *О себе*, только отображающее данные выбранной персоны). 

5. View *Роли*. В основной части экрана выводится таблица в первом столбце которой отображаются роли в доступные в системе. Плюс по столбцу на каждый экшен. В этих столбцах стоят чек-боксы. Включение/выключение которых редактирует список доступных роли экшенов. Роль AdminRole, должна быть выделена визуально. Все чекбоксы в строке роли AdminRole должны быть задизейблены. Сверху над таблицей кнопка "+", по нажатию на которую появляется диалоговое окно с инпутом и кнопкой "Создать". В которую пользователь вводит имя роли и нажимает создать. Если имя удовлетворяет ограничениям, то создается запись (по умолчанию с разрешенным доступом EDIT_OWN_PERSON_DATA) и появляется в конце таблицы. Если же нет, то система уведомляет об этом пользователя (любым визуально понятным способом). 

6. View *Типы контактов*. В основной части экрана выводится таблица с двумя столбцами. В первом отображается имя типа контакта, во втором кнопка "-", нажав на которую можно удалить данный тип. Сверху над таблицей кнопка "+", по нажатию на которую появляется диалоговое окно с инпутом и кнопкой "Создать". В которую пользователь вводит имя типа и нажимает создать. Если имя удовлетворяет ограничениям, то создается запись и появляется в конце таблицы. Если же нет, то система уведомляет об этом пользователя (любым визуально понятным способом). 
