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

# Инверсия зависимостей

(Инверсия зависимостей)[https://zen.yandex.ru/media/id/5b1d8642c71a92a91b9ea0ce/vnedrenie-zavisimostei-chto-eto-takoe-i-kak-rabotaet-5d5684d1a06eaf00ac3ad8cd]

Программу можно изменить так, чтобы она вызывала функцию sendEmail не напрямую, а принимала её как параметр:

```
import sendEmail from './emailSender.js';

// Ставим значение по умолчанию, чтобы не пришлось постоянно указывать функцию
const registerUser = (params, send = sendEmail) => {
  const user = new User(params);
  if (user.save()) {
    send('registration', { user });
    return true;
  }
  return false;
}
```

Тест:
```
const fakeSendEmail = (...args) => {
  /* Например письмо можно вывести в лог для удобства отладки */
};

test('registerUser', () => {
  const id = registerUser({ name: 'Mike' }, fakeSendEmail);
  const user = User.find(id)
  expect(user).toHaveProperty('name', 'Mike');
});
```