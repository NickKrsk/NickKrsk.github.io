---
layout: post
title:  " JS: Продвинутое тестирование, заметки"
date:   2020-02-20 12:55:20 +0700
categories: programming
image: 
---

Метчер jest для отлова исключений:

```
test('boom!', () => {
  expect(() => {
    functionWithException(0);
  }).toThrow();
})
```

Главная особенность этого матчера в том, что он принимает на вход функцию, которая вызывается внутри. Благодаря этому, он может самостоятельно отследить появление исключения. Этот код не содержит неявного состояния и лишних проверок, он делает ровно то что нужно делать и не требует от нас слишком много.

Иногда важно не просто поймать исключение, но и убедиться в том, что это ожидаемое исключение. Сделать это можно передав в матчер toThrow() строку, которая должна присутствовать в сообщении исключения.

```
test('boom!', () => {
  expect(() => {
    functionWithException(0);
  }).toThrow('divide by zero');
})
```

Фикстуры:

```
const { promises: fs } = require('fs');
const path = require('path');
const getFunction = require('../functions');

const toHtmlList = getFunction();

// BEGIN (write your solution here)
const formats = ['csv', 'json', 'yml'];
const getFixturePath = (name) => path.join(__dirname, '..', '__fixtures__', name);

let expected;

beforeAll(async () => {
  expected = await fs.readFile(getFixturePath('result.html'), 'utf-8');
});

test.each(formats)('%s', async (format) => {
  const filePath = getFixturePath(`list.${format}`);
  const actual = await toHtmlList(filePath);
  expect(actual).toEqual(expected.trim());
});
// END
```

# Побочные эффекты

Библиотека для генерации данных: [Faker](https://github.com/marak/Faker.js/)

Любое взаимодействие с внешней средой не детерминировано.

Библиотека для создания виртуальной ФС: [mock-fs](https://github.com/tschaub/mock-fs)