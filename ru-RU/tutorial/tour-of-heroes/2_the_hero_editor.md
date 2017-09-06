Если вы верно прошли шаг первый то структура проекта у вас должна выглядеть следующим образом.

`Тут картинка или схема`

# Запуск приложения

Для того чтобы запустить наше приложение в командной строке необходимо ввести `basis server`.
После этого запустится сервер в watch mode и будет реагировать на сохранение файлов.

Для файлов форматов `*.css, *.i10n, *.tmpl` работает `HMR` (Hot module replacment)  и нет необходимости перезагружать бразуер в случае изменения файлов этих форматов.

В случае изменения JS файлов необходимо перезагрузить браузер вручную.

# Старт

Прежде всего нам необходимо научиться переносить наши данные из Basis в DOM браузера. Давайте сделаем это на примере нашего супергероя.

Для начала опишем шаблон куда будем выводить наши данные в файле `layout.tmpl`


```html
<div>
	<h1>{title}</h1>
	<h2>{name} is a hero!</h2>
	<div><label>Id: </label>{id}</div>
	<div>
		<label>Name: </label>
		<input value="{name}" placeholder="Enter name">
	</div>
</div>
```

`{name}` - области куда буду рендериться наши данные.
Сама связь между шаблоном и компонентом basis задается через свойство binding унаследованного от `basis.ui.node`.

Теперь добавим кода данных в наш `app.js`

```js
var Node = require('basis.ui').Node;

module.exports = require('basis.app').create({
  title: 'Basisjs tour of heroes',

  init: function() {
    return new Node({
	  template: resource('./app/template/layout.tmpl'),
	  data: {
		title: 'Basis tour of heroes',
		hero: {
			id: 1,
			name: 'Superhero'
		}
	  },
	  binding: {
	  	id: 'data:hero.id',
	  	name: 'data:hero.name',
	  	title: 'data:'
	  }
    });
  }
});
```

Тут нас интересуют прежде всего два поля - `data` и `binding`.

В `binding` мы указываем в ключах объекта то, что будем использовать в шаблонах в фигурных скобках `{}`, а в качестве значений указываем то эти данные непосредственно получить.

Напрмер:

```js
binding: {
	name: 'data:hero.name'
}
```

биндинг `name` берётся из поля data и далее обращается к полю `name` объекта `hero`. Таким образом происходит привязка данных из data в DOM. Тоже самое и с `id`.

Поле title это отдельный случай. Если название биндинга совпадает с полем в `data`, то можно использовать коротокую запись.

```js
binding: {
	title: 'data:'
}
```

Тогда значение title также возмётся из `data`, как если бы мы написали полный путь `data:title`

Теперь добавим возможность редактирования.

В первую очередь нам необходимо отлавливать событие ввода данных в нашем `input`. Для того чтобы отлавливать бразерные события в шаблонах используется следующий синтаксис:

```
event-typeEvent="eventHandler"
```

Т.e. в нашем шаблоне это будет выглядеть так примерно так:

```html
<input value="{name}" event-input="setHeroName" placeholder="Enter name">
```

В самом компоненте есть свойство `action` которое также унаследовано от `basis.ui.node` в котором необходимо указать обработчик `setHeroName`.

Для обновления `data` используется метод `update` от `basis.ui.node`, который обновляет данные в `data` и неявно вызывает обновление в DOM биндинга.

```js
action: {
	setHeroName: function(e) {
	  this.update({
	    hero: {
		  id: this.data.hero.id,
		  name: e.sender.value
	    }
      });
  }
}
```

На самом деле, при таком способе обновления многое из того что происходит скрыто под капотом. Более подробно о том как это все происходит изнутри вы можете почитать в [документации](https://github.com/basisjs/articles/blob/master/ru-RU/tutorial/part1/index.md#Биндинги-и-действия).

В итоге получаем живое редактирование героя прямо на странице!

Ссылка на готовый пример: [Part2](https://github.com/prostoandrei/basis-tour-of-heroes/tree/part2)