### Debouncing and Throttling explained through examples

Debounce and throttle are two similar (but different!) techniques to control how many times we allow a function to be executed over time.

Having a debounced or throttled version of our function is especially useful when we are attaching the function to a DOM event. Why? Because we are giving ourselves a layer of control between the event and the execution of the function. Remember, we don’t control how often those DOM events are going to be emitted. It can vary.
两者在我们将一个函数attach到DOM事件的时候非常有效，因为我们给事件和方法执行间一个控制层

When scrolling using a trackpad, scroll wheel, or just by dragging a scrollbar can trigger easily 30 events per second. But scrolling slowly (swapping) in a smartphone could trigger as much as 100 events per second during my tests. Is your scroll handler prepared for this rate of execution?

In 2011, an issue popped up on the Twitter website: when you were scrolling down your Twitter feed, it became slow and unresponsive. John Resig published a blog post about the problem where it was explained how bad of an idea it is to directly attach expensive functions to the scroll event.
推特之前遇到过这个问题。将操作昂贵的方法attach到下拉事件是一个不好的想法

The suggested solution by John (at that time, five years ago) was a loop running every 250ms, outside of the onScroll event. That way the handler is not coupled to the event. With this simple technique, we can avoid ruining the user experience.
当时提出的方案是一个每250ms的在onScroll事件外的循环，从而使得handler和事件并不是绑定在一起。

These days there are slightly more sophisticated ways of handling events. Let me introduce you to Debounce, Throttle, and requestAnimationFrame. We’ll also look at the matching use cases.
下面介绍debounce，throttle和requestAnimationFrame

#### debounce
The Debounce technique allow us to “group” multiple sequential calls in a single one.
debounce技术允许我们将几个序列的执行组合为一个
![debounce](https://i0.wp.com/css-tricks.com/wp-content/uploads/2016/04/debounce.png)

Imagine you are in an elevator. The doors begin to close, and suddenly another person tries to get on. The elevator doesn’t begin its function to change floors, the doors open again. Now it happens again with another person. The elevator is delaying its function (moving floors), but optimizing its resources.
在电梯关闭前又有一个乘客进入，这时候电梯没有开始更新楼层而是打开电梯门让乘客进入。电梯延迟执行方法但是优化了资源

You can see how sequential fast events are represented by a single debounced event. But if the events are triggered with big gaps, the debouncing doesn’t happen.
有序快事件会被一个debouned事件但是如果有一个大的gap则debouncing就不会发生

##### Leading edge (or “immediate”)

You may find it irritating that the debouncing event waits before triggering the function execution, until the events stop happening so rapidly. Why not trigger the function execution immediately, so it behaves exactly as the original non-debounced handler? But not fire again until there is a pause in the rapid calls.
有可能会觉得debouncing的事件总是在结束后才触发函数执行。可以让函数立即执行但是在暂停时不执行

##### Debounce Implementation
lodash or undersource source

##### How to use debounce and throttle and common pitfalls
It can be tempting to build your own debounce/throttle function, or copy it from some random blog post. My recommendation is to use underscore or Lodash directly. If you only need the _.debounce and _.throttle functions, you can use Lodash custom builder to output a custom 2KB minified library. Build it with this simple command:

A common pitfall is to call the _.debounce function more than once:
```javascript
// WRONG
$(window).on('scroll', function() {
   _.debounce(doSomething, 300); 
});

// RIGHT
$(window).on('scroll', _.debounce(doSomething, 200));
```
Creating a variable for the debounced function will allow us to call the private method debounced_version.cancel(), available in lodash and underscore.js, in case you need it.
创建一个debounced函数参数允许我们使用cancel方法
```javascript
var debounced_version = _.debounce(doSomething, 200);
$(window).on('scroll', debounced_version);

// If you need it
debounced_version.cancel();
```

#### Throttle
By using _.throttle, we don’t allow to our function to execute more than once every X milliseconds.
使用throttle，可以**不允许**在X milliseconds内执行多于一次

The main difference between this and debouncing is that throttle guarantees the execution of the function regularly, at least every X milliseconds.
和debounce之间最大的区别在于throttle保证执行函数regularly，至少在每X milliseconds

The same way than debounce, throttle technique is covered by Ben’s plugin, underscore.js and lodash.

#### Throttling Examples
- Infinite scrolling
A quite common example. The user is scrolling down your infinite-scrolling page. You need to check how far from the bottom the user is. If the user is near the bottom, we should request via Ajax more content and append it to the page.

Here our beloved _.debounce wouldn’t be helpful. It only would trigger only when the user stops scrolling.. and we need to start fetching the content before the user reaches the bottom.

With _.throttle we can warranty that we are checking constantly how far we are from the bottom.
无限滚动使用throttle，在用户拉到底部之前开始获取内容

#### requestAnimationFrame (rAF)
requestAnimationFrame is another way of rate-limiting the execution of a function.
rAF是限制函数执行速率的方法

It can be thought as a _.throttle(dosomething, 16). But with a much higher fidelity, since it’s a browser native API that aims for better accuracy.

We can use the rAF API, as an alternative to the throttle function, considering this pros/cons:
- pros
  - Aims for 60fps (frames of 16 ms) but internally will decide the best timing on how to schedule the rendering. 致力于60fps刷新，决定如何调度渲染
  - Fairly simple and standard API, not changing in the future. Less maintenance. 标准API，没有未来的维护成本
- cons
  - The start/cancelation of rAFs it’s our responsibility, unlike .debounce or .throttle, where it’s managed internally. raf开始和结束需要手动控制
  - If the browser tab is not active, it would not execute. Although for scroll, mouse or keyboard events this doesn’t matter. tab不活跃则不执行。滚动，鼠标或者键盘事件不执行
  - Although all modern browsers offer rAF, still is not supported in IE9, Opera Mini and old Android. A polyfill would be needed still today. IE9等浏览器不支持
  - rAF is not supported in node.js, so you can’t use it on the server to throttle filesystem events. 服务端不可用
As a rule of thumb, I would use requestAnimationFrame if your JavaScript function is “painting” or animating directly properties, use it at everything that involves re-calculating element positions.

To make Ajax requests, or deciding if adding/removing a class (that could trigger a CSS animation), I would consider _.debounce or _.throttle, where you can set up lower executing rates (200ms for example, instead of 16ms)

If you think that rAF could be implemented inside underscore or lodash, they both have rejected the idea, since it’s a specialized use case, and it’s easy enough to be called directly.

##### Examples of rAF
I will cover only this example to use requestAnimation frame on scroll, inspired by Paul Lewis article, where he explains step-by-step the logic of this example.

I put it side by side to compare it to _.throttle at 16ms. Giving similar performance, but probably rAF will give you better results on more complex scenarios.

A more advanced example where I’ve seen this technique is in the library headroom.js, where the logic is decoupled and wrapped inside an object.

#### Conclusion
Use debounce, throttle and requestAnimationFrame to optimize your event handlers. Each technique is slightly different, but all three of them are useful and complement each other.

In summary:
- debounce: Grouping a sudden burst of events (like keystrokes) into a single one.
- throttle: Guaranteeing a constant flow of executions every X milliseconds. Like checking every 200ms your scroll position to trigger a CSS animation.
- requestAnimationFrame: a throttle alternative. When your function recalculates and renders elements on screen and you want to guarantee smooth changes or animations. Note: no IE9 support.

#### sources
[source](https://css-tricks.com/debouncing-throttling-explained-examples/)
