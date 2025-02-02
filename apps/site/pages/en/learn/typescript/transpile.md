---
title: Running TypeScript code using transpilation
layout: learn
authors: AugustinMauroy
# not used by website but keep it for now
node-v: '>=22.0.0'
---

# Running TypeScript code using transpilation

Transpilation is the process of converting source code from one language to another. In the case of TypeScript, it's the process of converting TypeScript code to JavaScript code.

## Compiling TypeScript to JavaScript

The most common way to run TypeScript code is to compile it to JavaScript first. You can do this using the TypeScript compiler `tsc`.

**Step 1:** Write your TypeScript code in a file, for example `example.ts`.

<!--
  Maintainers note: this code is duplicated in the previous article, please keep them in sync
-->

```ts
type User = {
  name: string;
  age: number;
};

function isAdult(user: User): boolean {
  return user.age >= 18;
}

const justine = {
  name: 'Justine',
  age: 23,
} satisfies User;

const isJustineAnAdult = isAdult(justine);
```

**Step 2:** Install TypeScript locally using a package manager:

In this example we're going to use npm, you can check our [our introduction to the npm package manager](/learn/getting-started/an-introduction-to-the-npm-package-manager) for more information.

```bash displayName="Install TypeScript locally"
npm i -D typescript # -D is a shorthand for --save-dev
```

**Step 3:** Compile your TypeScript code to JavaScript using the `tsc` command:

```bash
npx tsc example.ts
```

> **NOTE:** `npx` is a tool that allows you to run Node.js packages without installing them globally.

`tsc` is the TypeScript compiler which will take our TypeScript code and compile it to JavaScript.
This command will result in a new file named `example.js` that we can run using Node.js.
Now when we know how to compile and run TypeScript code let's see TypeScript bug-preventing capabilities in action!

**Step 4:** Run your JavaScript code using Node.js:

```bash
node example.js
```

You should see the output of your TypeScript code in the terminal

## If there are type errors

If you have type errors in your TypeScript code, the TypeScript compiler will catch them and prevent you from running the code. For example, if you change the `age` property of `justine` to a string, TypeScript will throw an error:

We will modify our code like this, to voluntarily introduce a type error:

```ts
type User = {
  name: string;
  age: number;
};

function isAdult(user: User): boolean {
  return user.age >= 18;
}

const justine: User = {
  name: 'Justine',
  age: 'Secret!',
};

const isJustineAnAdult: string = isAdult(justine, "I shouldn't be here!");
```

And this is what TypeScript has to say about this:

```console
example.ts:12:5 - error TS2322: Type 'string' is not assignable to type 'number'.

12     age: 'Secret!',
       ~~~

  example.ts:3:5
    3     age: number;
          ~~~
    The expected type comes from property 'age' which is declared here on type 'User'

example.ts:15:7 - error TS2322: Type 'boolean' is not assignable to type 'string'.

15 const isJustineAnAdult: string = isAdult(justine, "I shouldn't be here!");
         ~~~~~~~~~~~~~~~~

example.ts:15:51 - error TS2554: Expected 1 arguments, but got 2.

15 const isJustineAnAdult: string = isAdult(justine, "I shouldn't be here!");
                                                     ~~~~~~~~~~~~~~~~~~~~~~


Found 3 errors in the same file, starting at: example.ts:12
```

As you can see, TypeScript is very helpful in catching bugs before they even happen. This is one of the reasons why TypeScript is so popular among developers.

## Understanding tsconfig.json and Transpilation Options

TypeScript's behavior and compilation settings can be customized using a `tsconfig.json` file. This configuration file is essential for larger projects and provides fine-grained control over how TypeScript code is transpiled to JavaScript.

**Step 1:** Create a `tsconfig.json` file in your project root:

```bash
npx tsc --init
```

This command creates a `tsconfig.json` with default settings and helpful comments.

**Step 2:** Configure the transpilation target

One of the most important options in `tsconfig.json` is the `target` setting, which specifies which version of JavaScript your TypeScript code will be compiled to:

```json
{
  "compilerOptions": {
    "target": "es2022", // Specify ECMAScript target version
    "module": "commonjs", // Specify module code generation
    "strict": true // Enable all strict type-checking options
  }
}
```

The choice of target affects:

1. **Feature Compatibility**: Lower targets ensure broader browser/runtime compatibility
2. **Code Size**: Lower targets may generate more code to polyfill newer features
3. **Performance**: Modern targets can use newer, more efficient JavaScript features

Example of how different targets affect output:

```ts
// TypeScript input
class Example {
  #privateField = 42;

  getField() {
    return this.#privateField;
  }
}
```

When compiled with `"target": "es2022"`:

```js
'use strict';
class Example {
  #privateField = 42;
  getField() {
    return this.#privateField;
  }
}
```

When compiled with `"target": "es2015"`:

```js
// ... some polyfill code for private fields
var _Example_privateField;
class Example {
  constructor() {
    _Example_privateField.set(this, 42);
  }
  getField() {
    return __classPrivateFieldGet(this, _Example_privateField, 'f');
  }
}
_Example_privateField = new WeakMap();
```

**Other Important tsconfig Options:**

- `"module"`: Specifies the module system (commonjs, es2015, esnext, etc.)
- `"strict"`: Enables strict type checking
- `"outDir"`: Specifies output directory for compiled files
- `"sourceMap"`: Generates source maps for debugging

After setting up your `tsconfig.json`, you can compile your project without specifying individual files:

```bash
npx tsc
```

This will compile all TypeScript files according to your tsconfig settings.

### Why it's can be useful to chose the right target

Choosing the right target is important for ensuring your code works across different environments. For example, if you're building a library that will be used in a wide range of environments, you might want to target an older version of JavaScript to ensure compatibility.
