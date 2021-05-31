---
layout: default
title: Markdown kitchen sink
nav_order: 99
---

Text can be **bold**, _italic_, or ~~strikethrough~~.

[Link to another page](another-page).

There should be whitespace between paragraphs.

There should be whitespace between paragraphs. We recommend including a README, or a file with information about your project.

# [](#header-1)Header 1

This is a normal paragraph following a header. GitHub is a code hosting platform for version control and collaboration. It lets you and others work together on projects from anywhere.

## [](#header-2)Header 2

> This is a blockquote following a header.
>
> When something is important enough, you do it even if the odds are not in your favor.

### [](#header-3)Header 3

```js
// Javascript code with syntax highlighting.
var fun = function lang(l) {
  dateformat.i18n = require('./lang/' + l)
  return true;
}
```

```ruby
# Ruby code with syntax highlighting
GitHubPages::Dependencies.gems.each do |gem, version|
  s.add_dependency(gem, "= #{version}")
end
```

#### [](#header-4)Header 4 `with code not transformed`

*   This is an unordered list following a header.
*   This is an unordered list following a header.
*   This is an unordered list following a header.

##### [](#header-5)Header 5

1.  This is an ordered list following a header.
2.  This is an ordered list following a header.
3.  This is an ordered list following a header.

###### [](#header-6)Header 6

| head1        | head two          | three |
|:-------------|:------------------|:------|
| ok           | good swedish fish | nice  |
| out of stock | good and plenty   | nice  |
| ok           | good `oreos`      | hmm   |
| ok           | good `zoute` drop | yumm  |

### There's a horizontal rule below this.

* * *

### Here is an unordered list:

*   Item foo
*   Item bar
*   Item baz
*   Item zip

### And an ordered list:

1.  Item one
1.  Item two
1.  Item three
1.  Item four

### And a nested list:

- level 1 item
  - level 2 item
  - level 2 item
    - level 3 item
    - level 3 item
- level 1 item
  - level 2 item
  - level 2 item
  - level 2 item
- level 1 item
  - level 2 item
  - level 2 item
- level 1 item

### Nesting an ol in ul in an ol

- level 1 item (ul)
  1. level 2 item (ol)
  1. level 2 item (ol)
    - level 3 item (ul)
    - level 3 item (ul)
- level 1 item (ul)
  1. level 2 item (ol)
  1. level 2 item (ol)
    - level 3 item (ul)
    - level 3 item (ul)
  1. level 4 item (ol)
  1. level 4 item (ol)
    - level 3 item (ul)
    - level 3 item (ul)
- level 1 item (ul)

### And a task list

- [ ] Hello, this is a TODO item
- [ ] Hello, this is another TODO item
- [x] Goodbye, this item is done

### Small image

![](https://assets-cdn.github.com/images/icons/emoji/octocat.png)

### Large image

![](https://guides.github.com/activities/hello-world/branching.png)


### Definition lists can be used with HTML syntax.

<dl>
<dt>Name</dt>
<dd>Godzilla</dd>
<dt>Born</dt>
<dd>1952</dd>
<dt>Birthplace</dt>
<dd>Japan</dd>
<dt>Color</dt>
<dd>Green</dd>
</dl>

#### Multiple description terms and values

Term
: Brief description of Term

Longer Term
: Longer description of Term,
  possibly more than one line

Term
: First description of Term,
  possibly more than one line

: Second description of Term,
  possibly more than one line

Term1
Term2
: Single description of Term1 and Term2,
  possibly more than one line

Term1
Term2
: First description of Term1 and Term2,
  possibly more than one line

: Second description of Term1 and Term2,
  possibly more than one line
  
### More code

```
Long, single-line code blocks should not wrap. They should horizontally scroll if they are too long. This line should be long enough to demonstrate this.
```

```
The final element.
```

# Маршрутизация запросов к данных

Запросы к данным маршрутизируются следующим образом:
*   если в запросе указана [СУБД](../../../Введение/Поддерживаемые_СУБД_хранилища/Поддерживаемые_СУБД_хранилища.md) 
    [хранилища](../../../Обзор_понятий_компонентов_и_связей/Основные_понятия/Хранилище_данных/Хранилище_данных.md) 
    (см. [DATASOURCE_TYPE](../../../Справочная_информация/Запросы_SQLplus/SELECT/SELECT.md#param_datasource_type)), в которой должен быть выполнен запрос, запрос направляется 
    в указанную СУБД;
*   иначе запрос маршрутизируется автоматически на основе его категории в соответствии 
    с [конфигурацией](../../../Эксплуатация/Конфигурация/Конфигурация.md) системы.

По умолчанию в конфигурации определен следующий порядок выбора СУБД для исполнения запросов:
*   реляционные запросы — запросы с операторами JOIN и (или) подзапросами — направляются в ADB;
*   запросы агрегации и группировки данных направляются в ADQM;
*   запросы точечного чтения по ключу направляются в ADG;
*   запросы, не соответствующие ни одной из предыдущих категорий, направляются в ADB.

Категория запроса определяется в указанном выше порядке. Например, если запрос содержит оператор JOIN, 
то он попадает в первую категорию независимо от наличия агрегации, группировки и чтения по ключу. 
Примеры запросов каждой из категорий см. [ниже](#примеры-запросов-различных-категорий).

Указанный порядок выбора СУБД эффективно использует возможности каждой из СУБД хранилища, однако 
при необходимости его можно изменить в конфигурации системы.

**Примечание:** наиболее полный синтаксис запросов доступен в ADB. ADG и ADQM имеют ограничения 
на выполнение запросов, вызванные особенностями этих СУБД (см. [Поддержка SQL](../../../Справочная_информация/Поддержка_SQL/Поддержка_SQL.md)).

## Примеры запросов различных категорий

Реляционный запрос:
```sql
SELECT * FROM sales.sales AS s
JOIN sales.stores AS st
ON s.store_id = st.identification_number
```

Реляционный запрос с агрегацией, группировкой и чтением по ключу (`st.identification_number`):
```sql
SELECT
st.identification_number,
st.category,
SUM(s.product_units) AS product_amount
FROM sales.stores AS st
JOIN sales.sales AS s
ON st.identification_number = s.store_id
WHERE st.identification_number <> 10004
GROUP BY st.identification_number, st.category
ORDER BY product_amount DESC
```

Запрос агрегации и группировки:
```sql
SELECT s.product_code, SUM(s.product_units) AS product_amount
FROM sales.sales AS s
GROUP BY s.product_code
ORDER BY product_amount ASC
```

Запрос агрегации и группировки с чтением по ключу (`s.identification_number`):
```sql
SELECT s.product_code, SUM(s.product_units) AS product_amount
FROM sales.sales AS s
WHERE s.identification_number > 20000
GROUP BY s.product_code
```

Запрос чтения по ключу:
```sql
SELECT * FROM sales.sales as s
WHERE s.identification_number BETWEEN 1001 AND 2000
```

Запрос неопределенной категории:
```sql
SELECT *
FROM sales.sales AS s
WHERE s.product_units > 2  
```