
## useState
...


## useEffect
...


## useContext
如果对context API 比较熟悉，应该好理解，

它主要用于函数组件之间传值的问题
```js
    const value = useContext(MyContext);
    // useContext相当于 <MyContext.Consumer>
    // useContext(MyContext) 只是让你能够读取 context 的值以及订阅 context 的变化。
    // 你仍然需要在上层组件树中使用 <MyContext.Provider> 来为下层组件提供 context
```

[](https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fdingyue.ws.126.net%2FjJ%3DrEkiPgyyLqhWDwjBR5lfZFFhacRTQJmaDA83RIOjfE1516021084693compressflag.jpg&refer=http%3A%2F%2Fdingyue.ws.126.net&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=jpeg?sec=1630380649&t=3bc5e786b8dbb6ca4c64c5d929e7a7a6)
