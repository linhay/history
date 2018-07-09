

# 将 callback函数 封装为 promise 对象

[代码传送门](https://github.com/linhay/dustdin/blob/master/javascript/callback%20to%20promise.js)

### 1. callback函数:

```javascript
// callback
function funcCallback(value1, value2, callback) {
    setTimeout(function () {
        callback('value1: ' + value1 + " | " + 'value2: ' + value2)
    }, 2000)
}

// 调用
funcCallback(1, 2, function (res) {
    console.log("--funcCallback--")
    console.log(res)
})

/* log
--funcCallback--
value1: 1 | value2: 2
*/
```

### 2. Promise形式封装:

```javascript
// primse
function funcPromise(value1, value2) {
    return new Promise((resolve, reject) => {
        funcCallback(value1, value2, resolve, reject);
    });
}

// 调用
funcPromise(3,4).then((result) => {
    console.log("--funcPromise--")
    console.log(result)
}).catch((err) => {
    console.log("--funcPromise--")
    console.log(err)
});

/* log
--funcPromise--
value1: 3 | value2: 4
*/
```

### 3. async/await形式(我没理解错的话其实是Promise的语法糖?):

```javascript
async function funcAsync(value1, value2) {
    try {
        let result = await funcPromise(value1, value2);
        console.log("--funcAsync--")
        console.log(result)
    } catch (err) {
        console.log("--funcAsync--")
        console.log(err)
    }
}
// 调用
funcAsync(5,6)

/*
--funcAsync--
value1: 5 | value2: 6
*/
```
