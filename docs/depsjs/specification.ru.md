# Спецификация DEPS

* [Введение](#Введение)
* [Используемые обозначения](#Используемые-обозначения)
* [Дополнительная информация](#Дополнительная-информация)
* [Cинтаксис DEPS](#Синтаксис-deps)
* [Поля DEPS-сущности](#Поля-deps)
* [Синтаксический сахар](#Синтаксический-сахар)
* [Примеры деклараций](#Примеры-деклараций)

## Введение

Данная спецификация предоставляет подробную информацию по DEPS.js.

DEPS — технология для описания зависимостей между БЭМ-сущностями.

Зависимости между БЭМ-сущностями описываются в виде JavaScript объекта в файлах с расширением `.deps.js`. Полная запись DEPS-сущности имеет следующий вид:

```js
/* DEPS-сущность */
({
    block : 'block-name',
    elem  : 'elem-name',
    mod   : 'modName',
    val   : 'modValue',
    tech  : 'techName',
    mustDeps   : [ /* БЭМ-сущность */ ],
    shouldDeps : [ /* БЭМ-сущность */ ],
    noDeps     : [ /* БЭМ-сущность */ ],
    include    : false
})
```

Все поля являются опциональными.

## Используемые обозначения

Для короткой записи зависимостей в комментариях используются следующие условные обозначения:

* `/* b1 ---> b2 */` — блок `b1` зависит от блока `b2`;
* `/* b1 --/-> b2 */` — блок `b1` не зависит от блока `b2`;
* `/* b1 ---> b1__e1 */` — блок `b1` зависит от своего элемента `b1__e1`;
* `/* b1 ---> b1__e1_m1_v1 */` — блок `b1` зависит от своего модификатора `b1__e1_m1_v1`;
* `/* b1(js) ---> b2(bemhtml) */` — блок `b1` в технологии реализации JavaScript зависит от блока `b2` в технологии реализации BEMHTML.

## Дополнительная информация

Документация по Deps.js включает:

* [Изучение механизма сборки БЭМ-проектов](https://ru.bem.info/methodology/build/) — предоставляет информацию для начинающих и вводит в базовые концепции DEPS.js.
* Руководство по DEPS.js — предоставляет обзор DEPS.js и его основных полей.

## Синтаксис DEPS

* [Терминология](#Терминология)
* [Структура DEPS документа](#Структура-deps-документа)

### Терминология

**DEPS-сущность** — сущность, определяющая зависимость между [БЭМ-сущностями](https://ru.bem.info/methodology/key-concepts/#БЭМ-сущность).

**Зависимость** — взаимосвязь, при которой реализация одной БЭМ-сущности полностью зависит от другой и подключает ее в сборку.

### Структура DEPS-документа

Представить DEPS-сущность в файле `.deps.js` можно несколькими способами:

1. Используя литерал массива `[]`.

  ```js
  [{
      /* Deps-сущность */
  }]
  ```

2. Используя `modules.exports`.

  ```js
  modules.exports = ({
      /* Deps-сущность */
  })
  ```

DEPS-сущность представляет собой JavaScript-объект, с определенными полями.

## Поля DEPS

Можно разделить на группы:

* Поля описывающие БЭМ-сущность, для которой определяется зависимость:
  * [block](#block)
  * [elem](#elem)
  * [mod](#mod)
  * [val](#val)
* Поле, описывающие технологию реализации БЭМ-сущности:
  * [tech](#tech)
* Поля определяющие зависимость:
  * [mustDeps](#mustdeps)
  * [shouldDeps](#shoulddeps)
  * [noDeps](#nodeps)
  * [include](#include)

### Поля, определяющие БЭМ-сущность

Поля `block`, `elem`, `mod`, `val` указывают, для какой сущности необходимо подключить зависимость. Они являются опциональными и могут быть восстановлены из контекста по имени файла, поэтому записи для файла `b1__e1_m1_v1.deps.js` эквивалентны:

```js
({
    block : 'b1',
    elem : 'e1',
    mod : 'm1',
    val : 'v1',
    mustDeps : { block : 'b2' }
})
```

```js
({
    mustDeps : { block : 'b2' }
})
```

#### block

Поле `block` задает имя блока.

Принимаемые типы данных:

```js
/* Строка */
({
    block: 'b1'
})
```

#### elem

Поле `elem` задает имя элемента.

Принимаемые типы данных:

```js
/* Строка */
({
    elem: 'e1'
})
```

#### mod

Поле `mod` задает имя модификатора.

Принимаемые типы данных:

```js
/* Строка */
({
    mod: 'm1'
})
```

#### val

Поле `val` задает значение модификатора. Не используется отдельно от поля `mod`. Если поле `val` не указано, считается что значение модификатора равно `true`.

Принимаемые типы данных:

```js
/* Строка */
({
    mod: 'm1',
    val: 'v1'
})

/* Boolean (Булев, Логический тип данных) */
({
    mod: 'm1',
    val: true
})
```

### Поле определяющее технологию реализации БЭМ-сущности

Поле `tech` позволяет описать зависимость между реализациями БЭМ-сущностей для конкретных технологий. В случае, когда `tech` не указана, зависимость называется общей и относится ко всем технологиям.

Подключение зависимостей по технологии используется, например, для того, чтобы собрать в клиентский JavaScript-бандл шаблоны только тех блоков, которые будут использованы в браузере. При такой схеме часть шаблонизации происходит на стороне сервера и, следовательно, некоторые шаблоны на клиенте никогда не используются.

#### tech

Принимаемые типы данных:

```js
/* Строка */
({
    tech: 'js'
})
```

**Примечание**: технология реализации БЭМ-сущностей может не совпадать с технологией реализации файлов. Например, технология JavaScript (`tech: 'js'`) на файловой системе может быть представлена файлами с расширением `.coffee`.

### Поля, определяющие зависимость

Поля `mustDeps`, `shouldDeps`, `noDeps` определяют зависимость. Поле `include` является вспомогательным полем, позволяющим изменить строгую зависимость на функциональную.

#### mustDeps

Поле `mustDeps` определяет зависимости, которые гарантировано попадут в результаты сборки до кода БЭМ-сущности, в которой эти зависимости объявляются.

Принимаемые типы данных:

```js
/* Массив объектов */
({
    mustDeps: [
        { /* DepsEntity */ },
        { /* DepsEntity */ }
    ]
})
```

#### shouldDeps

Поле `shouldDeps` определяет зависимости, порядок подключения которых не важен.

Принимаемые типы данных:

```js
/* Массив объектов */
({
    shouldDeps: [
        { /* DepsEntity */ },
        { /* DepsEntity */ }
    ]
})
```

#### noDeps

Поле `noDeps` отменяет зависимости для текущей БЭМ-сущности, описанные на более низких [уровнях переопределения](https://ru.bem.info/methodology/key-concepts/#Уровень-переопределения).

Принимаемые типы данных:

```js
/* Массив объектов */
({
    noDeps: [
        { /* DepsEntity */ },
        { /* DepsEntity */ }
    ]
})
```

#### include

Поле `include` принимает одно значение `false` и используется совместно с полем `mustDeps` или `shouldDeps`.

Позволяет определить, на что влияет описываемая зависимость:

* на состав и порядок подключения БЭМ-сущности в сборку;
* только на порядок подключения БЭМ-сущности в сборку.

Подключение произойдет в зависимости от выполнения определенных условий:

**include & mustDeps**

*b1.deps.js*

```js
({
    mustDeps: { block: 'b2' },
    include: false
})
```

Блок `b2` попадет в сборку в том случае, если у какого-то другого блока (объявленного ранее) также будет определена зависимость от блока `b2`. Блок `b2` будет определен раньше блока `b1`.

**include & shouldDeps**

*b1.deps.js*

```js
({
    block: 'b3',
    shouldDeps: { block: 'b2' },
    include: false
})
```

Блок `b2` попадет в сборку в том случае, если внутри блока `b1` будет определен блок `b3`.


## Синтаксический сахар

* [Структура DEPS-документа](#Структура-deps-документа-1)
* [elems](#elems)
* [mods](#mods)
* [elem](#elem-1)
* [mustDeps](#mustdeps-1)
* [shouldDeps](#shoulddeps-1)
* [noDeps](#nodeps-1)


### Структура DEPS-документа

Представить DEPS-сущность в файле `.deps.js` можно следующим образом:

```js
({
    /* Deps-сущность */
})
```

### elems

Поле `elems` позволяет подключить несколько элементов для блока и раскрывается в `shouldDeps`. Поэтому записи для файла `b1.deps.js` равнозначны:

```js
({
    /* b1 ---> b1__e1; b1 ---> b1__e2 */
    block: 'b1',
    elems: [
      'e1',
      'e2'
    ]
})
```

```js
({
    /* b1 ---> b1__e1; b1 ---> b1__e2 */
    block: 'b1',
    shouldDeps: [
      {
        elem: [
            'e1',
            'e2'
        ]
      }
    ]
})
```

Принимаемые типы данных:

```js
/* Строка */
({
    elems: 'e1'
})

/* Объект */
({
    elems: { /* elemDepsEntity */ }
})

/* Массив строк */
({
    elems: [
      'e1',
      'e2'
    ]
})

/* Массив объектов */
({
    elems: [
      { /* elemDepsEntity */ },
      { /* elemDepsEntity */ }
    ]
})
```

[Подробнее об elemDepsEntity](#elemdepsentity).

### mods

Поле `mods` задает имя и значение модификатора и раскрывается в `shouldDeps`, поэтому записи для файла `b1.deps.js` равнозначны:

```js
({
    /* b1 ---> b1_m1_v1; b1 ---> b1_m1_v2 */
    block: 'b1',
    mods: { m1: [
        'v1',
        'v2'
      ]
    }
})
```

```js
({
    /* b1 ---> b1_m1_v1; b1 ---> b1_m1_v2 */
    block: 'b1',
    shouldDeps: [
      {
        block: 'b1',
        mod: 'm1',
        val: 'v1'
      },
      {
        block: 'b1',
        mod: 'm1',
        val: 'v1'
      }
    ]
})
```

Принимаемые типы данных:

```js
/* Объект, где значением свойства может быть: */

/* Строка */
({
    mods: { m1: 'v1' }
})

/* Boolean (Булев, Логический тип данных) */
({
    mods: { m1: true }
})

/* Массив строк */
({
    mods: { m1: [
        'v1',
        'v2'
      ]
    }
})

/* Массив строк */
({
    mods: [
      'm1',
      'm2'
    ]
})
```

### elem

К синтаксическому сахару относятся следующие типы данных, принимаемые полем `elem`:

```js
/* Массив строк */
({
    elem: [
        'e1',
        'e2'
    ]
})
```

### mustDeps

К синтаксическому сахару относятся следующие типы данных:

```js
/* Строка */
({
    mustDeps: 'b2'
})

/* Объект */
({
    mustDeps: { /* DepsEntity */ }
})

/* Массив строк */
({
    mustDeps: [
        'b2',
        'b3'
    ]
})
```

### shouldDeps

К синтаксическому сахару относятся следующие типы данных:

```js
/* Строка */
({
    shouldDeps: 'b2'
})

/* Объект */
({
    shouldDeps: { /* DepsEntity */ }
})

/* Массив строк */
({
    shouldDeps: [
        'b2',
        'b3'
    ]
})
```

### noDeps

К синтаксическому сахару относятся следующие типы данных:

```js
/* Строка */
({
    noDeps: 'b2'
})

/* Объект */
({
    noDeps: { /* DepsEntity */ }
})

/* Массив строк */
({
    noDeps: [
        'b2',
        'b3'
    ]
})
```

## Примеры деклараций

  * [block](#block-1)
  * [elem](#elem-2)
  * [mod](#mod-1)
  * [val](#val-1)
  * [tech](#tech-1)
  * [mustDeps](#mustdeps-2)
  * [shouldDeps](#shoulddeps-2)
  * [noDeps](#nodeps-2)
  * [include](#include-1)
  * [elems](#elems-1)
  * [mods](#mods-1)

### block

```js
({
    /* Блок b1 */
    block: 'b1'
})
```

### elem

```js
({
    /* Элемент b1__e1 */
    block: 'b1',   
    elem: 'e1'
})
```

### mod

```js
({
    /* Модификатор b1__e1_m1 со значением true */
    block: 'b1',   
    elem: 'e1',
    mod: 'm1'
})
```

### val

```js
({
    /* Модификатор b1__e1_m1_v1 со значением v1 */
    block: 'b1',   
    elem: 'e1',
    mod: 'm1',
    val: 'v1'
})
```

### tech

```js
({
    /* Блок b1 в технологии js */
    block: 'b1',
    tech: 'js'
})
```

### mustDeps

```js
({
    /* b1 ---> b2 */
    block: 'b1',
    mustDeps: [
        { block: 'b2' }
    ]
})
```

Блок `b2` попадет в результат сборки до кода блок `b1`.

### shouldDeps

```js
({
      /* b1 ---> b2 */
    block: 'b1',
    shouldDeps: [
        { block: 'b2' }
    ]
})
```

### noDeps

```js
/* common.blocks */
({
    /* b1 ---> b2 */
    block: 'b1',
    shouldDeps: [
        { block: 'b2' }
    ]
})

/* desktop.blocks */
({
    /* b1 --/-> b2 */
    block: 'b1',
    noDeps: [
        { block: 'b2' }
    ]
})
```

### include

```js
({
    /* b1 --(f)-> b2 */
    block: 'b1',
    mustDeps: { block: 'b2' },
    include: false
})
```

### elems

```js
({
    /* b1 ---> b1__e1; b1 ---> b1__e2 */
    block: 'b1',   
    elems: [
      'e1',
      'e2'
    ]
})
```

### mods

```js
({
    /* b1__e1 ---> b1__e1_m1_v1 */
    block: 'b1',   
    elem: 'e1',
    mods: { m1: 'v1' }
})
```

## elemDepsEntity

<table>
<tr>
<th>elemDepsEntity</th>
<th colspan="2">Пример</th>
</tr>
<tr>
<td  colspan="3"><strong>Обязательные поля</strong></td>
</tr>
<tr>
<td>elem</td>
<td>
Принимаемые типы данных:</br>
Строка.</br>
<pre>
<code>
({
    elem: 'e1'
})
</code>
</pre>
Массив строк.</br>
<pre>
<code>
({
    elem: [
        'e1',
        'e2'
    ]
})
</code>
</pre>
</td>
</tr>
<tr>
<td  colspan="3"><strong>Опциональные поля</strong></td>
</tr>
<tr>
<td>mods</td>
<td  colspan="2">
<pre>
<code>
({
    elem: 'e1',
    mods: { m1: 'v1' }
})
</code>
</pre>
</td>
</tr>
<tr>
<td>tech</td>
<td  colspan="2">
<pre>
<code>
({
    elem: 'e1',
    tech: 'js'
})
</code>
</pre>
</td>
</tr>
<tr>
<td>mustDeps</td>
<td  colspan="2">
<pre>
<code>
({
    elem: 'e1',
    mustDeps: 'b2'
})
</code>
</pre>
</td>
</tr>
<tr>
<td>shouldDeps</td>
<td  colspan="2">
<pre>
<code>
({
    elem: 'e1',
    shouldDeps: 'b2'
})
</code>
</pre>
</td>
</tr>
<tr>
<td>noDeps</td>
<td  colspan="2">
<pre>
<code>
({
    elem: 'e1',
    noDeps: 'b2'
})
</code>
</pre>
</td>
</tr>
<tr>
<td>include</td>
<td  colspan="2">
<pre>
<code>
({
    elem: 'e1',
    mustDeps: { block: 'b1' },
    include: false
})
</code>
</pre>
</td>
</tr>
</table>