---
title: Веб-безопасность
slug: Learn_web_development/Extensions/Server-side/First_steps/Website_security
---

{{LearnSidebar}}{{PreviousMenu("Learn/Server-side/First_steps/Web_frameworks", "Learn/Server-side/First_steps")}}

Безопасность сайта требует бдительности во всех аспектах дизайна и использования сайта. Эта вводная статья не сделает из вас гуру безопасности веб-сайта, но она поможет вам понять, откуда приходят угрозы, и что вы можете сделать, чтобы укрепить своё веб-приложение против наиболее распространённых атак.

| Условия: | Элементарная компьютерная грамотность                                                                                                |
| -------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| Цель:    | Понять самые распространённые угрозы безопасности веб-приложений. И что вы можете сделать, чтобы уменьшить риск взлома вашего сайта. |

## Что такое безопасность сайта?

Интернет - опасное место! Мы регулярно слышим о том, что веб-сайты становятся недоступными из-за атак типа отказано в обслуживании, или отображение изменённой (и часто повреждённой) информации на их страницах. В других случаях миллионы паролей, адресов электронной почты и данные кредитных карт становились общедоступными, подвергая пользователей веб-сайта личному смущению или к финансовым рискам.

Цель веб-безопасности заключается в предотвращении этих (или других) видов атак. Более формальным определением веб-безопасности является: _способы защиты веб-сайтов от несанкционированного доступа, использования, изменения, уничтожения или нарушения работы._

Для эффективной безопасности веб-сайта необходимо уделять особое внимание к разработке всего веб-сайта: к вашему веб-приложению, конфигурации веб-сервера, при написании политик создания и обновления паролей, а так же кода на стороне клиента. Хотя все это звучит очень зловеще, хорошая новость заключается в том, что если вы используете веб-фреймворк для серверной части, то он почти наверняка обеспечит «по умолчанию» надёжные и продуманные механизмы защиты от ряда наиболее распространённых атак. Другие атаки можно смягчить с помощью конфигурации вашего веб-сервера, например, включив HTTPS. Наконец, есть общедоступные инструменты для сканирования уязвимостей, которые могут помочь вам определить, если вы допустили какие-либо очевидные ошибки.

В оставшейся части этой статьи мы рассмотрим более подробную информацию о некоторых самых распространённых угрозах и о простых шагах, которые вы можете предпринять, чтобы защитить свой сайт.

> **Примечание:**Это вводная статья, призванная помочь вам задуматься о безопасности веб-сайта, но она не является исчерпывающей.

## Угрозы безопасности сайта

В этом разделе перечислены лишь некоторые из наиболее распространённых угроз веб-сайта и способы их устранения. Читая, обратите внимание на то, насколько успешны угрозы, когда веб-приложение доверяет, либо _недостаточно параноидально_ относится к данным, поступающим из браузера.

### Межсайтовый скриптинг (XSS)

XSS (_Cross-Site Scripting_ - Межсайтовый скриптинг) это термин, используемый для описания типа атак, которые позволяют злоумышленнику внедрять вредоносный код _через_ веб-сайт в браузеры других пользователей. Поскольку внедрённый код поступает в браузер с сайта, он является _доверенным_ и может выполнять такие действия, как отправка авторизационного файла *cookie*пользователя злоумышленнику. Когда у злоумышленника есть файл _cookie_, он может войти на сайт, как если бы он был пользователем, и сделать все, что может пользователь, например, получить доступ к данным кредитной карты, просмотреть контактные данные или изменить пароли.

> [!NOTE]
> Уязвимости XSS исторически встречались чаще, чем любые другие виды угроз безопасности.

Уязвимости XSS делятся на _отражённые_ и _хранимые_, в зависимости от того, как сайт возвращает внедрённый код в браузер.

- _Отражённая_ XSS-уязвимость возникает, когда пользовательский контент, который передаётся на сервер, _немедленно_ и _без изменений_ возвращается для отображения в браузере. Любой скрипт в исходном пользовательском контенте запустится при загрузке новой страницы. Например, рассмотрим строку поиска по сайту, в которой поисковые слова закодированы как параметры URL, и эти слова отображаются вместе с результатами. Злоумышленник может создать поисковую ссылку, которая будет содержать вредоносный скрипт в качестве параметра (например: `https://developer.mozilla.org?q=beer<script%20src="http://example.com/tricky.js"></script>`) и переслать его другому пользователю по электронной почте. Если целевой пользователь кликнет по этой «интересной ссылке», то скрипт выполнится при отображении результатов поиска. Как мы уже говорили, злоумышленник таким образом получает всю информацию, необходимую ему для входа на сайт в качестве целевого пользователя, потенциального совершения покупок от имени пользователя или получения его контактной информации.
- Постоянная уязвимость XSS возникает, когда вредоносный скрипт хранится на веб-сайте, а затем снова отображается без изменений, чтобы другие пользователи могли выполнять его невольно.

  Например, доска обсуждений, которая принимает комментарии, содержащие неизмененный HTML, может хранить вредоносный скрипт от злоумышленника. Когда комментарии отображаются, скрипт выполняется и может отправить злоумышленнику информацию, необходимую для доступа к учётной записи пользователя. Атака такого рода чрезвычайно популярна и мощна, потому что злоумышленник может даже не иметь прямого отношения к жертвам.

  Хотя данные из запросов POST или GET являются наиболее распространённым источником уязвимостей XSS, любые данные из браузера потенциально уязвимы, такие как данные cookie, отображаемые браузером, или пользовательские файлы, которые загружаются и отображаются.

  Наилучшей защитой от уязвимостей XSS является удаление или отключение любой разметки, которая потенциально может содержать инструкции по запуску кода. Для HTML это включает такие элементы, как \<script>, \<object>, \<embed> и \<link>.

  Процесс изменения пользовательских данных, чтобы их нельзя было использовать для запуска сценариев или иным образом влиять на выполнение серверного кода, называется очисткой ввода. Многие веб-фреймворки автоматически очищают пользовательский ввод от HTML-форм по умолчанию.

### SQL injection

Уязвимости SQL-инъекций позволяют злоумышленникам выполнять произвольный код SQL в базе данных, позволяя получать, изменять или удалять данные независимо от разрешений пользователя. Успешная инъекционная атака может подделать удостоверения, создать новые удостоверения с правами администратора, получить доступ ко всем данным на сервере или уничтожить / изменить данные, чтобы сделать их непригодными для использования.

Типы внедрения SQL включают внедрение SQL на основе ошибок, внедрение SQL на основе логических ошибок и внедрение SQL на основе времени.

Эта уязвимость присутствует, если пользовательский ввод, который передаётся в базовый оператор SQL, может изменить смысл оператора. Например, следующий код предназначен для перечисления всех пользователей с определённым именем (userName), которое было предоставлено из формы HTML:

```sql
statement = "SELECT * FROM users WHERE name = '" + userName + "';"
```

Если пользователь указывает реальное имя, оператор будет работать так, как задумано. Однако злонамеренный пользователь может полностью изменить поведение этого оператора SQL на новый оператор в следующем примере, просто указав текст полужирным шрифтом для userName.

```sql
SELECT * FROM users WHERE name = 'a';DROP TABLE users; SELECT * FROM userinfo WHERE 't' = 't';
```

Модифицированный оператор создаёт действительный оператор SQL, который удаляет таблицу пользователей и выбирает все данные из таблицы userinfo (которая раскрывает информацию о каждом пользователе). Это работает, потому что первая часть введённого текста (a ';) завершает исходное утверждение.

Чтобы избежать такого рода атак, вы должны убедиться, что любые пользовательские данные, которые передаются в запрос SQL, не могут изменить природу запроса. Один из способов сделать это - экранировать все символы пользовательского ввода, которые имеют особое значение в SQL.

> [!NOTE]
> Инструкция SQL обрабатывает символ ' как начало и конец строкового литерала. Поместив обратную косую черту перед этим символом (\ '), мы экранируем символ и говорим SQL вместо этого трактовать его как символ (только часть строки).

В следующей инструкции мы экранируем символ '. Теперь SQL будет интерпретировать имя как всю строку, выделенную жирным шрифтом (это действительно очень странное имя, но безопасное).

```sql
SELECT * FROM users WHERE name = 'a\';DROP TABLE users; SELECT * FROM userinfo WHERE \'t\' = \'t';
```

Веб-фреймворки будут часто заботиться о зарезервированных символах для вас. Django, например, гарантирует, что любые пользовательские данные, передаваемые в наборы запросов (модельные запросы), будут экранируются.

> **Примечание:**этот раздел в значительной степени основан на информации из [Wikipedia](https://en.wikipedia.org/wiki/SQL_injection).

### Подделка межсайтовых запросов (CSRF)

CSRF-атаки позволяют злоумышленнику выполнять действия, используя учётные данные другого пользователя, без его ведома или согласия.

Этот тип атаки лучше всего пояснить на примере. Джон - злоумышленник, который знает, что определённый сайт позволяет пользователям, вошедшим в систему, отправлять деньги на указанную учётную запись, используя HTTP-запрос `POST`, который включает имя учётной записи и сумму денег. Джон создаёт форму, которая включает в себя его банковские реквизиты и сумму денег в виде скрытых полей, и отправляет её по электронной почте другим пользователям сайта (с кнопкой «Отправить», замаскированной под ссылку на сайт «быстрого обогащения»).

Если пользователь нажимает кнопку отправки, на сервер будет отправлен HTTP-запрос `POST`, содержащий сведения о транзакции и любые файлы cookie на стороне клиента, которые браузер связал с сайтом (добавление связанных файлов cookie сайта в запросы является нормальным поведением браузера). Сервер проверит файлы cookie и использует их, чтобы определить, вошёл ли пользователь в систему и имеет ли разрешение на совершение транзакции.

В результате любой пользователь, который нажимает кнопку _Отправить_ во время входа на торговый сайт, совершает транзакцию. Джон становится богатым.

> **Примечание:**Уловка здесь в том, что Джону не нужен доступ к файлам cookie пользователя (или учётным данным). Браузер пользователя сохраняет эту информацию и автоматически включает её во все запросы к соответствующему серверу.

Один из способов предотвратить этот тип атаки - запросить сервером запросы `POST`, содержащие секрет, созданный пользователем для конкретного сайта. Секрет будет предоставлен сервером при отправке веб-формы, используемой для переводов. Такой подход не позволяет Джону создать свою собственную форму, потому что он должен знать секрет, который сервер предоставляет пользователю. Даже если он узнает секрет и создаст форму для конкретного пользователя, он больше не сможет использовать ту же форму для атаки на каждого пользователя.

Веб-фреймворки часто включают такие механизмы предотвращения CSRF.

### Прочие угрозы

Другие распространённые атаки / уязвимости включают:

- [Clickjacking](https://www.owasp.org/index.php/Clickjacking). В этой атаке злоумышленник перехватывает клики, предназначенные для видимого сайта верхнего уровня, и направляет их на скрытую ниже страницу. Этот метод можно использовать, например, для отображения законного сайта банка, но захвата учётных данных для входа в невидимый {{htmlelement("iframe")}} , контролируемый злоумышленником. Clickjacking также можно использовать для того, чтобы заставить пользователя нажать кнопку на видимом сайте, но при этом на самом деле невольно нажимать совершенно другую кнопку. В качестве защиты ваш сайт может предотвратить встраивание себя в iframe на другом сайте, установив соответствующие заголовки HTTP.
- [Denial of Service](/ru/docs/Glossary/Distributed_Denial_of_Service) (DoS). DoS обычно достигается за счёт наводнения целевого сайта поддельными запросами, так что доступ к сайту нарушается для законных пользователей. Запросы могут быть просто многочисленными или по отдельности потреблять большие объёмы ресурсов (например, медленное чтение или загрузка больших файлов). Защита от DoS обычно работает, выявляя и блокируя «плохой» трафик, пропуская при этом легитимные сообщения. Эти средства защиты обычно расположены перед веб-сервером или на нем (они не являются частью самого веб-приложения).
- [Directory Traversal](https://en.wikipedia.org/wiki/Directory_traversal_attack) (Файл и раскрытие). В этой атаке злоумышленник пытается получить доступ к частям файловой системы веб-сервера, к которым у него не должно быть доступа. Эта уязвимость возникает, когда пользователь может передавать имена файлов, содержащие символы навигации файловой системы (например, `../../`). Решение состоит в том, чтобы очищать ввод перед его использованием.
- [File Inclusion](https://en.wikipedia.org/wiki/File_inclusion_vulnerability). В этой атаке пользователь может "случайно" указать файл для отображения или выполнения в данных, передаваемых на сервер. После загрузки этот файл может выполняться на веб-сервере или на стороне клиента (что приводит к XSS-атаке). Решение состоит в том, чтобы дезинфицировать ввод перед его использованием.
- [Внедрение команд](https://www.owasp.org/index.php/Command_Injection). Атаки с внедрением команд позволяют злоумышленнику выполнять произвольные системные команды в операционной системе хоста. Решение состоит в том, чтобы дезинфицировать вводимые пользователем данные до того, как их можно будет использовать в системных вызовах.

Полный список угроз безопасности веб-сайтов см. [Category: Web security exploits](https://en.wikipedia.org/wiki/Category:Web_security_exploits) (Wikipedia) и [Category: Attack](https://www.owasp.org/index.php/Category:Attack) (Open Web Application Security Project).

## Несколько ключевых сообщений

Почти все эксплойты безопасности, описанные в предыдущих разделах, успешны, когда веб-приложение доверяет данным из браузера. Что бы вы ни делали для повышения безопасности своего веб-сайта, вы должны дезинфицировать все данные, исходящие от пользователей, прежде чем они будут отображаться в браузере, использоваться в запросах SQL или передаваться в вызов операционной системы или файловой системы.

> [!WARNING]
> Внимание! Самый важный урок, который вы можете извлечь о безопасности веб-сайтов: **никогда не доверяйте данным из браузера**. Это включает, помимо прочего, данные в параметрах URL-адресов запросов `GET`, запросов `POST`, заголовков HTTP и файлов cookie, а также файлов, загруженных пользователем. Всегда проверяйте и дезинфицируйте все входящие данные. Всегда предполагайте худшее!

Вы можете предпринять ряд других конкретных шагов:

- Используйте более эффективное управление паролями. Поощряйте регулярную смену надёжных паролей. Рассмотрите возможность двухфакторной аутентификации для вашего сайта, чтобы в дополнение к паролю пользователь должен был ввести другой код аутентификации (обычно тот, который доставляется через какое-то физическое оборудование, которое будет иметь только пользователь, например, код в SMS, отправленном на его телефон).
- Настройте свой веб-сервер для использования [HTTPS](/ru/docs/Glossary/HTTPS) и [HTTP Strict Transport Security](/ru/docs/Web/HTTP/Reference/Headers/Strict-Transport-Security) (HSTS). HTTPS шифрует данные, передаваемые между вашим клиентом и сервером. Это гарантирует, что учётные данные для входа, файлы cookie, данные запросов `POST` и информация заголовка не будут легко доступны для злоумышленников.
- Следите за наиболее популярными угрозами ([текущий список OWASP находится здесь](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)) и в первую очередь устраняйте наиболее распространённые уязвимости.
- Используйте [инструменты сканирования уязвимостей](https://www.owasp.org/index.php/Category:Vulnerability_Scanning_Tools), чтобы выполнить автоматическое тестирование безопасности на вашем сайте. Позже ваш очень успешный веб-сайт может также обнаруживать ошибки, предлагая вознаграждение за обнаружение ошибок, как это [делает здесь](https://www.mozilla.org/en-US/security/bug-bounty/faq-webapp/) Mozilla.
- Храните и отображайте только те данные, которые вам нужны. Например, если ваши пользователи должны хранить конфиденциальную информацию, такую как данные кредитной карты, отображайте часть номера карты только для того, чтобы он мог быть идентифицирован пользователем, и недостаточно, чтобы его мог скопировать злоумышленник и использовать на другом сайте. Самый распространённый шаблон в настоящее время - отображение только последних 4 цифр номера кредитной карты.

Веб-фреймворки могут помочь смягчить многие из наиболее распространённых уязвимостей.

## Резюме

В этой статье объясняется концепция веб-безопасности и некоторые из наиболее распространённых угроз, от которых ваш веб-сайт должен пытаться защититься. Самое главное, вы должны понимать, что веб-приложение не может доверять никаким данным из веб-браузера. Все пользовательские данные должны быть очищены перед отображением или использованием в SQL-запросах и вызовах файловой системы.

Этой статьёй вы подошли к концу [этого модуля](/ru/docs/Learn_web_development/Extensions/Server-side/First_steps), охватывающего ваши первые шаги в программировании на стороне сервера. Мы надеемся, что вам понравилось изучать эти фундаментальные концепции, и теперь вы готовы выбрать веб-платформу и начать программировать.

{{PreviousMenu("Learn/Server-side/First_steps/Web_frameworks", "Learn/Server-side/First_steps")}}
