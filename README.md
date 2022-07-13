# Website-template-development-on-1C-Bitrix
Разработка шаблона сайта на **1С-Битрикс**

## 1. Введение.
В этом курсе разработаем шаблон корпаративного сайта, научимся работать с простыми компонентами, комплексными, **API** битрикса, тестировать проекты, создавать **backup**.

## 1. Подготовка к разработке шаблона.

* Подготовка редактора кода VsCode. 
* Приготовлена верстка из шаблона к курсу.
* Подготовлен сервер на Beget.
* Проверить установлен ли **git**, в командной строке вводим, **git --version**. 
**FTP**/**SSH** менеджер **Total Commander**/**FileZilla**.

Задание.
Подготовить сервер(аккаунт на **beget**), определиться с **IDE(vscode)**, подготовить **FTP** клиент(**FileZilla**).

Регестрируемся на **beget**, на почту приходит логин,пароль,хост заполняем данные в **FileZilla** устанавливаем соединение, переходим на сервере в папку хоста => **public_html** => создаём пустой индекс **index.php**, заходим на оф.сайт **1С-битрикс** скачиваем **bitrixsetup.php** копируем код в пустой индекс файл или изменям название на **index.php**, и запускаем установку.

Выбераем 1С-Битрикс:Управление сайтом => Бизнес

## 1. Шаблон. скрипты, стили, компоненты и включаемые области.

Приступим к разработке шаблона.

##### ВАЖНО!
1. Строжайше запрещено писать любую **PHP**-логику на 
обычной (публичной) странице сайта. Любой php-код 
должен инкапсулироваться в компоненты, контент-менеджер может туда влезть и что-нибудь сломать.
2. Максимум, что позволяется - использовать 
подключение скриптов с помощью **IncludeFile**, но это 
если разработчик полностью отдает себе отчет в том, 
что этого делать нельзя и это будет исправлено в 
ближайшее время, или того действительно требует 
логика проекта (единичные случаи) или проверки результата.

##### Использование компонентов
• Для решения задач изменения формы вывода данных правим 
шаблон компонента.
• Для изменения и дополнения кешируемых данных компонента, 
используем файл **result_modifier.php.**
• Для реализации логики, отрабатывающей при каждом вызове 
компонента независимо от кеширования, 
используем **component_epilog.php.**
• Для дополнения логики работы компонента копируем его в 
свое пространство имен и изменяем под требования задачи.
• Для создания новой логики и новых возможностей создаем 
компонент заново.

##### Вывод компонентов
```
<?$APPLICATION->IncludeComponent(
"bitrix:catalog.section",
“template_name",
array(
“IBLOCK_ID" => 18,
“IBLOCK_SECTION_ID" => 155, 
... // еще настройки компонента 
); 
);?>
```

##### Включаемая область
• Контент подтягивается из файла, не 
задействуется база данных
• Нужна для вывода статики
• Позволяет редактировать контент в режиме 
правки
• Выводится на страницу с помощью 
компонента **bitrix:main.include**
• Глобальные – код вызова размещен в 
шаблоне. Файлы хранятся в шаблоне, либо 
в выделенной папке
• Локальные – код вызова размещен на 
соответствующих страницах. Файлы 
хранятся в своих разделах

##### Вывод включаемой области
```
<?
$APPLICATION-> 
IncludeComponent("bitrix:main.include","",Array( 
"AREA_FILE_SHOW" => "sect", //page, file
"AREA_FILE_SUFFIX" => "inc",
"AREA_FILE_RECURSIVE" => "Y",
"EDIT_TEMPLATE" => "standard.php" ) 
);
?>
```
##### Подключение стилей и скриптов(старое ядро)
скрипты
```
$APPLICATION->AddHeadScript(SITE_TEMPLATE_PATH."/js/file.js" );
```
стили 
```
$APPLICATION->SetAdditionalCSS(SITE_TEMPLATE_PATH."/js/file.css", 
true);
```
Мета теги и другая инфа в **head**
```
$APPLICATION->AddHeadString("name='<meta name='yandex-verification' 
content='62be9ea1' />'");
```
Подключение стилей и скриптов **d7**(новое ядро)
```
use Bitrix\Main\Page\Asset;
Asset::getInstance()->addCss("/bitrix/css/main/bootstrap.min.css");
Asset::getInstance()->addJs(SITE_TEMPLATE_PATH . "/js/myscripts.js");
Asset::getInstance()->addString("<link rel='shortcut icon' 
href='/local/images/favicon.ico' />");
```
Битрикс умеет автоматически минифицировать и объединять **js** и **css** для ускорения работы сайта.

##### Подключение стилей и скриптов внутри компонентов
```
$this->getTemplate()->addExternalCss("/local/addcss.css");
$this->getTemplate()->addExternalJs("/local/addjs.js");
```