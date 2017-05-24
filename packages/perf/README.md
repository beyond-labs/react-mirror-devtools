Mirror Perf Devtools
====================

Mirror can say how long the main JavaScript thread was blocked for by dispatching an action (could only be blocked by Mirror itself). And also how long it was blocked for by events scheduled on the next tick as a consequence of that action (likely includes React rendering, mixed with other work). Mirror would be unable to say which work on subsequent ticks could be attributed to the original action (triggered by, for example passing updated props to a nested child) though with visualisations a human might be able to do this. Mirror would also be unable to further break down time spent - to know what it was specifically spent on.

However, Mirror would be able to provide a very detailed account of which *events* took place (with timestamps). For example, which components were mounted, rendered, received props, dispatched actions, etc. In particular, Mirror could break down the behaviour of `pure` to a very fine granularity, so that, for example, Mirror could say "For 45 out of 87 state updates triggered within TodoItems as a direct consequence of 'MARK_ALL_TODOS_AS_DONE' `mapStateToProps` was called because state.done changed from `false` to `true`". This could make detecting accidental re-renders due to dirty props much easier.

To summarise, Mirror is unable to say "This much time was spent here, here, and here.", but it can say "The JavaScript thread was blocked here, here and here. These are the events you should investigate."

To make any of this possible Mirror would need to implement hooks (at both the component & global levels) that the devtools can take advantage of. This is relatively easy to do.

```js
// May vary between browsers, good in latest Chrome & Edge browsers
const isNextTickProfilingAccurate = async () => {
  var arr = []

  await Promise.all(new Array(10).fill().map((v, i) => new Promise(resolve => setTimeout(resolve)).then(() => arr.push(i))))

  return JSON.stringify(arr) === JSON.stringify(arr.slice().sort())
}
```
