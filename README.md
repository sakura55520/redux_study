# redux_study
redux学习笔记

Action表示应用中的各类动作或操作，不同的操作会改变应用相应的state状态，说白了就是一个带type属性的对象。

Store则是我们储存state的地方。我们通过redux当中的createStore方法来创建一个store，它提供3个主要的方法，在这里我们可以模拟一下createStore的源码

```
const createStore = (reducer) => {
  let state;
  let listeners = [];
  // 用来返回当前的state
  const getState = () => state;
  // 根据action调用reducer返回新的state并触发listener
  const dispatch = (action) => {
      state = reducer(state, action);
      listeners.forEach(listener => listener());
    };
  /* 这里的subscribe有两个功能
   * 调用 subscribe(listener) 会使用listeners.push(listener)注册一个listener
   * 而调用 subscribe 的返回函数则会注销掉listener
   */
  const subscribe = (listener) => {
      listeners.push(listener);
      return () => {
        listeners = listeners.filter(l => l !== listener);
      };
    };
 
  return { getState, dispatch, subscribe };
};
```

reducer的demo:

```

// 以下代码示例来自redux官方教程
 
// reducer接受state和action并返回新的state
const todos = (state = [], action) => {
  // 根据不同的action.type对state进行不同的操作，一般都是用switch语句来实现，当然你要用if...else我也拦不住你
  switch (action.type) {
    case 'ADD_TODO':
      return [
        // 这里是ES7里的对象展开运算符语法
        ...state,
        {
          id: action.id,
          text: action.text,
          completed: false
        }
      ];
    // 不知道是什么action类型的话则返回默认state
    default:
      return state;
  }
};
```
如果非要翻译reducer的话，可以将其翻译为缩减器或者折叠器？

为了进一步加深理解，我们再了解一下reduce是什么东西，这个名词其实是函数式编程当中的一个术语，在更多的情况下，reduce操作被称为Fold折叠
直观起见，我们还是拿JavaScript来理解。reduce属于一种高阶函数，它将其中的回调函数reducer递归应用到数组的所有元素上并返回一个独立的值。这也就是“缩减”或“折叠”的意义所在了。

总而言之一句话，redux当中的reducer之所以叫做reducer，是因为它和 Array.prototype.reduce 当中传入的回调函数非常相似。

