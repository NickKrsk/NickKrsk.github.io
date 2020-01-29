---
layout: post
title:  " Курс «JS: Автоматическое тестирование», заметки"
date:   2020-01-29 12:55:20 +0700
categories: programming
image: 1.jpg 
---

У обычных утверждений есть мощная альтернатива: [power-assert](https://github.com/power-assert-js/power-assert). Эта библиотека добавляет магию в привычный инструмент.

**Jest - фраймворк для запуска тестов**
Матчеры Jest: [Документация](https://jestjs.io/docs/ru/expect) 

**функция test не запускает тест на выполнение. Она добавляет его во внутрь Jest, а вот он уже решает, когда выполнить этот тест. Поэтому между загрузкой модуля и отработкой тестов проходит неопределённое время.**

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



  Прочее:
  [Приложение двенадцати факторов](https://habr.com/ru/post/258739/)

  [Чек-лист хороших инженерных практик в компаниях](https://guides.hexlet.io/check-list-of-engineering-practices/)