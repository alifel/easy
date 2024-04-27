# WeakMap

A `WeakMap` is a collection of key/value pairs whose keys must be objects or [non-registered symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#shared_symbols_in_the_global_symbol_registry), with values of any arbitrary [JavaScript type](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures), and which does not create strong references to its keys. That is, an object's presence as a key in a `WeakMap` does not prevent the object from being garbage collected. Once an object used as a key has been collected, its corresponding values in any WeakMap become candidates for garbage collection as well — as long as they aren't strongly referred to elsewhere. The only primitive type that can be used as a `WeakMap` key is symbol — more specifically, [non-registered symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#shared_symbols_in_the_global_symbol_registry) — because non-registered symbols are guaranteed to be unique and cannot be re-created.

`WeakMap`是一个健值对的集合，它的key必须是一个对象或者[non-registered symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#shared_symbols_in_the_global_symbol_registry)，它的value可以是任意的[JavaScript type](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Data_structures)，不能创建强引用到它的key。也就是说，一个对象作为`WeakMap`的key，它不能阻止垃圾回收器回收这个对象。一旦作为key的对象被回收，它相关的value对应的值也变为可回收状态，只要他们没有在其他地方被强引用。1个主要的可以作为`WeakMao`的key的primitive类型是symbol （特指, [non-registered symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#shared_symbols_in_the_global_symbol_registry)），因为non-registered symbols保证了唯一性和不能被再次创建。

`WeakMap` allows associating data to objects in a way that doesn't prevent the key objects from being collected, even if the values reference the keys. However, a `WeakMap` doesn't allow observing the liveness of its keys, which is why it doesn't allow enumeration; if a `WeakMap` exposed any method to obtain a list of its keys, the list would depend on the state of garbage collection, introducing non-determinism. If you want to have a list of keys, you should use a `Map` rather than a `WeakMap`.

`WeakMap`允许关联数据到一个对象上，并且不会阻止这个对象被回收，即使value引用了key(:pill:***从这里可以知道，即使value引用了自己的key，但是如果key没有其他的强引用，仍然会被回收***)。无论如何，`WeakMap`不允许观察他们key的活跃性，这就是为什么不允许枚举的原因。如果一个`WeakMap`导出任意方法去获取key的列表，列表将会依赖于垃圾回收器的状态，引入不确定性。如果你想有一个key的列表，你可以使用`Map`而不是`WeakMap`。

You can learn more about `WeakMap` in the [WeakMap object section](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections#weakmap_object) of the [Keyed collections](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections) guide.

你可以在 [Keyed collections](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections) guide的[WeakMap object section](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Keyed_collections#weakmap_object)中学习更多关于`WeakMap`的东西.

## Describe

Keys of `WeakMaps` must be garbage-collectable. Most [primitive data types](https://developer.mozilla.org/en-US/docs/Glossary/Primitive) can be arbitrarily created and don't have a lifetime, so they cannot be used as keys. Objects and [non-registered symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#shared_symbols_in_the_global_symbol_registry) can be used as keys because they are garbage-collectable.

`WeakMap`的key必须是可以被回收的。大部分[原始类型](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)可以被任意创建，没有生命周期，因此他们不能被用来作为key。对象和[non-registered symbols](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol#shared_symbols_in_the_global_symbol_registry) 可以被用来作为key，因为他们可以被回收。

### Why WeakMap?

A map API could be implemented in JavaScript with two arrays (one for keys, one for values) shared by the four API methods. Setting elements on this map would involve pushing a key and value onto the end of each of those arrays simultaneously. As a result, the indices of the key and value would correspond to both arrays. Getting values from the map would involve iterating through all keys to find a match, then using the index of this match to retrieve the corresponding value from the array of values.

一个map API可以在Javascript中用2个Array（1个放key，1个放value）来实现(:pill: ***shared by the four API methods 这句没有理解***)。设置元素在map中，将会同时在2个数组中调用push，押入key和value。因此，key和value的指数就是相关的。获取值的时候，map会便利所有的key去匹配，然后使用匹配的index去找回相关的value（从value的数组中）。

Such an implementation would have two main inconveniences:

这样一个实现有2个主要的不方便点：

1. The first one is an O(n) set and search (n being the number of keys in the map) since both operations must iterate through the list of keys to find a matching value.
   第一个是set和search的时间复杂度都是O(n)，因为2个操作必须便利所有的key，进行匹配。
2. The second inconvenience is a memory leak because the arrays ensure that references to each key and each value are maintained indefinitely. These references prevent the keys from being garbage collected, even if there are no other references to the object. This would also prevent the corresponding values from being garbage collected.
   第二个不方便是内存泄漏，因为数组确保了引用的每个key和value都被无限期的维持着。这些引用阻止了key被回收，即使没有其他的引用到对象。这也阻止了对应的值被回收。

By contrast, in a `WeakMap`, a key object refers strongly to its contents as long as the key is not garbage collected, but weakly from then on. As such, a WeakMap:

相比之下，在`WeakMap`中，一个key对象强引用它的内容(只要key没有被回收)。但从某个时间起这个引用会变弱，例如，1个WeakMap：

- does not prevent garbage collection, which eventually removes references to the key object
  最终删除了key对象的引用后，不阻止垃圾回收。
- allows garbage collection of any values if their key objects are not referenced from somewhere other than a WeakMap
  允许垃圾回收任何值，如果key对象没有被其他地方引用，除了WeakMap。

A `WeakMap` can be a particularly useful construct when mapping keys to information about the key that is valuable only if the key has not been garbage collected.

`WeakMap`可以被作为一个特殊的结构，当映射key到关于key的信息，这个信息只有在key没有被回收的时候才能valuable。

But because a `WeakMap` doesn't allow observing the liveness of its keys, its keys are not enumerable. There is no method to obtain a list of the keys. If there were, the list would depend on the state of garbage collection, introducing non-determinism. If you want to have a list of keys, you should use a `Map`.

但是因为`WeakMap`不允许观察key的存活状态，所以key不是可枚举的。没有方法可以获取key的列表。如果有，这个列表将依赖于垃圾回收状态，引入不确定性。如果你想有一个key的列表，请使用`Map`。

## Examples

### Using WeakMap

```javascript
const wm1 = new WeakMap();
const wm2 = new WeakMap();
const wm3 = new WeakMap();
const o1 = {};
const o2 = function () {};
const o3 = window;

wm1.set(o1, 37);
wm1.set(o2, "azerty");
wm2.set(o1, o2); // a value can be anything, including an object or a function
wm2.set(o2, undefined);
wm2.set(wm1, wm2); // keys and values can be any objects. Even WeakMaps!

wm1.get(o2); // "azerty"
wm2.get(o2); // undefined, because that is the set value
wm2.get(o3); // undefined, because there is no key for o3 on wm2

wm1.has(o2); // true
wm2.has(o2); // true (even if the value itself is 'undefined')
wm2.has(o3); // false

wm3.set(o1, 37);
wm3.get(o1); // 37

wm1.has(o1); // true
wm1.delete(o1);
wm1.has(o1); // false
```

### Implementing a WeakMap-like class with a .clear() method

```javascript
class ClearableWeakMap {
  #wm;
  constructor(init) {
    this.#wm = new WeakMap(init);
  }
  clear() {
    this.#wm = new WeakMap();
  }
  delete(k) {
    return this.#wm.delete(k);
  }
  get(k) {
    return this.#wm.get(k);
  }
  has(k) {
    return this.#wm.has(k);
  }
  set(k, v) {
    this.#wm.set(k, v);
    return this;
  }
}
```

### Emulating private members

Developers can use a `WeakMap` to associate private data to an object, with the following benefits:

开发者可以使用`WeakMap`去给一个对象增加一个私有数据，可以获得以下收益：

- Compared to a `Map`, a `WeakMap` does not hold strong references to the object used as the key, so the metadata shares the same lifetime as the object itself, avoiding memory leaks.
  与`Map`相比，`WeakMap`不会持有作为key的对象的强引用，因此相关数据与作为key的对象本身共享生命周期，避免内存泄漏。
- Compared to using non-enumerable and/or `Symbol` properties, a `WeakMap` is external to the object and there is no way for user code to retrieve the metadata through reflective methods like `Object.getOwnPropertySymbols`.
  与使用非枚举`Symbol`属性相比，`WeakMap`不属于对象，无法使用反射方法（像`Object.getOwnPropertySymbols`）来获取用户的元数据。
- Compared to a closure, the same `WeakMap` can be reused for all instances created from a constructor, making it more memory-efficient, and allows different instances of the same class to read the private members of each other.
  与闭包相比，`WeakMap`可以复用

```javascript
let Thing;

{ // 这里放1个花括号，是为了让const privateScope的作用域仅仅在这个花括号内
  // 花括号以外的代码无法访问privateScope
  // 因为const的作用域是代码块级别的（即花括号）
  const privateScope = new WeakMap();
  let counter = 0;

  Thing = function () {
    this.someProperty = "foo";

    privateScope.set(this, {
      hidden: ++counter,
    });
  };

  Thing.prototype.showPublic = function () {
    return this.someProperty;
  };

  Thing.prototype.showPrivate = function () {
    return privateScope.get(this).hidden;
  };
}

console.log(typeof privateScope);
// "undefined"

const thing = new Thing();

console.log(thing);
// Thing {someProperty: "foo"}

thing.showPublic();
// "foo"

thing.showPrivate();
// 1
```

This is roughly equivalent to the following, using private fields:

这个大约相等于下main的私有fields:

```javascript
class Thing {
  static #counter = 0;
  #hidden;
  constructor() {
    this.someProperty = "foo";
    this.#hidden = ++Thing.#counter;
  }
  showPublic() {
    return this.someProperty;
  }
  showPrivate() {
    return this.#hidden;
  }
}

console.log(thing);
// Thing {someProperty: "foo"}

thing.showPublic();
// "foo"

thing.showPrivate();
// 1
```

### Associating metadata

A `WeakMap` can be used to associate metadata with an object, without affecting the lifetime of the object itself. This is very similar to the private members example, since private members are also modelled as external metadata that doesn't participate in prototypical inheritance.

`WeakMap`可以用来给对象增加metadata，不会影响对象自身的生命周期。这非常类似于私有成员的例子，因为私有成员也模仿作为一个外部的metadata（不参与原型链继承）。

This use case can be extended to already-created objects. For example, on the web, we may want to associate extra data with a DOM element, which the DOM element may access later. A common approach is to attach the data as a property:

这个用例可以扩展到已经创建的对象。例如，在web中，我们可能想要给一个DOM元素增加额外的数据，这个DOM元素可能稍后访问。常见的做法是给这个元素增加一个属性：

```javascript
const buttons = document.querySelectorAll(".button");
buttons.forEach((button) => {
  button.clicked = false;
  button.addEventListener("click", () => {
    button.clicked = true;
    const currentButtons = [...document.querySelectorAll(".button")];
    if (currentButtons.every((button) => button.clicked)) {
      console.log("All buttons have been clicked!");
    }
  });
});
```

This approach works, but it has a few pitfalls:

这样的方法，有一些缺陷：

- The `clicked` property is enumerable, so it will show up in `Object.keys(button)`, `for...in` loops, etc. This can be mitigated by using `Object.defineProperty()`, but that makes the code more verbose.
  `clicked`属性是可枚举的，所以它会出现在`Object.keys(button)`、`for...in`循环中，等等。这可以通过使用`Object.defineProperty()`来缓解，但这会增加代码的冗余。`
- The `clicked` property is a normal string property, so it can be accessed and overwritten by other code. This can be mitigated by using a `Symbol` key, but the key would still be accessible via `Object.getOwnPropertySymbols()`.
  `clicked`属性是一个普通的字符串属性，所以它可以被其他代码访问和覆盖。这可以通过使用一个`Symbol`键来缓解，但是键仍然可以通过`Object.getOwnPropertySymbols()`来访问。

Using a `WeakMap` fixes these:

使用`WeakMap`修复：

```javascript
const buttons = document.querySelectorAll(".button");
const clicked = new WeakMap();
buttons.forEach((button) => {
  clicked.set(button, false);
  button.addEventListener("click", () => {
    clicked.set(button, true);
    const currentButtons = [...document.querySelectorAll(".button")];
    if (currentButtons.every((button) => clicked.get(button))) {
      console.log("All buttons have been clicked!");
    }
  });
});
```

Here, only code that has access to `clicked` knows the clicked state of each button, and external code can't modify the states. In addition, if any of the buttons gets removed from the DOM, the associated metadata will automatically get garbage-collected.

这儿，仅仅只有访问`clicked`的代码知道每个按钮的点击状态，并且外部代码不能修改这些状态。此外，如果任何按钮从DOM中移除，关联的元数据也会自动被垃圾回收。

### Caching

You can associate objects passed to a function with the result of the function, so that if the same object is passed again, the cached result can be returned without re-executing the function. This is useful if the function is pure (i.e. it doesn't mutate any outside objects or cause other observable side effects).

你可以把传递给函数的对象与函数的结果关联起来，这样，如果同一个对象被传递第二次，就可以返回缓存的结果，而不需要重新执行函数。这很有用，如果函数是纯的（即它不会改变任何外部对象，也不会导致其他可观察的副作用）。

```javascript
const cache = new WeakMap();
function handleObjectValues(obj) {
  if (cache.has(obj)) {
    return cache.get(obj);
  }
  const result = Object.values(obj).map(heavyComputation);
  cache.set(obj, result);
  return result;
}

```

This only works if your function's input is an object. Moreover, even if the input is never passed in again, the result still remains forever in the cache. A more effective way is to use a `Map` paired with `WeakRef` objects, which allows you to associate any type of input value with its respective (potentially large) computation result. See the WeakRefs and FinalizationRegistry example for more details.

这仅仅工作在如果你的函数的输入是一个对象。此外，即使输入从未被传递过，结果仍然会永远留在缓存中。一个更有效的方法是使用WeakRef对象与Map配对，这允许你将任何类型的输入值与相应的（可能很大）计算结果关联起来。有关更多详细信息，请参阅WeakRefs和FinalizationRegistry示例。

---

==以上是mdn文档的关于WeakMap的部分截取==

<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map>
