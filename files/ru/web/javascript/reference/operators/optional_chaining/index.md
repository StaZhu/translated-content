---
title: Оператор опциональной последовательности
slug: Web/JavaScript/Reference/Operators/Optional_chaining
---

{{jsSidebar("Operators")}}

Оператор **опциональной последовательности** **`?.`** позволяет получить значение свойства, находящегося на любом уровне вложенности в цепочке связанных между собой объектов, без необходимости проверять каждое из промежуточных свойств в ней на существование. `?.` работает подобно оператору `.`, за исключением того, что не выбрасывает исключение, если объект, к свойству или методу которого идёт обращение, равен {{jsxref("null")}} или {{jsxref("undefined")}}. В этих случаях он возвращает `undefined`.

Таким образом, мы получаем более короткий и понятный код при обращении к вложенным по цепочке свойствам объекта, когда есть вероятность, что какое-то из них отсутствует.

{{InteractiveExample("JavaScript Demo: Expressions - Optional chaining operator", "taller")}}

```js interactive-example
const adventurer = {
  name: "Alice",
  cat: {
    name: "Dinah",
  },
};

const dogName = adventurer.dog?.name;
console.log(dogName);
// Expected output: undefined

console.log(adventurer.someNonExistentMethod?.());
// Expected output: undefined
```

## Синтаксис

```js
obj?.prop;
obj?.[expr];
arr?.[index];
func?.(args);
```

## Описание

Оператор опциональной последовательности предоставляет способ упростить доступ к значениям в цепочке объектов, когда возможно, что какое-то свойство (или метод) в ней равно `undefined` или `null`.

Для примера, создадим объект `obj`, имеющий вложенную структуру. Без оператора опциональной последовательности поиск глубоко расположенных подсвойств требует проверки всех промежуточных свойств на существование, например:

```js
let nestedProp = obj.first && obj.first.second;
```

Если обращаться к `obj.first.second` без проверки `obj.first`, то, если свойство `obj.first` равно `null` или `undefined`, выбросится исключение {{jsxref("TypeError")}}.

Однако, с оператором опциональной последовательности (`?.`) не требуется явно проверять ссылку на `obj.first` перед обращением к `obj.first.second`:

```js
let nestedProp = obj.first?.second;
```

Если используется оператор `?.` вместо `.`, JavaScript знает о необходимости проверки `obj.first` перед обращением к `obj.first.second`. Если значение `obj.first` равно `null` или `undefined`, выполнение выражения автоматически прекращается и возвращается `undefined`.

Это эквивалентно следующему (кроме создания временной переменной):

```js
let temp = obj.first;
let nestedProp = temp === null || temp === undefined ? undefined : temp.second;
```

### Опциональная последовательность с вызовом функции

Вы можете использовать `?.`**,** когда необходимо вызвать метод, которого может не существовать. Это может быть полезно, например, при использовании API, в котором метод может быть недоступен из-за устаревания или не поддерживаемости устройством пользователя.

Использование `?.` с вызовом функции значит, что выполнение автоматически вернёт `undefined`, а не выбросит исключение, если метод не найден:

```js
let result = someInterface.customMethod?.();
```

> [!NOTE]
> Для существующего свойства, не являющегося функцией, использование конструкции `x.y?.()` всё равно выбросит {{jsxref("TypeError")}} исключение (`x.y не является функцией`).

### Работа с колбэк-функциями и обработчиками событий

Если вы используете колбэк-функции или извлекаете методы объекта [деструктурирующим присваиванием](/ru/docs/Web/JavaScript/Reference/Operators/Destructuring#разбор_объектов), Вы можете получить несуществующие значения, которые нельзя вызывать как функции до проверки на их существование. Используя оператор `?.`, вы можете избежать лишних проверок:

```js
// С использованием ES2019
function doSomething(onContent, onError) {
  try {
    // ... делаем что-то с данными
  } catch (err) {
    if (onError) {
      // проверяем, существует ли onError
      onError(err.message);
    }
  }
}
```

```js
// С использованием оператора опциональной последовательности
function doSomething(onContent, onError) {
  try {
    // ... делаем что-то с данными
  } catch (err) {
    onError?.(err.message); // не выбросит исключение, если onError равен undefined
  }
}
```

### Опциональные последовательности в выражениях

Вы также можете использовать оператор опциональной последовательности, когда обращаетесь к свойству с помощью [скобочной нотации](/ru/docs/Web/JavaScript/Reference/Operators/Property_accessors#%d0%a1%d0%ba%d0%be%d0%b1%d0%be%d1%87%d0%bd%d0%b0%d1%8f_%d0%bd%d0%be%d1%82%d0%b0%d1%86%d0%b8%d1%8f):

```js
let nestedProp = obj?.["prop" + "Name"];
```

## Примеры

### Базовый пример

В этом примере производится обращение к свойству `name` элемента с ключом `bar` объекта `Map`. Элемент с таким ключом отсутствует, но исключение выброшено не будет; `nameBar` равен `undefined`.

```js
let myMap = new Map();
myMap.set("foo", { name: "baz", desc: "inga" });

let nameBar = myMap.get("bar")?.name;
```

### Сокращённое выполнение

При использовании оператора опциональной последовательности в выражениях, где левая часть операнда равна `null` или `undefined`, выражение не будет выполнено. Например:

```js
let potentiallyNullObj = null;
let x = 0;
let prop = potentiallyNullObj?.[x++];

console.log(x); // 0, т.к. x не был инкрементирован
```

### Совместное использование операторов опциональной последовательности

Во вложенных объектах возможно использование оператора опциональной последовательности неограниченное количество раз:

```js
let customer = {
  name: "Carl",
  details: {
    age: 82,
    location: "Paradise Falls", // точный адрес неизвестен
  },
};
let customerCity = customer.details?.address?.city;

// … это также работает с вызовами функций
let duration = vacations.trip?.getTime?.();
```

### Использование с оператором ??

Оператор {{JSxRef("Operators/Nullish_Coalescing_Operator", "??", '', 1)}} может использоваться после опциональной последовательности для установления значения по умолчанию:

```js
let customer = {
  name: "Carl",
  details: { age: 82 },
};
const customerCity = customer?.city ?? "Unknown city";
console.log(customerCity); // Unknown city
```

## Спецификации

{{Specifications}}

## Совместимость с браузерами

{{Compat}}

## Смотрите также

- [Github - Proposal-pipeline-operator](https://github.com/tc39/proposal-pipeline-operator)
- [TC39 proposals](https://github.com/tc39/proposals)
- {{JSxRef("Operators/Nullish_Coalescing_Operator", "Nullish Coalescing Operator", '', 1)}}
