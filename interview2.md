## 玄说前端面试层层解析—关于 redux 的源码

此篇文章是 redux 系列的第二篇，上一篇关于面试题的文章，请看：
[玄说前端面试层层提问—关于 redux 的面试题]()

面试题中，有很多需要清楚 redux 内部究竟是怎么样的，今天就一起来看个明白！

首先，你需要知道几件事情：

> 1.redux 和 react 是没有任何关系的，redux 只是一个状态管理器，它也可以用到 Vue 里  
> 2.connect 是属于 react-redux 的，react-redux 是连接 redux 和 react 的一个库

### 今日解析

概括一点：redux 就是一个状态管理器，那到底什么是状态管理器呢？

#### 1.实现一个简单的状态管理器

比如计数器中的 number，如下代码：

```javascript
let state = {
  number: 1
};
```

使用状态的时候通过`state.number`,比如修改状态的时候`state.number=2`, 这样修改 number 会有一个问题，使用到 number 的地方收不到通知，这里我们使用发布订阅模式来解决这个问题。  
代码这样处理：

```javascript
let state = {
  number: 1
};
let listeners = [];

/**订阅*/
function subscribe(listener) {
  listeners.push(listener);
}

/**改变number的时候*/
function changeNumber(number) {
  state.number = number;
  /**通知所有订阅者*/
  for (let i = 0; i < listeners.length; i++) {
    const listener = listeners[i];
    listener();
  }
}
```

这个时候，咱们先来尝试一下这个计数状态管理器。

```javascript
/**订阅*/
subscribe(() => {
  console.log(state.number);
});

/**通过changeNumber改变*/
changeNumber(2);
```

现在可以观察到，修改 number 的时候，订阅的方法会触发，打印出相应的 number 的值。但是现在有几个问题咱们需要面对一下：

> 1.这个状态管理器只能管理 number，不能通用  
> 2.公共的代码没有封装起来

咱们把公用的代码封装起来：

```javascript
/**
 * initState 初始状态
 */
const createStore = function(initState) {
  let state = initState;
  let listeners = [];

  /**订阅*/
  function subscribe(listener) {
    listeners.push(listener);
  }

  function changeState(newState) {
    state = newState;
    /**通知*/
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i];
      listener();
    }
  }
  /**获取当前状态*/
  function getState() {
    return state;
  }
  /**返回所有封装的方法*/
  return { subscribe, changeState, getState };
};
```

咱们现在来试试这个状态管理来管理多个状态 counter 和 info ；

```javascript
/**初始数据*/
let initState = {
  counter: {
    count: 1
  },
  info: {
    name: "",
    description: ""
  }
};
/**初始化store，传入初始状态*/
let store = createStore(initState);

/**订阅*/
store.subscribe(() => {
  let state = store.getState();
  console.log(`${state.info.name}：${state.info.description}`);
});
store.subscribe(() => {
  let state = store.getState();
  console.log(state.counter.count);
});

/**通过changeState方法改变数据info*/
store.changeState({
  ...store.getState(),
  info: {
    name: "玄说前端",
    description: "专注前端的公众号"
  }
});

/**通过changeState方法改变数据counter*/
store.changeState({
  ...store.getState(),
  counter: {
    count: 2
  }
});
```

到目前，咱们已经完成了一个简单的状态管理器。
在 createStore 中，提供了三个方法，改变状态的方法 changeState，获取当前状态的方法 getState，订阅状态发生改变的方法 subscribe

#### 2.实现一个有计划的状态管理器

很多同学会非常迷惑，这个有计划是什么意思？且听我细细道来。  
我们基于上面的状态管理器来实现一个自增，自减的计数器。

```javascript
let initState = {
  count: 0
};
let store = createStore(initState);

/**监听*/
store.subscribe(() => {
  let state = store.getState();
  console.log(state.count);
});

/*自增===>功能正常*/
store.changeState({
  count: store.getState().count + 1
});
/*自减===>功能正常*/
store.changeState({
  count: store.getState().count - 1
});

/*此处我这么来一下子，一脸懵逼*/
store.changeState({
  count: "玄说"
});
```

这个代码中，有一个问题，就是 count 被随意改成了字符串`玄说`,我们对于 count 的修改没有任何约束，它可以被改成任何值，任何地方都可以修改。这样在程序就变得不可维护了。  
此时我们需要约束 count，不允许随意修改 count 的值，只允许 count 自增或者自减。

两步来解决这个问题：

> 1.初始化 store 的时候，让他知道我们的修改计划是什么，制定一个 state 的修改计划。  
> 2.修改 store.changeState 方法，让他在修改 state 的时候，按照我们的计划来修改

这样我们来制定一个 state 的修改计划，命名为 plan 函数，它接受现在的 state 和一个我们需要它按照修改的计划名称 action，返回一个改变之后的 state。

```javascript
/*注意：action = {type:'',other:''}, action 必须有一个 type 属性,表明我们的计划*/
function plan(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return {
        ...state,
        count: state.count + 1
      };
    case "DECREMENT":
      return {
        ...state,
        count: state.count - 1
      };
    default:
      return state;
  }
}
```

现在已经创建好了一个按照意愿来修改的计划，把计划告诉 store，store.changeState 就会按照计划来修改。

修改一下之前的`createStore`

```javascript
/*增加一个参数 plan*/
const createStore = function(plan, initState) {
  let state = initState;
  let listeners = [];

  function subscribe(listener) {
    listeners.push(listener);
  }

  /*给changeState传入要修改的计划*/
  function changeState(action) {
    /*按照计划来修改 state*/
    state = plan(state, action);
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i];
      listener();
    }
  }

  function getState() {
    return state;
  }
  return {
    subscribe,
    changeState,
    getState
  };
};
```

现在再来试一下行的 createStore 实现的自增和自减：

```javascript
let initState = {
  count: 0
};
/*传入一开始定义的plan*/
let store = createStore(plan, initState);

/**监听*/
store.subscribe(() => {
  let state = store.getState();
  console.log(state.count);
});

/*自增*/
store.changeState({
  type: "INCREMENT"
});

/*自减*/
store.changeState({
  type: "DECREMENT"
});

/*传入无效的计划或者值类型，不会修改原state*/
store.changeState({
  count: "abc"
});
```

这样就实现了一个有计划的状态管理器；  
到这一步基本的模型就已经实现了，把 plan 的名字修改成 reducer，changeState 修改成 dispatch，这样和 redux 的叫法就一样了。

#### reducer 的处理

但是现在还存在一个问题，reducer 是一个计划函数，接受的是老的 state，一般在项目中有大量 state，每一个 state 的修改，都需要计划函数，如果所有的计划都写在一个 reducer 函数里面，这样就显得很繁杂，也不好理解。  
我们可以按照组件的维度来拆分出多个 reducer 函数，然后通过一个合并函数，把所有的 reducer 合并到一起。比如现在有两个 state，counter 和 info。

```javascript
let state = {
  counter: {
    count: 1
  },
  info: {
    name: "玄说前端",
    description: "前端公众号"
  }
};
```

拆分成两个 reducer，counterReducer 和 infoReducer。

```javascript
/**
 * counterReducer
 */
//这个reducer需要接受的数据是state状态里面的state.counter！！！！！
function counterReducer(state, action) {
  switch (action.type) {
    case "INCREMENT":
      return {
        count: state.count + 1
      };
    case "DECREMENT":
      return {
        ...state,
        count: state.count - 1
      };
    default:
      return state;
  }
}

/**
 * infoReducer
 */
//这个reducer需要接受的数据是state状态里面的state.info！！！！！
function infoReducer(state, action) {
  switch (action.type) {
    case "SET_NAME":
      return {
        ...state,
        name: action.name
      };
    case "SET_DESCRIPTION":
      return {
        ...state,
        description: action.description
      };
    default:
      return state;
  }
}
```

现在把两个 reducer 通过一个函数把他合并起来，我们把这个函数叫做 combineReducers，一般它是这么用的，传入一个对象，对象包含所有的 reducer，它的 key 值对应 state 的 key 值是一样的：

```javascript
const reducer = combineReducers({
  counter: counterReducer,
  info: infoReducer
});
```

基于用法，需要返回一个合并的 reducer，执行完成之后，传入 state 状态和计划，可以返回一个新的 state，我们来实现以下：

```javascript
/**传入一个包含多个reducer的对象*/
function combineReducers(reducers) {
  /* 获取传入对象所有的key，reducerKeys = ['counter', 'info']*/
  const reducerKeys = Object.keys(reducers);

  /*返回一个合并后的新的reducer函数，接受state和action参数*/
  return function combination(state = {}, action) {
    /*初始化一个新的state*/
    const nextState = {};

    /*遍历循环执行所有的reducer，整合所有的state为一个新的*/
    for (let i = 0; i < reducerKeys.length; i++) {
      const key = reducerKeys[i];
      /*获取当前循环的reducer*/
      const reducer = reducers[key];
      /*传入的reducer对象的key和总的state的key一样。所以通过key值，获取reducer对应要改变的state*/
      const previousStateSingle = state[key];
      /*执行 当前循环的reducer，生成新的state*/
      const nextStateSingle = reducer(previousStateSingle, action);

      /**每一个新的state通过key，生成新的总的state*/
      nextState[key] = nextStateSingle;
    }
    return nextState;
  };
}
```

至此，我们已经完成了 combineReducers，来试验一下：

```javascript
const reducer = combineReducers({
  counter: counterReducer,
  info: InfoReducer
});
let state = {
  counter: {
    count: 1
  },
  info: {
    name: "玄说前端",
    description: "前端公众号"
  }
};
/**生成store*/
let store = createStore(reducer, initState);

/**监听修改*/
store.subscribe(() => {
  /**获取新的state*/
  let { counter, info } = store.getState();
  console.log(counter.count, info.name, info.description);
});

/*自增*/
store.dispatch({
  type: "DECREMENT"
});

/*修改 description*/
store.dispatch({
  type: "SET_DESCRIPTION",
  description: "我爱看玄说前端"
});
```

至此，reducer 拆分逻辑已经完成，在业务中只需要每个组件管理自己的状态，后面再通过合并就好。

#### state 的整合

就目前来看还存在一个问题（怎么特么这么多问题呀，我也没有办法，层层深入呗 😭），state 所有的还写在一起的，这样每个组件的业务管理 state 还是有点点难维护，基于上面对于 reducer 按照组件的维度来拆分的逻辑，我们也在这个维度来做拆掉。

---

在业务中的时候这样写：

```javascript
/**把每一个reducer所有修改的状态，单独和这个reducer写在一起*/
/**比如上面的counter*/
//单个的state
let initState = {
  count: 1
};
//所对应的reducer
function counterReducer(state, action) {
  /*注意：如果参数 state 没有初始值，那就给他初始值！！*/
  if (!state) {
    state = initState;
  }
  switch (action.type) {
    case "INCREMENT":
      return {
        count: state.count + 1
      };
    default:
      return state;
  }
}
```

所有单个的状态 state，需要在一个地方我们把它整合成一个，这个最好的地方就是在 createStore 里面：

```javascript
const createStore = function(reducer, initState) {
  let state = initState;
  let listeners = [];

  function subscribe(listener) {
    listeners.push(listener);
  }

  function dispatch(action) {
    state = reducer(state, action);
    for (let i = 0; i < listeners.length; i++) {
      const listener = listeners[i];
      listener();
    }
  }

  function getState() {
    return state;
  }
  /**只是在此处增加了一行，通过一个不匹配任何计划的 type，来获取初始值*/
  dispatch({ type: Symbol() });

  return {
    subscribe,
    dispatch,
    getState
  };
};
```

这样在 createStore 被调用的时候，不匹配任何 action，每一个 reducer 就会返回初始化的 state，合并 reducer 的时候，就把所有的 state 整合到了一起。
基于上面的 reducer，来试试下面的这个情况：

```javascript
/*不传入初始化state */
const store = createStore(reducer);
/*获取state*/
console.log(store.getState());
```

到目前为止，redux 里面基本的 reducer，createStore，combineReducers 等方法已经实现的差不多了。但是还有一个特别重要的东西—**中间件**

#### middleware 中间件的构成
