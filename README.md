# ECMAScript Async Function resolve/reject proposal

Facilitate async function resolution from events without new Promise() 

**Stage**: 0?

**Author**: Alexandre Morgaut

**Champion**: To be found

## Problem

While async/await notation simplifies Promise API usage, non promise API integration is more complex than it could. Using `new Promise((resolve, reject) => { /* callback code */ })` should be worthless within the context of an async function.

### Example

```js
async function foo() {
  let result;
  let resolveEvent;
  const eventDone = new Promise((resolve) => {
    resolveEvent = resolve;
    elem.addEventListener('click', resolve);
  });
  try {
    result = await Promise.race([
      eventDone,
      new Promise((resolve, resject) => setTimeout(reject, MAX_DELAY, new Error('Expired'))
    ]);
  } catch (e) {
    console.error(e);
  } finally {
    elem.removeEventListener('click', resolveEvent);
  }
  return result;
}
```

## Proposal

Provide a `async#resolve` / `async#reject` syntax


```js
async function foo() {
  let result;
  let resolveEvent;
  const eventDone = async () => {
    resolveEvent = async#resolve;
    elem.addEventListener('click', resolveEvent);
  });
  try {
    result = await Promise.race([
      eventDone,
      async () => setTimeout(async#reject, MAX_DELAY, new Error('Expired'))
    ]);
  } catch (e) {
    console.error(e);
  } finally {
    elem.removeEventListener('click', resolveEvent);
  }
}
```
