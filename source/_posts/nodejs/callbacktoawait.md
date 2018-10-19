---
title: 将callback转化为async/await方式
date: 2018-10-9
tag: nodejs
---

> 参考 https://www.jianshu.com/p/e5bdf60cda15

nodejs异步模块已经过度到async/await时期

最近在使用七牛nodejs sdk上传文件，但官方提供还是callback方式，在koa2里面不是很好用，搜索了一番，在此记录。

```
var formUploader = new qiniu.form_up.FormUploader(config);
var putExtra = new qiniu.form_up.PutExtra();
var key='test.txt';
formUploader.put(uploadToken, key, "hello world", putExtra, function(respErr,
  respBody, respInfo) {
  if (respErr) {
    throw respErr;
  }
  if (respInfo.statusCode == 200) {
    console.log(respBody);
  } else {
    console.log(respInfo.statusCode);
    console.log(respBody);
  }
});
```

```
const stream_uploader = (token, key, readableStream) => {
  return new Promise((resolve, reject) => {
    formUploader.putStream(token, key, readableStream, putExtra, (res_error, res_body, res_info) => {
      if (res_error) {
        reject(res_error);
      }
    
      if (res_info.statusCode == 200) {
        reslove(res_body);
      } else {
        reject({
          code: res_info.statusCode,
          body: res_body,
        });
      }
    });
  });
}

const main = async () => {
  // ... 获取token、key业务逻辑

  // 将obj，转化为 readableStream 流
  const readableStream = await intoStream(JSON.stringify(obj));

  try {
    const result = await stream_uploader(token, key, readableStream, putExtra);  
  } catch (error) {
    // 处理异常
  }
};

main();

```

> 参考链接

- [converting-geolocation-from-callbacks-into-async-await-javascript](https://steemit.com/programming/@leighhalliday/converting-geolocation-from-callbacks-into-async-await-javascript)
- [how-to-convert-callback-to-promise](https://75team.com/post/how-to-convert-callback-to-promise.html)
- [How do I convert an existing callback API to promises?](https://stackoverflow.com/questions/22519784/how-do-i-convert-an-existing-callback-api-to-promises)
- [JavaScript — from callbacks to async/await](https://medium.freecodecamp.org/javascript-from-callbacks-to-async-await-1cc090ddad99)
- [async 函数的含义和用法](http://www.ruanyifeng.com/blog/2015/05/async.html)