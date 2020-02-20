---
layout: post
title:  " Курс «JS: Автоматическое тестирование», заметки"
date:   2020-01-29 12:55:20 +0700
categories: programming
image: 1.jpg 
---


У обычных утверждений есть мощная альтернатива: [power-assert](https://github.com/power-assert-js/power-assert). Эта библиотека добавляет магию в привычный инструмент.

**Jest - фраймворк для запуска тестов**

(Документация jest)[https://jestjs.io/docs/en/getting-started]

Матчеры Jest: [Документация](https://jestjs.io/docs/ru/expect) 

**функция test не запускает тест на выполнение. Она добавляет его во внутрь Jest, а вот он уже решает, когда выполнить этот тест. Поэтому между загрузкой модуля и отработкой тестов проходит неопределённое время.**


```
import assert from 'power-assert';

// Весь код остаётся тем же самым
const user = {
  name: 'Madonna',
  friends: ['Kate', 'Michel'],
  email: 'madonna@example.com',
};

// Интерфейс библиотеки power-assert на 100% совместим со встроенным модулем assert.
assert(user.name === 'Michel');
И посмотреть вывод:

AssertionError [ERR_ASSERTION]:   # test.js:10

  assert(user.name === 'Michel')
         |    |    |
         |    |    false
         |    "Madonna"
         Object{name:"Madonna",friends:#Array#,email:"madonna@example.com"}

  --- [string] 'Michel'
  +++ [string] user.name
  @@ -1,6 +1,7 @@
   M
  -ichel
  +adonna
```

**--watch**
Watch files for changes and rerun tests related to changed files. If you want to re-run all tests when a file has changed, use the --watchAll option instead.

**Хуки**

 Тестовые фреймворки предоставляют хуки — специальные функции, которые запускаются до или после тестов. Ниже пример того, как создавать дату перед каждым тестом:

(Документация по хукам)[https://jestjs.io/docs/en/api#testeachtable-name-fn-timeout]

```
let now;
let counter = 0;

// Запускается перед каждым тестом
beforeEach(() => {
  now = new Date();
  counter += 1;
});

test('first example', () => console.log(now, counter));
test('second example', () => console.log(now, counter));
```

**Плохие и хорошие практики тестирования**

1. Нет условным конструкциям внутри тестов. Любое ветвление внутри тестов это фактически несколько тестов в рамках одного теста. От этого надо избавляться и никогда так не писать.
2. Исключать взаимное влияние тестов

Так как же лучше написать тест? Нужно найти достаточно высокую точку входа в нашу программу, которая не зависит от внутренней реализации, и при этом выполняет поставленную задачу.
  
**Покрытие кода тестами**

В Jest покрытие меряется крайне просто. Достаточно запустить тесты с флагом --coverage:
```
nodejs-package$ npx jest --coverage
```

Большинство разработчиков сходится во мнении, что 80% — это достаточно хорошее покрытие. На этом можно и остановиться.

  **Примеры тестов**
```
  test('Cart', () => {
  const cart = makeCart();
  expect(cart.getItems()).toHaveLength(0);

  cart.addItem({ name: 'car', price: 3 }, 5);
  expect(cart.getItems()).toHaveLength(1);
  expect(cart.getCost()).toBe(15);
  expect(cart.getCount()).toBe(5);

  cart.addItem({ name: 'house', price: 10 }, 2);
  expect(cart.getItems()).toHaveLength(2);
  expect(cart.getCost()).toBe(35);
  expect(cart.getCount()).toBe(7);
});
```







  **Прочее:**

  [Приложение двенадцати факторов](https://habr.com/ru/post/258739/)

  [Чек-лист хороших инженерных практик в компаниях](https://guides.hexlet.io/check-list-of-engineering-practices/)

  [Начинаем писать тесты правильно (видео)](https://www.youtube.com/watch?v=zsz8kdi62mE) 

  [Начинаем писать тесты правильно (статья)](https://ru.hexlet.io/blog/posts/how-to-test-code)

  [Среды разработки. Мужики, выкатывай!](https://ru.hexlet.io/blog/posts/environment)

  [Создание базового проекта NodeJS с Babel 7, eslint, jest.](https://medium.com/@sergey.bakaev/%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D0%B1%D0%B0%D0%B7%D0%BE%D0%B2%D0%BE%D0%B3%D0%BE-%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B0-nodejs-%D1%81-babel-7-eslint-jest-8164b99777a4)