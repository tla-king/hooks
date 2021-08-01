## 实现自定义的 useDebounce
防抖：传入的两个参数为：fn（要执行的回调方法）和 delay（防抖时间），然后该 hook 返回一个执行方法
```js
//hooks
import { useCallback, useRef } from 'react';

const useDebounce = (fn: Function, delay = 100) => {
  const time1 = useRef<any>();

  return useCallback((...args) => {
    if (time1.current) {
      clearTimeout(time1.current);
    }
    time1.current = setTimeout(() => {
      fn(...args);
    }, delay);
  }, [delay]);
};

export default useDebounce;

//调用
import React, { useCallback, useState } from 'react';
import useDebounce from './useDebounce';

const MyPage = () => {
  const [count, setCount] = useState(0);
  const fun = useCallback(() => {
    setCount(count => count + 1);
  }, []);

  const run = useDebounce(fun, 3000);

  return (
    <div >
      <button type="button" onClick={() => { run(); }}>
        增加 {count}
      </button>
    </div>
  );
};

export default MyPage;
```


## 实现自定义的useThrottle

```js
import { useEffect, useRef, useState } from 'react'

const useThrottle = (fn, ms = 30, deps = []) => {
    let previous = useRef(0)
    let [time, setTime] = useState(ms)
    useEffect(() => {
        let now = Date.now();
        if (now - previous.current > time) {
            fn();
            previous.current = now;
        }
    }, deps)

    const cancel = () => {
        setTime(0)
    }

    return [cancel]
  }

export default useThrottle
```


## 实现自定义的 useTimeout
延迟调用定时器
- 需要在 dom 卸载时，手动进行 clearTimeout 将定时器移除，否则可能造成内存泄漏
- 避免多次重复写移除代码
- 只关注调用，不必关注移除定时器
```js
import { useEffect } from 'react';

function useTimeout (fn: () => void, delay: number) {
  useEffect(() => {
    const timer = setTimeout(() => {
      fn();
    }, delay);
    return () => {
      clearTimeout(timer); // 移除定时器
    };
  }, [delay]);
}

export default useTimeout;
```

## 实现自定义的 useInterval
定时调用定时器
```js
import { useEffect } from 'react';

function useInterval (fn: () => void, delay: number) {
  useEffect(() => {
    const timer = setInterval(() => {
      fn();
    }, delay);
    return () => {
      clearInterval(timer); // 移除定时器
    };
  }, [delay]);
}

export default useInterval;

```


## 实现自定义的 usePrevious

- 需要保存上一次渲染时 state 的值
- 主要用到 useRef.current 来存放变量

```js
// 自定义方法封装
import { useRef } from 'react';

function usePrevious<T> (state: T): T|undefined {
  const prevRef = useRef<T>();
  const curRef = useRef<T>();

  prevRef.current = curRef.current;
  curRef.current = state;

  return prevRef.current;
}

export default usePrevious;


//调用
import React, { useState } from 'react';
import usePrevious from './usePrevious';

const MyPage = () => {
  const [count, setCount] = useState(0);
  const previous = usePrevious(count);

  return (
    <div >
      <div>新值:{count}</div>
      <div>旧值:{previous}</div>
      <button type="button" onClick={() => { setCount(count + 1); }}>
        增加
      </button>
    </div>
  );
};

export default MyPage;

```



## 实现自定义的 useImageLazy
栗子🌰🌰：图片懒加载
- 不直接给img标签的src属性赋值，
- 当图片到顶部的距离大于可视区域和滚动区域之和的时候，再的值赋值给src
- `getBoundingClientRect`  返回元素的大小及其相对于视口的位置，是一个集合；集合中有top, right, bottom, left等属性。

```js
//先实现一个throttle节流的自定义hooks，scroll事件一般都要配合其使用

import React, { useRef, useState, useEffect } from 'react'

const useThrottle = (fn, wait, deps = []) => {
    let prev = useRef(0)
    const [time, setTime] = useState(wait)

    useEffect(() => {
        let curr = Date.now()
        if (curr - prev.current > wait) {
            fn()
            prev.current = curr
        }
    }, deps)
}

export default useThrottle


//实现图片懒加载hooks useImageLazy
import React, { useState, useEffect, useRef } from 'react'
import useThrottle from './useThrottle'

const useImageLazy = (domList) => {
    const [scrollCount, setScrollCount] = useState(0)

    const savedCallback = useRef()

    const getTop = () => {
        let scrollTop = document.documentElement.scrollTop || document.body.scrollTop
        let clientHeight = document.documentElement.clientHeight || document.body.clientHeight
        let len = domList.length

        for (let i = 0; i < len; i++) {
            let { top } = domList[i].getBoundingClientRect()
            if (top < (scrollTop + clientHeight)) {
                domList[i].src = domList[i].dataset.src
                domList.splice(i, 1)
                i--;
                len--;
            }
        }
    }

    const scrollEvent = () => {
        setScrollCount(scrollCount + 1)
    }

    useEffect(() => {
        savedCallback.current = scrollEvent
    })

    useEffect(() => {
        let tick = () => { savedCallback.current() }
        document.addEventListener('scroll', tick)

        return () => {
            document.removeEventListener('scroll', tick)
        }
    }, [])

    useThrottle(() => {
        getTop()
    }, 200, [scrollCount])

}

export default useImageLazy


//在组件里使用：
import React, { useRef } from 'react'
import useImagelazy from '../common/use/useImagelazy'

const imgList = Array.from({ length: 8 }, (item, index) => (item = `https://www.maojiemao.com/public/svg/gen${index + 5}.png`))

const style = {
    display: 'block',
    width: '300px',
    height: '300px',
    marginTop: '50px',
}

const Test = () => {
    const domRef = useRef([])
    useImagelazy(domRef.current)

    return (
        <>
            {imgList.map((item, index) => (
                <img
                    ref={el => domRef.current[index] = el}
                    key={`lazy-${index}`}
                    data-src={item}
                    style={style} />
            ))}
        </>
    )

}

export default Test

```


## 实现自定义的 useInitialRequest
在实践场景中，几乎每个页面都会在初始化时加载至少一个接口，<br/>
而这些个接口有一些统一的处理逻辑可以抽离，例如请求成功，返回数据，请求失败，异常处理，特定时机下刷新<br/>
可利用**自定义hooks**封装这个场景

```js
import {useState, useEffect} from 'react';

export default function useInitial<T, P>(
  api: (params: P) => Promise<T>,
  params: P,
  defaultData: T
) {
  const [loading, setLoading] = useState(true);
  const [response, setResponse] = useState(defaultData);
  const [errMsg, setErrmsg] = useState('');

  useEffect(() => {
    if (!loading) { return };
    getData();
  }, [loading]);

  function getData() {
    api(params).then(res => {
      setResponse(res);
    }).catch(e => {
      setErrmsg(errMsg);
    }).finally(() => {
      setLoading(false);
    })
  }

  return {
    loading,
    setLoading,
    response,
    errMsg
  }
}

//页面中调用
export default function FunctionDemo() {
    // 只需要传入api， 对应的参数与返回结果的初始默认值即可
    const {loading, setLoading, response, errMsg} = useInitial(api, {id: 10}, {});
}

//当我们想要刷新页面，只需要执行一句代码即可
setLoading(true);
```

## 实现自定义的 useCurrentValue
-  `ref` 对象在组件的整个生命周期内保持不变
-  当更新 `current` 值时并不会 `re-render` ，这是与 `useState` 不同的地方
-  `useRef` 类似于类组件的 `this`

因此可以利用`useRef` 可以用来定义变量，这些变量更改之后不会引起页面重新渲染，比如分页获取数据时，存储页码

```js
import {useRef} from 'react'

export const useCurrentValue = value => {
    const ref = useRef(null)

    useEffect(() => {
        ref.current = value
    }, [value])

    return ref.current
}
```

## 实现自定义的 useModalState
栗子🌰🌰： `oms_web`  `send_web` 等
```js
import {useState,useCallback} from 'react'

export const useModalState = (state) => {
    const [data, setData] = useState(!state && {
        visible: false,
        currentItem: {}
    })

    const onOpen = useCallback((obj = {}) => {
        setData(prev => ({
            ...prev,
            visible: true,
            currentItem: obj
        }))
    }, [])

    const onClose = useCallback(() => {
        setData(prev => ({
            ...prev,
            visible: false,
            currentItem: {}
        }))
    }, [])

    return {
        state: data,
        setState: setData,
        onOpen,
        onClose
    }
}

```

## 实现自定义的 useQueryState
栗子🌰🌰🌰🌰：有 `filter` 查询的，需要记录当前搜索选项值

```js
import {useState,useCallback} from 'react'

export const useQueryState = (initQuery = {}) => {
    const [query, setQuery] = useState(initQuery)

    const onChangePage = useCallback((pageNo) => {
        setQuery(prev => ({
            ...prev,
            pageNo: pageNo
        }))
    }, [])

    const onChange = useCallback((params) => {
        setQuery(prev => ({
            ...prev,
            ...params
        }))
    }, [])

    const onSubmit = useCallback((v, key) => {
        setQuery(prev => ({
            ...prev,
            [key]: v,
            pageNo: 1,
        }))
    }, [])

    const onReset = useCallback(() => {
        setQuery({})
    }, [])

    return [
        query,
        {
            onChangePage: onChangePage,
            onChange: onChange,
            onSubmit: onSubmit,
            onReset: onReset
        }
    ]
}
```


## 实现自定义的 useRowSelection
栗子🌰🌰🌰：比如 `antd` 的 `table` 选择多项列表的方法 `rowSelection`,不想没此都写，大可如此封装
```js
import {
    Fragment,
    memo,
    useEffect,
    useState,
    useCallback,
    useRef,
    useReducer,
    createContext,
} from 'react'

/**
 * 列表多选
 * @param {Array} item 选择的数据回显
 */
export const useSelection = (value, refreshDeps = []) => {

    const [select, setSelect] = useState(value || [])

    const rowSelection = {
        selectedRowKeys: select.map(v => v.id),
        onSelect (record, selected, selectedRows) {
            // console.log(selected)
            if (selected) {
                setSelect(prev => ([...prev, record]))
            } else {
                setSelect(prev => prev.filter(v => v.id !== record.id))
            }
        },
        onSelectAll (selected, selectedRows, changeRows) {
            // console.log(selected, selectedRows, changeRows)
            if (selected) {
                const arr = changeRows.filter(item => !select.some(v => v.id === item.id))
                setSelect(prev => ([...prev, ...arr]))
            } else {
                setSelect(prev => prev.filter(item => !changeRows.some(v => v.id === item.id)))
            }
        }
    }

    // console.log('select => ', select)

    useEffect(() => {
        // console.log(value)
        if (!Array.isArray(value) || value.length <= 0) return
        setSelect(value)
    }, [value])

    useEffect(() => {
        setSelect([])
        // eslint-disable-next-line react-hooks/exhaustive-deps
    }, refreshDeps)

    return {
        select: select,
        setSelect: setSelect,
        rowSelection: rowSelection
    }
}

```



## 总结一下
> - 自定义hooks其实就是解决 **逻辑片段复用** ，这是自定义hook的底层思维
> - 利用 `HOC` `render props` `mixin` 也都可以解决，但个人感觉他们:可读性不高,存在局限性,代码不够优雅
> - 理解了这个思维，就能够容易的辨别出来，哪些场景需要使用自定义hooks。也能够感受得到，自定义hooks对于大型项目的重要性




-----

~~学习累了，安利一波，马云爸爸的，鸡汤。。激励一下自己~~

-----

![png](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fi0.hdslb.com%2Fbfs%2Farticle%2Fe4e84574a74992b80a5c11382d4ba1c24a17c101.jpg&refer=http%3A%2F%2Fi0.hdslb.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1630410587&t=8ca1dab066f540ec0835e02a09396435)
