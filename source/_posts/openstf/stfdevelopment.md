---
title: openstf免登录
tag: open-stf
---
openstf已经很流行了，有个需求，需要把openstf介入到现有系统中，直接跳转到设备管理页面， 话不多说，直接上代码

### 以下为stf源码中mock登录
```
app.post('/auth/api/v1/mock', function(req, res) {
    var log = logger.createLogger('auth-mock')
    log.setLocalIdentifier(req.ip)
    switch (req.accepts(['json'])) {
      case 'json':
        requtil.validate(req, function() {
            req.checkBody('name').notEmpty()
            req.checkBody('email').isEmail()
          })
          .then(function() {
            log.info('Authenticated "%s"', req.body.email)
            var token = jwtutil.encode({
              payload: {
                email: req.body.email
              , name: req.body.name
              }
            , secret: options.secret
            , header: {
                exp: Date.now() + 24 * 3600
              }
            })

            log.info('stf login token: ', urlutil.addParams(options.appUrl, {
              jwt: token
            }))
            res.status(200)
              .json({
                success: true
              , redirect: urlutil.addParams(options.appUrl, {
                  jwt: token
                })
              })
          })
          .catch(requtil.ValidationError, function(err) {
            res.status(400)
              .json({
                success: false
              , error: 'ValidationError'
              , validationErrors: err.errors
              })
          })
          .catch(function(err) {
            log.error('Unexpected error', err.stack)
            res.status(500)
              .json({
                success: false
              , error: 'ServerError'
              })
          })
        break
      default:
        res.send(406)
        break
    }
  })

```
1. 重点看一下 redirect: urlutil.addParams(options.appUrl, {jwt: token})这段代码，意思是生成的token为jwt入参，跳转到设备管理页面，大家可以以日志的形式，将urlutil.addParams(options.appUrl, {jwt: token})打印出来，复制到浏览器直接访问，即可进到设备管理页面
2. 有了上面的知识点，于是继续看下面的代码
```
app.get('/auth/api/v1/url', function(req, res) {
    var log = logger.createLogger('auth-api-url')
    log.setLocalIdentifier(req.ip)
    var userName = req.query.username
    log.info('传入的username: ' + userName)
    if(userName) {
      var token = jwtutil.encode({
        payload: {
          email: userName + '@zbj.com'
          , name: userName
        }
        , secret: options.secret
        , header: {
          exp: Date.now() + 24 * 3600
        }
      })
      log.info('生成的token ' + token)
      var respStr = urlutil.addParams(options.appUrl, {
        jwt: token
      })
      log.warn('返回的登录地址 ' + respStr)
      // 渲染列表页面，支持跨域
      res.header('Access-Control-Allow-Origin', '*')
      res.jsonp({url: respStr})
    }
   else {
    res.status(400)
      .json({
        success: false
        , error: 'ValidationError'
        , validationErrors: err.errors
      })
      }
  })
```
只需要输入一个username就可以登录系统，如果用户不存在，openstf会自动添加一条用户记录，usermail这里是直接拼接的，也可以直接转入usermail,username