# AbortController-中止请求
**控制器+信号机动**

满足AbortController和AbortSignal：

```jsx
const controller = new AbortController();
const signal = controller.signal;
```

控制器只有一种方法：

```jsx
controller.abort();
```

当你这样做时，它会通知信号：

```jsx
signal.addEventListener('abort', () => {  
    // Logs true:  
    console.log(signal.aborted);
});
```

这个 API 是由[DOM 标准](https://dom.spec.whatwg.org/#aborting-ongoing-activities)提供的，这就是整个 API。它是故意通用的，因此可以被其他 Web 标准和 JavaScript 库使用

**中止信号和获取**

Fetch 可以带一个AbortSignal. 例如，以下是您在 5 秒后获取超时的方法

```jsx
const controller = new AbortController();
const signal = controller.signal;
setTimeout(() => controller.abort(), 5000);
fetch(url, { signal }).then(response => {  
    return response.text();
}).then(text => { 
    console.log(text);
});
```

当您中止提取时，它会中止请求和响应，因此对响应正文（例如response.text()）的任何读取也会中止。

::: tip 注意：
在 fetch 已经完成后调用 **.abort()** 是可以的，fetch 只是忽略它。
:::

**这是一个演示**– 在撰写本文时，唯一支持此功能的浏览器是 Firefox 57。此外，请做好准备，没有任何具有任何设计技能的人参与创建演示。

或者，可以将信号提供给请求对象，然后传递给 fetch：

```jsx
const controller = new AbortController();
const signal = controller.signal;
const request = new Request(url, { signal });
fetch(request);
```

这是有效的，因为request.signal是一个AbortSignal.

::: tip 注意
从技术上讲，**request.signal**这与您传递给构造函数的信号不同。这是一个**AbortSignal**模拟传递给构造函数的信号的新方法 。这意味着每个**Request**都有一个信号，无论一个信号是否被提供给它的构造函数。
:::

**对中止的提取做出反应**

当你中止一个异步操作时，promise 会拒绝一个**DOMException**命名的**AbortError**

```jsx
fetch(url, { signal }).then(response => {  
    return response.text();
}).then(text => {  
    console.log(text);
}).catch(err => { 
 	if (err.name === 'AbortError') {    
 		console.log('Fetch aborted'); 
  	} else {    
  		console.error('Uh oh, an error!', err); 
   }
});
```

如果用户中止操作，您通常不希望显示错误消息，因为如果您成功执行用户要求的操作，这不是“错误”。为避免这种情况，请使用上面的 if 语句来专门处理中止错误。

这是一个示例，它为用户提供了一个用于加载内容的按钮和一个用于中止的按钮。如果获取错误，则会显示错误，***除非***它是中止错误：

```jsx
// This will allow us to abort the fetch.
    let controller;
    // Abort if the user clicks:
    abortBtn.addEventListener('click', () => {
        if (controller) controller.abort();
    });
    // Load the content:
    loadBtn.addEventListener('click', async () => {
        controller = new AbortController();
        const signal = controller.signal;
        // Prevent another click until this fetch is done
        loadBtn.disabled = true;
        abortBtn.disabled = false;
        try {
            // Fetch the content & use the signal for aborting
            const response = await fetch(contentUrl, { signal });
            // Add the content to the page
            output.innerHTML = await response.text();
        }
        catch (err) {
            // Avoid showing an error message if the fetch was aborted
            if (err.name !== 'AbortError') {
                output.textContent = "Oh no! Fetching failed.";
            }
        }
        // These actions happen no matter how the fetch ends
        loadBtn.disabled = false;
        abortBtn.disabled = true;
    });
```

> **注意：**此示例使用[异步函数](https://developers.google.com/web/fundamentals/getting-started/primers/async-functions)。
> 

**One signal, many fetches**

单个信号可用于一次中止多次提取：

```jsx
async function fetchStory({ signal } = {}) {
    const storyResponse = await fetch('/story.json', { signal });
    const data = await storyResponse.json();
  
    const chapterFetches = data.chapterUrls.map(async url => {
      const response = await fetch(url, { signal });
      return response.text();
    });
  
    return Promise.all(chapterFetches);
  }
```

在上面的例子中，相同的信号用于初始提取和并行章节提取。以下是您的使用方法**fetchStory**

```jsx
const controller = new AbortController();
const signal = controller.signal;
fetchStory({ signal }).then(chapters => {
    console.log(chapters);
});
```

在这种情况下，调用controller.abort()将中止正在进行的任何提取。

如果您想返回一个提供响应但也可以中止的对象，您可以创建一个简单的包装器：

```jsx
function abortableFetch(request, opts) {
    const controller = new AbortController();
    const signal = controller.signal;
    return {
        abort: () => controller.abort(),
        ready: fetch(request, { ...opts, signal })
    };
}
```

**axios取消请求**

[文档来源](https://axios-http.com/zh/docs/cancellation)

```jsx
const CancelToken = axios.CancelToken;
const source = CancelToken.source();
axios.get('/user/12345', {
  cancelToken: source.token
}).catch(function (thrown) {
  if (axios.isCancel(thrown)) {
    console.log('Request canceled', thrown.message);
  } else {
    // 处理错误
  }
});
axios.post('/user/12345', {
  name: 'new name'
}, {
  cancelToken: source.token
})
// 取消请求（message 参数是可选的）
source.cancel('Operation canceled by the user.');
```



