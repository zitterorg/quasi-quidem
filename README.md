# jest-mock-inference-helper

[![Coverage Status](https://coveralls.io/repos/github/zitterorg/quasi-quidem/badge.svg?branch=main)](https://coveralls.io/github/zitterorg/quasi-quidem?branch=main)
[![GitHub](https://img.shields.io/github/license/zitterorg/quasi-quidem)](LICENSE)
[![npm](https://img.shields.io/npm/v/@zitterorg/quasi-quidem)](https://www.npmjs.com/package/@zitterorg/quasi-quidem)
[![Vulnerabilities](https://snyk.io/test/github/zitterorg/quasi-quidem/badge.svg)](https://snyk.io/test/github/zitterorg/quasi-quidem)
[![npm bundle size (scoped)](https://img.shields.io/bundlephobia/min/@zitterorg/quasi-quidem)](https://bundlephobia.com/result?p=@zitterorg/quasi-quidem)
[![GitHub last commit](https://img.shields.io/github/last-commit/zitterorg/quasi-quidem)](https://github.com/zitterorg/quasi-quidem/commits/main)

This targets typescrit projects and aims to simplify declaration of mocked classes and functions

## install

```bash
npm i -D @zitterorg/quasi-quidem
```

## usage

### asMock

![AsMockDemo](./docs/images/asMock.png)

Assuming SUT file

```typescript
import { bar, baz } from "./bar";

export function foo() {
  return bar() + " - " + baz();
}
```

in your spec file

```typescript
import { asMock } from "@zitterorg/quasi-quidem";
import { foo } from "./foo";
import { bar, baz } from "./bar";

// Automock bar
jest.mock("bar");

const barMock = asMock(bar).mockReturnValue("bar");
const bazMock = asMock(baz).mockReturnValue("baz");

// same as
const barMock = (bar as jest.MockedFunction<typeof bar>).mockReturnValue("bar");
const bazMock = (baz as jest.MockedFunction<typeof baz>).mockReturnValue("baz");

describe("foo", () => {
  it("Should success", () => {
    const res = foo();

    await expect(barMock).toHaveBeenCalled();
  });
});
```

### asMocks

works similar to `asMock` but provides inference sugar for multiple
functions in a single call

![AsMocksDemo](./docs/images/asMocks.png)

Assuming SUT file

```typescript
import { bar, baz } from "./bar";

export function foo() {
  return bar() + " - " + baz();
}
```

in your spec file

```typescript
import { asMock } from "@zitterorg/quasi-quidem";
import { foo } from "./foo";
import { bar, baz } from "./bar";

// Automock bar
jest.mock("bar");

const { barMock, bazMock } = asMocks({ bar, baz });

// same as
const barMock = bar as jest.MockedFunction<typeof bar>;
const bazMock = baz as jest.MockedFunction<typeof baz>;

describe("foo", () => {
  beforeEach(() => {
    barMock.mockReturnValue("bar");
    barzock.mockReturnValue("baz");
  });

  it("Should success", () => {
    const res = foo();

    await expect(barMock).toHaveBeenCalled();
  });
});
```

### asClassMock

Provides functionality to infer class mock type and also
shortcuts to implement inner object fields and/or properties

see [test file](./src/index.spec.ts#L81) for extended usage

![AsMocksDemo](./docs/images/asClassMockOptions.png)

Assuming SUT file

```typescript
import { BarClass } from "./bar";

export function foo() {
  const bar = new BarClass();

  return bar.func1() + " - " + bar.func2();
}
```

in your spec file

```typescript
import { asClassMock } from "@zitterorg/quasi-quidem";
import { foo } from "./foo";
import { BarClass } from "./bar";

// Automock bar
jest.mock("bar");

const BarClassMock = asClassMock(BarClass);

// similar to
const BarClassMock = bar as jest.MockedClass<BarClassMock>;

describe("foo", () => {
  beforeEach(() => {
    BarClassMock.func1.mockReturnValue("bar");
  });

  it("Should success", () => {
    const res = foo();

    await expect(BarClassMock.func1).toHaveBeenCalled();
  });
});
```

## Changelog

[Changelog](CHANGELOG.md).

## License

[MIT Lizenz](https://choosealicense.com/licenses/mit/)
