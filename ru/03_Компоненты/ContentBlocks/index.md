Автор: <a href="https://github.com/mnoskov/contentblocks">mnoskov</a>

Плагин позволяет разработчику определить набор блоков с определенной разметкой и списком полей, чтобы контент-менеджер использовал те блоки, которые считает нужным, со своим наполнением.

Конфигурация для блоков берется из папки config. Для создания нового блока нужно создать в этой папке файл .php, который должен вернуть ассоциативный массив. Структура массива следующая:

<table>
<tr><th>Ключ</th><th>Значение</th></tr>
<tr><td>title</td><td>Название блока, видимое менеджеру при заполнении</td></tr>
<tr>
<td>fields</td>
<td>
Ассоциативный массив используемых полей, в котором ключами являются идентификаторы полей, а значениями - массивы опций этих полей.

Возможные типы полей и опции приведены ниже.
</td>
</tr>
<tr><td>show_in_templates</td><td>Массив идентификаторов шаблонов, для которых доступны редактирование и вывод блоков</td></tr>
<tr><td>hide_in_docs</td><td>Массив идентификаторов документов, для которых редактирование и вывод недоступны</td></tr>
<tr><td>show_in_docs</td><td>Массив идентификаторов документов, для которых доступны редактирование и вывод блоков.

Если этот параметр указан, то "hide_in_docs" не принимается во внимание.

Если не указан ни один из параметров, ограничивающих доступ, блоки будут доступны во всех документах.</td></tr>
<tr>
<td>templates</td>
<td>
Ассоциативный массив, содержащий шаблон для ключа "owner", а также шаблоны для каждой группы полей.

Описание методов создания шаблонов смотрите ниже
</td>
</tr>
<tr><td>icon</td><td>Класс иконки, который будет выводиться в секции для добавления нового блока, если в параметрах плагина значение &addType установлено в "icons"
  
Например, если задать класс `fa fa-cogs`, то в интерфейс будет выведено следующее:
```html
<i class="fa fa-cogs"></i>
```
</td></tr>
<tr><td>image</td><td>Изображение, которое будет выводиться в секции для добавления нового блока, если в параметрах плагина значение &addType установлено в "images".
  
Изображение будет обработано сниппетом `phpthumb` с параметром `w=80`</td></tr>
</table>

##Шаблоны

Основной шаблон блока должен быть определен для ключа "owner". Помимо него, массив должен содержать шаблоны для каждой группы полей, и может содержать шаблоны для полей, у которых определено свойство "elements" (это поля типа "dropdown", "checkbox", "radio"). В таких шаблонах доступны выбранные значения свойства "elements".

Например, если в массиве полей используется группа "images", то в шаблонах должен быть определен элемент с ключом "images", который будет содержать либо строку шаблона:

```php
'images' => '<img src="[+image+]" alt="[+title+]" class="slide">'
```

либо ассоциативный массив шаблонов:

```php
'images' => [
  'item'  => '<img src="[+image+]" alt="[+title+]" class="slide">',
  'thumb' => '<div class="thumb" style="background-image: url([+image+])"></div>',
],
```

Во втором случае вывод этих элементов в родительском шаблоне можно использовать как `[+images.item+]` и `[+images.thumb+]`.

##Плейсхолдеры

В качестве плейсхолдеров могут использоваться имена полей (напр. `[+title+]`), имена групповых полей (напр. `[+images+]`, `[+images.thumb+]`).

В шаблонах для полей выбора "dropdown", "checkbox", "radio" доступны плейсхолдеры `[+value+]` и `[+title+]`.

Также в шаблоне 'owner' доступны плейсхолдеры `[+index+]` и `[+iteration+]`, а в груповых полях и полях выбора - `[+{имя_поля}_index+]` и `[+{имя_поля}_iteration+]`.

##Источники шаблонов

Разметку можно указать в самом значении массива, как показано в примерах выше.

Возможно указание имени чанка, в котором находится нужный шаблон. Для этого нужно использовать привязку `@CHUNK`, например:

```php
'checkbox' => '@CHUNK all_fields_checkboxes',
```

Также возможна подгрузка шаблона из файла, например:

```php
'owner' => '@FILE contentblocks/all_fields.tpl',
```

В этом примере файл шаблона будем загружен из `MODX_BASE_PATH . "assets/templates/contentblocks/all_fields.tpl"`. Вообще файл  ищется в следующих директориях:

```
assets/tvs/
assets/chunks/
assets/templates/
```

Либо можно указать полный путь от корня сайта. Первый слеш не указывается.

##Группы шаблонов

Шаблоны можно группировать, чтобы при выводе использовать разные группы шаблонов с параметром `&templates`. Например, если указать следующую конфигурацию для блока:

```php
'templates' => [
  'owner'  => '@CHUNK full_owner',
  'images' => '@CHUNK full_images'

  'anchors' => [
    'owner' => '@CHUNK link_owner',
  ],
],
```

то вызов сниппета с параметром `&templates`, равным `anchors`, будет использовать для вывода шаблоны, которые определены в группе `anchors`:

```
[[ContentBlocks? &templates=`anchors`]]
```
##Поля

##Структура массива для описания поля

<table>
<tr><th>Ключ</th><th>Значение</th></tr>
<tr><td>caption</td><td>Название поля, которое видит менеджер. Необязательно</td></tr>
<tr><td>type</td><td>Тип поля, см. ниже</td></tr>
<tr><td>theme</td><td>Тема редактора для поля "richtext", доступные значения можно посмотреть в конфигурации Evolution CMS, на вкладке "Интерфейс"</td></tr>
<tr><td>options</td><td>Дополнительные опции для поля "richtext", значения можно посмотреть <a href="https://www.tinymce.com/docs/configure/" target="_blank">здесь</a></td></tr>
<tr><td>fields</td><td>Вложенные поля, для типа "group"</td></tr>
<tr><td>height</td><td>Высота поля, с указанием единиц измерения, например "150px". Доступно для типа поля "textarea".

Для полей "richtext" указывается в составе опций редактора, в ключе "options"</td></tr>
<tr><td>elements</td><td>Возможные значения для поля выбора. Доступны для полей "dropdown", "radio", "checkbox". Могут быть представлены в виде массива "ключ" => "значение", или в виде строки в доступном формате Evolution CMS (@SELECT и пр. работают).</td></tr>
<tr><td>layout</td><td>Вид расположения вариантов для полей "radio" и "checkbox". Возможные значения - "vertical" (по умолчанию) и "horizontal"</td></tr>
<tr><td>default</td><td>Значение по умолчанию. Для типа поля "checkbox" может быть массивом значений.

Возможно указание в формате "1||2||3", и использование привязок @SELECT, @EVAL и пр.</td></tr>
</table>

##Типы полей

<table>
<tr><th>Значение</th><th>Описание</th></tr>
<tr><td>text</td><td>Однострочное текстовое поле</td></tr>
<tr><td>image</td><td>Текстовое поле с миниатюрой и кнопкой для выбора изображения</td></tr>
<tr><td>richtext</td><td>Текстовый редактор TinyMCE 4</td></tr>
<tr><td>textarea</td><td>Многострочное текстовое поле</td></tr>
<tr><td>date</td><td>Текстовое поле с выпадающим календарем для выбора даты</td></tr>
<tr><td>dropdown</td><td>Выпадающий список</td></tr>
<tr><td>checkbox</td><td>Флажки, позволяет выбрать несколько вариантов из представленных</td></tr>
<tr><td>radio</td><td>Переключатели, позволяют выбрать только один вариант</td></tr>
<tr><td>group</td><td>Группа полей, обязательно должны быть определены вложенные поля в ключе "fields"</td></tr>
</table>

##Примеры конфигурации

Примеры конфигурации можно найти <a href="https://github.com/mnoskov/contentblocks/tree/master/assets/plugins/contentblocks/config" target="_blank">здесь</a>. (Чтобы примеры блоков стали доступны для выбора, нужно переименовать файлы *.php.sample в *.php)

##Сниппет ContentBlocks

Для вывода заполненых блоков используется сниппет ContentBlocks с параметрами:
<table>
<tr><th>Имя параметра</th><th>Значение по умолчанию</th><th>Возможные значения</th></tr>
<tr><td>docid</td><td>Текущий документ</td><td>Идентификатор любого существующего документа, целое число</td></tr>
<tr><td>blocks</td><td>*</td><td>Список блоков через запятую, без пробелов. Берется имя файла конфигурации без расширения (Например, 'all_fields,groups'). Если указать '*', фильтрация по имени произведена не будет</td></tr>
<tr><td>templates</td><td></td><td>Идентификатор группы шаблонов, которые будут использоваться для вывода. Должен быть определен в конфигурации каждого выводимого блока</td></tr>
<tr><td>offset</td><td>0</td><td>Число пропускаемых блоков с начала вывода</td></tr>
<tr><td>limit</td><td>0</td><td>Число блоков для вывода, либо 0 - для вывода всех</td></tr>
</table>

##Плагин ContentBlocks

Плагин отвечает за вывод формы редактирования блоков и имеет следующие параметры:
<table>
<tr><th>Имя параметра</th><th>Значение по умолчанию</th><th>Возможные значения</th></tr>
<tr><td>tabName</td><td>Content Blocks</td><td>Название вкладки на странице редактирования ресурса, в которой будет выводиться форма</td></tr>
<tr><td>addType</td><td>dropdown</td><td>Вид секции для добавления новых блоков, может иметь значения "dropdown", "icons", "images".
  
Для значения "icons" в конфигурации каждого блока должен быть определен ключ "icon", содержащий класс иконки.

Для значения "images" должен быть определен ключ "image", с адресом изображения (макс. 80х60)</td></tr>
<tr><td>placement</td><td>tab</td><td>Размещение формы: tab - в отдельной вкладке, content - под содержимым ресурса</td></tr>
</table>
