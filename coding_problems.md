# Coding problems

## 1. Implement curry()

### Description

Currying is a useful technique used in JavaScript applications.

Please implement a curry() function, which accepts a function and return a curried one.

Here is an example

```js
const join = (a, b, c) => {
   return `${a}_${b}_${c}`
}

const curriedJoin = curry(join)

curriedJoin(1, 2, 3) // '1_2_3'

curriedJoin(1)(2, 3) // '1_2_3'

curriedJoin(1, 2)(3) // '1_2_3'
```

more to read: 
https://javascript.info/currying-partials
https://lodash.com/docs/4.17.15#curry

### Solution :thumbsup:

```js 
// This is a JavaScript coding problem from BFE.dev 

/**
 * @param { (...args: any[]) => any } fn
 * @returns { (...args: any[]) => any }
 */
function curry(fn) {
  function curried(...args) {
    if (args.length >= fn.length) {
      return fn(...args);
    } else {
      return curried.bind(null, ...args);
    }
  }

  return curried;
}
```

## 2. Implement curry() with placeholder support.

### Description

This is a follow-up on 1. implement curry()

please implement curry() which also supports placeholder.

Here is an example

```js
const  join = (a, b, c) => {
   return `${a}_${b}_${c}`
}

const curriedJoin = curry(join)
const _ = curry.placeholder

curriedJoin(1, 2, 3) // '1_2_3'

curriedJoin(_, 2)(1, 3) // '1_2_3'

curriedJoin(_, _, _)(1)(_, 3)(2) // '1_2_3'
```

more to read

https://javascript.info/currying-partials

https://lodash.com/docs/4.17.15#curry

https://github.com/planttheidea/curriable

### Solution :thumbsup:

```js
// This is a JavaScript coding problem from BFE.dev 

/**
 * @param { (...args: any[]) => any } fn
 * @returns { (...args: any[]) => any }
 */
function curry(fn) {
  function curried(...args) {
    if (args.length >= fn.length && !args.includes(curry.placeholder)) {
      return fn(...args);
    } else {
      return (...newArgs) => {
        const replacedArgs = args.map(arg => arg === curry.placeholder ? newArgs.shift() : arg);
        return curried(...replacedArgs, ...newArgs);
      }
    }
  }

  return curried;
}


curry.placeholder = Symbol()
```

## 3.Implement `Array.prototype.flat()`.

### Description

There is already Array.prototype.flat() in JavaScript (ES2019), which reduces the nesting of Array.

Could you manage to implement your own one?

Here is an example to illustrate

```js
const arr = [1, [2], [3, [4]]];

flat(arr)
// [1, 2, 3, [4]]

flat(arr, 1)
// [1, 2, 3, [4]]

flat(arr, 2)
// [1, 2, 3, 4]
```
Are you able to solve it both recursively and iteratively?

### Solution :thumbsdown: (got the recursion wrong)

```JavaScript
// This is a JavaScript coding problem from BFE.dev 
/**
 * @param { Array } arr
 * @param { number } depth
 * @returns { Array }
 */
function flat(arr, depth = 1) {
  if (depth <= 0 ) return arr;
  return arr.reduce((acc, cur) => [...acc, ...(Array.isArray(cur) ? flat(cur, depth - 1) : [cur])], [])
}
```

## 6. Implement basic debounce

### Description

Debounce is a common technique used in Web Application, in most cases using lodash solution would be a good choice.

could you implement your own version of basic debounce()?

In case you forgot, debounce(func, delay) will returned a debounced function, which delays the invoke.

Here is an example.

Before debouncing we have a series of calling like

─A─B─C─ ─D─ ─ ─ ─ ─ ─E─ ─F─G

After debouncing at wait time of 3 dashes

─ ─ ─ ─ ─ ─ ─ ─ D ─ ─ ─ ─ ─ ─ ─ ─ ─ G

notes

1. please follow above spec. the behavior might not be exactly the same as lodash.debounce()

2. because window.setTimeout and window.clearTimeout are not accurate in browser environment, they are replaced to other implementation when judging your code. They still have the same interface, and internally keep track of the timing for testing purpose.

3. Something like below will be used to do the test.

```js
let currentTime = 0

const run = (input) => {
  currentTime = 0
  const calls = []

  const func = (arg) => {
     calls.push(`${arg}@${currentTime}`)
  }

  const debounced = debounce(func, 3)
  input.forEach((call) => {
     const [arg, time] = call.split('@')
     setTimeout(() => debounced(arg), time)
  })
  return calls
}

expect(run(['A@0', 'B@2', 'C@3'])).toEqual(['C@5'])
```

### Solution :thumbsup:

```JavaScript
// This is a JavaScript coding problem from BFE.dev 

/**
 * @param {(...args: any[]) => any} func
 * @param {number} wait
 * @returns {(...args: any[]) => any}
 */
function debounce(func, wait) {
  let id = null;
  return (...args) => {
    if (id) clearTimeout(id);
    id = setTimeout(() => func(...args), wait);
  }
}
```

## 8. Implement shuffle

### Description

How would you implement a shuffle() ?

When passed with an array, it should modify the array inline to generate a randomly picked permutation at the same probability.

for an array like this:

```js
const arr = [1, 2, 3, 4]
// there would be possibly 4! = 24 permutations

[1, 2, 3, 4]
[1, 2, 4, 3]
[1, 3, 2, 4]
[1, 3, 4, 2]
[1, 4, 2, 3]
[1, 4, 3, 2]
[2, 1, 3, 4]
[2, 1, 4, 3]
[2, 3, 1, 4]
[2, 3, 4, 1]
[2, 4, 1, 3]
[2, 4, 3, 1]
[3, 1, 2, 4]
[3, 1, 4, 2]
[3, 2, 1, 4]
[3, 2, 4, 1]
[3, 4, 1, 2]
[3, 4, 2, 1]
[4, 1, 2, 3]
[4, 1, 3, 2]
[4, 2, 1, 3]
[4, 2, 3, 1]
[4, 3, 1, 2]
[4, 3, 2, 1]
```

your shuffle() should transform the array in one of the above array, at the same 1/24 probability.

notes

1. Your shuffle() will be called multiple times, to calculate the probability on each possible result, and test again standard deviation

### Solution

```JavaScript
// This is a JavaScript coding problem from BFE.dev 

/**
 * @param {any[]} arr
 * @returns {void}
 */
function shuffle(arr) {
  for (let i = arr.length - 1; i > 0; i--) {
    const to = Math.floor(Math.random() * (i + 1));
    [arr[i], arr[to]] = [arr[to], arr[i]];
  }
  return arr;
}
```

## 9. Decode message.

### Description

Your are given a 2-D array of characters. There is a hidden message in it.

```
I B C A L K A
D R F C A E A
G H O E L A D 
```

The way to collect the message is as follows

1. start at top left
2. move diagonally down right
3. when cannot move any more, try to switch to diagonally up right
4. when cannot move any more, try switch to diagonally down right, repeat 3
5. stop when cannot neither move down right or up right. the character on the path is the message
6. for the input above, IROCLED should be returned.

notes: if no characters could be collected, return empty string

### Solution :thumbsup:

```JavaScript
// This is a JavaScript coding problem from BFE.dev 

/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
  if (message.length === 0 || message[0].length === 0) return '';

  let res = '';
  let direction = 1;
  let row = col = 0;

  while (col <= message[0].length - 1) {
    res += message[row][col];
    if (row === message.length - 1) direction = -1;
    if (row === 0) direction = 1;
    col++;
    row += direction;
  }

  return res;
}
```

### Better Solution

You can just multiply by `-1` if there is no next letter

```JavaScript
/**
 * @param {string[][]} message
 * @return {string}
 */
function decode(message) {
  let i = 0, j = 0, cols = message[0]?.length
  let decoded = '', step = 1

  while(j < cols) {
    decoded += message[i][j]
    if(!message[i+step]){
      step *= -1
    }
    i += step
    j++
  }

  return decoded
}
```

