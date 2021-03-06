---
title: hooks组件详解(上)
categories: []
date: 2020-7-19 10:21:35
tags: react
keywords: 前端 
---
# hooks组件详解（上）
1. useState用法
2. useREducer用法
3. useContent用法
4. useEffect & useLayoutEffect的区别用法
5. useMemo && React.memo && useCallback用法

## 1.useState用法
- useState使用状态
- 注意事项
  - 不可以局部更新
    - 如果state是一个对象，setState不会自动合并属性
  - 如果setState（obj）如果地址不变，react就会认为数据没变
  - useState可以接收函数
``` javascript
    const [state,setState] = useState(()=>{
        return {}
    })
```
案例
``` javascript
    function App() {
        const [user,setUser] = useState({name:'小明', age: 18})
        const onClick = ()=>{
            setUser({
            ...user,
            name: '小红'
            })
        }
        return (
            <div className="App">
            <h1>{user.name}</h1>
            <h2>{user.age}</h2>
            <button onClick={onClick}>Click</button>
            </div>
        );
    }
```

## 2. useREducer用法
- 可以理解成useState的复杂版
```javascript
    const initial = {
        n: 0
    };

    const reducer = (state, action) => {
        if (action.type === "add") {
            return { n: state.n + action.number };
        } else if (action.type === "multi") {
            return { n: state.n * 2 };
        } else {
            throw new Error("unknown type");
        }
    };

    function App() {
        const [state, dispatch] = useReducer(reducer, initial);
        const { n } = state;
        const onClick = () => {
            dispatch({ type: "add", number: 1 });
        };
        const onClick2 = () => {
            dispatch({ type: "add", number: 2 });
        };
        return (
            <div className="App">
            <h1>n: {n}</h1>

            <button onClick={onClick}>+1</button>
            <button onClick={onClick2}>+2</button>
            </div>
        );
    }
```

## 3. useContent用法
- useContent全局变量是全局的上下文
- 注意
  - 不是响应式的
    - 在一个模块将C里面的值改变后，另一个模块不会感知这个变化

``` javascript
    const C = createContext(null);

    function App() {
        console.log("App 执行了");
        const [n, setN] = useState(0);
        return (
            <C.Provider value={{ n, setN }}>
            <div className="App">
                <Baba />
            </div>
            </C.Provider>
        );
    }

    function Baba() {
        const { n, setN } = useContext(C);
        return (
            <div>
            我是爸爸 n: {n} <Child />
            </div>
        );
    }

    function Child() {
        const { n, setN } = useContext(C);
        const onClick = () => {
            setN(i => i + 1);
        };
        return (
            <div>
            我是儿子 我得到的 n: {n}
            <button onClick={onClick}>+1</button>
            </div>
        );
    }
```

## 4. useEffect & useLayoutEffect的区别用法
- useEffect 副作用
  - 对环境的改变就是副作用,如修改了document.title
- 用途
  - 在函数组件中模拟componentDidMount\componentDidUpdate\componentWillUnmount
  - 以上三种用途可以同时存在
- 当多个useEffect出现时,会按照出现的次序执行
```javascript
    function App(){
        const [n,setN] = useState(0)
        const add = ()=>{
            setN((state)=>{return state+1})
        }
        useEffect(()=>{
            console.log("第一次渲染执行")
        },[])
        useEffect(()=>{
            console.log("n发生变化是执行")
        },[n])
        useEffect(()=>{
            return ()=>{
                console.log("页面销毁时执行")
            }
        },[])
        return(
            <div>
                {n}
                <button onClick={add}>add</button>
            </div>
        )
    }
```
### useLayoutEffect
用法和useEffect完全相同
案例一
``` javascript
    const App = () => {
        const [value, setValue] = useState(0);

        useEffect(() => {
            document.querySelector("#x").innerText = `value: 1000`;
        }, [value]);

        return (
            <div id="x" onClick={() => setValue(0)}>
                value: {value}
            </div>
        );
    };
```
上面的代码会在会浏览器开始显示value:0,然后在显示value:100;(如果想让浏览器直接显示value:100)
```javascript
    useLayoutEffect(() => {
        document.querySelector("#x").innerText = `value: 1000`;
    }, [value]);
```
### useEffect和useLayoutEffect的区别
- useEffect在render之后执行,所以页面会先显示value:0然后在更新为value:1000
- useLayoutEffects在render之前执行,在页面渲染之前value已经被更改为1000;
固两者这的区别就是执行时机的区别,在开发过程中推荐使用useEffect

## 5. useMemo && React.memo && useCallback用法
- useMemo为了解决React的多余渲染
  - 场景 当App组件引用子组件时,当App组件更新时会重新调用App()重新并重新渲染子组件,这就导致了子组件在没有任何改变就被重新渲染了.
- useMemo 搭配React.memo使用

``` javascript
    function App(){
    console.log("调用了app")
    const [n,setN] = useState(0)
    const [m,setM] = useState(0)
    const addN = ()=>{
        setN((state)=>{
            return state+1
        })
    }
    const addM = useMemo(()=>{
        return ()=>{
                    setM((state)=>{
                        return state+1
                    })
                }
    },[m])
    return (
        <div>
            <div>
                prend:{n}
                <button onClick={addN}>addN</button>
            </div>
            {/*<Child2 data={m} onClick={addM}></Child2>*/}
            <Child2 data={m} onClick={addM}></Child2>
        </div>
    )

}

const Child2 = React.memo((props)=>{
    console.log("调用了CHild")
    return(
        <div>
            Child: {props.data}
            <button onClick={props.onClick}>addM</button>
        </div>
    )
})

```

usememo的语法糖 useCallback
```javascript
    //useMemo的语法糖
    const addM =useCallback(
        ()=>{
            setM((state)=>{
                return state+1
            })
        },[m]
    )
```











