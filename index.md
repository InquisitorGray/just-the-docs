---
layout: default
title: Home
nav_order: 1
description: "Just the Docs is a responsive Jekyll theme with built-in search that is easily customizable and hosted on GitHub Pages."
permalink: /
---

# Focus on writing good documentation
{: .fs-9 }

Just the Docs gives your documentation a jumpstart with a responsive Jekyll theme that is easily customizable and hosted on GitHub Pages.
{: .fs-6 .fw-300 }

[Get started now](#getting-started){: .btn .btn-primary .fs-5 .mb-4 .mb-md-0 .mr-2 } [View it on GitHub](https://github.com/pmarsceill/just-the-docs){: .btn .fs-5 .mb-4 .mb-md-0 }

---

## Getting started

### Dependencies

Just the Docs is built for [Jekyll](https://jekyllrb.com), a static site generator. View the [quick start guide](https://jekyllrb.com/docs/) for more information. Just the Docs requires no special plugins and can run on GitHub Pages' standard Jekyll compiler. The [Jekyll SEO Tag plugin](https://github.com/jekyll/jekyll-seo-tag) is included by default (no need to run any special installation) to inject SEO and open graph metadata on docs pages. For information on how to configure SEO and open graph metadata visit the [Jekyll SEO Tag usage guide](https://jekyll.github.io/jekyll-seo-tag/usage/).

### Quick start: Use as a GitHub Pages remote theme

1. Add Just the Docs to your Jekyll site's `_config.yml` as a [remote theme](https://blog.github.com/2017-11-29-use-any-theme-with-github-pages/)
```yaml
remote_theme: pmarsceill/just-the-docs
```
<small>You must have GitHub Pages enabled on your repo, one or more Markdown files, and a `_config.yml` file. [See an example repository](https://github.com/pmarsceill/jtd-remote)</small>

### Local installation: Use the gem-based theme

1. Install the Ruby Gem
```bash
$ gem install just-the-docs
```
```yaml
# .. or add it to your your Jekyll site’s Gemfile
gem "just-the-docs"
```
2. Add Just the Docs to your Jekyll site’s `_config.yml`
```yaml
theme: "just-the-docs"
```
3. _Optional:_ Initialize search data (creates `search-data.json`)
```bash
$ bundle exec just-the-docs rake search:init
```
3. Run you local Jekyll server
```bash
$ jekyll serve
```
```bash
# .. or if you're using a Gemfile (bundler)
$ bundle exec jekyll serve
```
4. Point your web browser to [http://localhost:4000](http://localhost:4000)

If you're hosting your site on GitHub Pages, [set up GitHub Pages and Jekyll locally](https://help.github.com/en/articles/setting-up-your-github-pages-site-locally-with-jekyll) so that you can more easily work in your development environment.

### Configure Just the Docs

- [See configuration options]({{ site.baseurl }}{% link docs/configuration.md %})

---

## About the project

Just the Docs is &copy; 2017-{{ "now" | date: "%Y" }} by [Patrick Marsceill](http://patrickmarsceill.com).

### License

Just the Docs is distributed by an [MIT license](https://github.com/pmarsceill/just-the-docs/tree/master/LICENSE.txt).

### Contributing

When contributing to this repository, please first discuss the change you wish to make via issue,
email, or any other method with the owners of this repository before making a change. Read more about becoming a contributor in [our GitHub repo](https://github.com/pmarsceill/just-the-docs#contributing).

#### Thank you to the contributors of Just the Docs!

<ul class="list-style-none">
{% for contributor in site.github.contributors %}
  <li class="d-inline-block mr-1">
     <a href="{{ contributor.html_url }}"><img src="{{ contributor.avatar_url }}" width="32" height="32" alt="{{ contributor.login }}"/></a>
  </li>
{% endfor %}
</ul>

### Code of Conduct

Just the Docs is committed to fostering a welcoming community.

[View our Code of Conduct](https://github.com/pmarsceill/just-the-docs/tree/master/CODE_OF_CONDUCT.md) on our GitHub repository.


# SELECT

Запрос позволяет выбрать данные из [логических таблиц](../../../overview/main_concepts/logical_table/logical_table.md), 
[логических представлений](../../../overview/main_concepts/logical_view/logical_view.md) 
и (или) [материализованных представлений](../../../overview/main_concepts/materialized_view/materialized_view.md). 
Запрос можно использовать как самостоятельный запрос для [чтения данных](../../../working_with_system/data_reading/data_reading.md), 
а также в качестве подзапроса в следующих запросах:
*   в [запросе на выгрузку данных](../INSERT_INTO_download_external_table/INSERT_INTO_download_external_table.md),
*   в запросе на [создание](../CREATE_VIEW/CREATE_VIEW.md) или [обновление](../ALTER_VIEW/ALTER_VIEW.md) логического представления,
*   в запросе на [создание материализованного представления](../CREATE_MATERIALIZED_VIEW/CREATE_MATERIALIZED_VIEW.md).

С помощью запроса можно выбрать данные любой из категорий:
*   актуальные данные;
*   архивные данные, которые были актуальны в указанный момент времени;
*   изменения данных, выполненные в рамках указанного диапазона [дельт](../../../overview/main_concepts/delta/delta.md).

Запрос обрабатывается в порядке, описанном в разделе [Порядок обработки запросов на чтение данных](../../../overview/interactions/llr_processing/llr_processing.md).

В ответе возвращается:
*   объект ResultSet c выбранными записями при успешном выполнении запроса;
*   исключение при неуспешном выполнении запроса.

## Синтаксис

```sql
SELECT column_list
FROM [db_name.]entity_name
[FOR SYSTEM_TIME time_expression [AS alias_name]]
[DATASOURCE_TYPE = datasource_alias]
```

Описание параметров запроса см. [ниже](#параметры).

В запросе можно использовать следующие секции, которые должны быть указаны в порядке их перечисления:
*   `FOR SYSTEM_TIME` — для указания момента времени или периода, за который выбираются данные или 
    изменения данных. Если секция не указана, из логической таблицы и логического представления 
    выбираются данные, актуальные на момент обработки запроса, из материализованного представления  
    — данные, актуальные на момент последней синхронизации представления;
*   `JOIN ON` — для соединения данных нескольких логических таблиц или представлений;
*   `WHERE` — для указания условий выбора данных;
*   `GROUP BY` — для группировки данных;
*   `HAVING` — для указания условий выбора сгруппированных данных;
*   `ORDER BY` — для сортировки данных;
*   `LIMIT` — для ограничения количества возвращаемых строк;
*   <a id="param_datasource_type"></a>`DATASOURCE_TYPE` — для указания [СУБД](../../../introduction/supported_DBMS/supported_DBMS.md) 
    [хранилища](../../../overview/main_concepts/data_storage/data_storage.md), 
    из которой выбираются данные.

Для имен таблиц, представлений и столбцов можно использовать псевдонимы. В запросе доступны оператор 
`DISTINCT`, агрегатные функции, а также соединение нескольких логических таблиц или логических 
представлений из одной или нескольких [логических БД](../../../overview/main_concepts/logical_db/logical_db.md).

Поддерживаются следующие типы соединений:
*   `[INNER]` — внутреннее соединение,
*   `NATURAL` — внутреннее соединение по всем столбцам с одинаковыми именами, ключи соединения 
    не указываются,
*   `LEFT [OUTER]` — левое внешнее соединение,
*   `RIGHT [OUTER]` — правое внешнее соединение,
*   `FULL [OUTER]` — полное внешнее соединение,
*   `CROSS` — декартово произведение таблиц или представлений, ключи соединения не указываются.

**Примечание:** некоторые агрегатные функции и типы соединений недоступны для исполнения в определенных 
СУБД хранилища. Список доступных возможностей см. в разделе [Поддержка SQL](../../sql_support/sql_support.md).

## Параметры

*   `column_list` — список выбираемых столбцов таблицы или представления. Допустимо указывать символ `*` 
    для выбора всех столбцов;
*   `db_name` — имя логической базы данных, из которой выбираются данные. Указывается опционально, если 
    выбрана логическая БД, [используемая по умолчанию](../../../working_with_system/other_features/default_db_set-up/default_db_set-up.md);
*   `entity_name` — имя таблицы или представления, из которого выбираются данные;
*   `time_expression` — выражение, которое задает момент или период времени, за который выбираются 
    данные или изменения данных. Формат выражения см. [ниже](#sect_for_system_time);
*   `alias_name` — псевдоним таблицы или представления. Может включать латинские буквы, цифры и символы 
    подчеркивания;
*   `datasource_alias` — системный псевдоним СУБД хранилища, из которой выбираются данные. Возможные 
    значения: `'adb'`, `'adqm'`, `'adg'`.

<a id="sect_for_system_time"></a>
## Синтаксис директивы FOR SYSTEM_TIME

Директива `FOR SYSTEM_TIME` позволяет указать момент, по состоянию на который запрашиваются данные, 
или период (диапазон дельт), за который запрашиваются изменения. Директива относится к логической таблице, 
логическому представлению или материализованному представлению, после имени которого она следует. Если в запросе 
соединяется несколько логических таблиц и представлений, для каждой логической сущности можно указать свою директиву 
`FOR SYSTEM_TIME`, при этом значения директив могут различаться (см. пример [ниже](#ex_different_system_times)).

**Примечание:** в зависимости от наличия и значения директивы `FOR SYSTEM_TIME` для материализованного представления 
запрос маршрутизируется по-разному (см. раздел [Маршрутизация запросов к данным материализованных представлений](../../../working_with_system/data_reading/routing/routing.md#sect_mat_view_routing)).

Директива указывается в формате `FOR SYSTEM_TIME time_expression`, где выражение `time_expression` 
принимает одно из следующих значений:
*   `AS OF 'YYYY-MM-DD hh:mm:ss'` — для запроса данных, актуальных на указанную дату и время. Возможные форматы 
    даты и времени см. в разделе [Форматы даты и времени в запросах](../../timestamp_formats/timestamp_formats.md);
*   `AS OF DELTA_NUM delta_num` — для запроса данных, актуальных на дату и время закрытия дельты 
    с номером `delta_num`;
*   `AS OF LATEST_UNCOMMITTED_DELTA` — для запроса данных на текущий момент, включая данные, загруженные 
    в рамках открытой (горячей) дельты. По горячей дельте возвращаются записи, загруженные в рамках 
    непрерывного диапазона завершенных операций записи (см. параметры `cn_from` и `cn_to` 
    в разделе [GET_DELTA_HOT](../GET_DELTA_HOT/GET_DELTA_HOT.md));
*   `STARTED IN (delta_num1, delta_num2)` — для запроса данных, добавленных или измененных в период 
    между дельтой `delta_num1` и дельтой `delta_num2` (включительно);
*   `FINISHED IN (delta_num1, delta_num2)` — для запроса данных, удаленных в период между дельтой 
    `delta_num1` и дельтой `delta_num2` (включительно).

Следующие значения директивы не поддерживаются в запросах к материализованным представлениям:
* `FOR SYSTEM_TIME AS OF LATEST_UNCOMMITTED_DELTA`;
* `FOR SYSTEM_TIME AS OF STARTED IN (delta_num1, delta_num2)`, если хотя бы одна дельта из диапазона 
  отсутствует в материализованном представлении;   
* `FOR SYSTEM_TIME AS OF FINISHED IN (delta_num1, delta_num2)`, если хотя бы одна дельта из диапазона 
  отсутствует в материализованном представлении.
  
## Ограничения

*   Запрос может обращаться либо к логической БД, либо к сервисной БД (см. [SELECT FROM INFORMATION_SCHEMA](../SELECT_FROM_INFORMATION_SCHEMA/SELECT_FROM_INFORMATION_SCHEMA.md)), 
    но не к обеим одновременно.
*   Если ключами секции `JOIN` выступают поля типа Nullable, то строки, где хотя бы один из ключей 
    имеет значение NULL, не соединяются.
*   Для SELECT-подзапроса, указываемого в составе запроса [CREATE MATERIALIZED VIEW](../CREATE_MATERIALIZED_VIEW/CREATE_MATERIALIZED_VIEW.md), 
    не поддерживается оператор `ORDER BY`. 

## Примеры

Запрос с неявным указанием столбцов и секцией `WHERE`:
```sql
SELECT * FROM sales.sales
WHERE store_id = 1234
```

Запрос с явным указанием столбцов и выбором данных из определенной СУБД хранилища (ADQM):
```sql
SELECT sold.store_id, sold.product_amount
FROM sales.stores_by_sold_products AS sold
DATASOURCE_TYPE = 'adqm'
```

Запрос с агрегацией, группировкой и сортировкой данных, а также выбором первых 20 строк:
```sql
SELECT s.store_id, SUM(s.product_units) AS product_amount
FROM sales.sales AS s
GROUP BY (s.store_id)
ORDER BY product_amount DESC
LIMIT 20
```

Запрос записей, актуальных на момент закрытия дельты с номером 9, из материализованного представления:
```sql
SELECT * FROM sales.sales_and_stores FOR SYSTEM_TIME AS OF DELTA_NUM 9
```

Запрос с соединением данных двух логических таблиц из двух различных логических БД:
```sql
SELECT
  st.identification_number,
  st.category,
  s.product_code
FROM sales.stores FOR SYSTEM_TIME AS OF LATEST_UNCOMMITTED_DELTA AS st
INNER JOIN sales2.sales FOR SYSTEM_TIME AS OF LATEST_UNCOMMITTED_DELTA AS s
  ON st.identification_number = s.store_id
```

<a id="ex_different_system_times"></a>
Запрос с соединением записей логической таблицы, добавленных и измененных в двух различных диапазонах 
дельт:
```sql
-- выбор логической базы данных sales в качестве базы данных по умолчанию
use sales

-- запрос данных из логической таблицы prices
SELECT
  p1.product_code,
  p1.price as feb_price,
  p2.price as march_price,
  (p2.price - p1.price) as diff
FROM
  (SELECT product_code,
  price from sales.prices
  FOR SYSTEM_TIME STARTED IN(3,6)) AS p1
FULL JOIN (select product_code,
  price from sales.prices
  FOR SYSTEM_TIME STARTED IN(7,10)) AS p2
  ON p1.product_code = p2.product_code
WHERE p1.product_code is NOT NULL
ORDER BY diff DESC
LIMIT 50
DATASOURCE_TYPE = 'adb'
```
