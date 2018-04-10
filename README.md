# Oompsh

#### A Node.js server which adds realtime push notification to Oom apps

+ __Last update:__  2018/04/09
+ __Version:__      0.1.7

[Homepage](http://oompsh.loop.coop/) &nbsp;
[Repo](https://github.com/loopdotcoop/oompsh) &nbsp;
[NPM](https://www.npmjs.com/package/oompsh) &nbsp;
[Changelog](http://oompsh.loop.coop/CHANGELOG) &nbsp;
[Test @TODO](http://oompsh.loop.coop/support/test.html)

Oom &nbsp;
🔅 &nbsp;
Realtime &nbsp;
🌟 &nbsp;
Push &nbsp;
🎉 &nbsp;
Server-Sent Events




## Author

Designed, developed and authored by Rich Plastow for Loop.Coop.

+ __Website:__
  [richplastow.com](http://richplastow.com/) &nbsp;
  [loop.coop](https://loop.coop/)
+ __GitHub:__
  [richplastow](https://github.com/richplastow) &nbsp;
  [loopdotcoop](https://github.com/loopdotcoop)
+ __Twitter:__
  [@richplastow](https://twitter.com/richplastow) &nbsp;
  [@loopdotcoop](https://twitter.com/loopdotcoop)
+ __Location:__
  Brighton, UK and Himachal Pradesh, India




## App

+ __Locales:__
  - English
+ __Software:__
  - Atom
  - Git
+ __Languages:__
  - HTML5
  - CSS3
  - JavaScript ES6
+ __Dependencies:__
  - None!




<!-- BEGIN config-builder.js -->
## Namespace

The global namespace is `window.OOMPSH` in a browser, or `global.OOMPSH` in the
Node.js server.

```js
ROOT.OOMPSH = {}
```



## Configuration

```js
ROOT.OOMPSH.configuration = {

    VERSION: '0.1.7' // the major part of this is also the API version, `APIV`
  , get APIV () { return 'v' + ROOT.OOMPSH.configuration.VERSION.split('.')[0] }

    //// Used as one of the default `domain` values in the frontend UI.
  , remoteURL: 'https://oompsh.herokuapp.com'

    //// Validators for the first character of a username or password, and for
    //// subsequent characters.
  , c1: '[a-zA-Z0-9!()_`~@\'"^]' // not allowed to start with . or -
  , c2: '[a-zA-Z0-9!()_`~@\'"^.-]'

    //// Initial values for the server’s ‘very very verbose’ and ‘very verbose’.
  , vvv: false // very very verbose logging
  , vv:  true  // very verbose logging (must be true if `vvv` is true)

}
```




## API

```js
const OOMPSH = ROOT.OOMPSH
const api = ROOT.OOMPSH.api = {
    enduser: {
        GET:  {
            version: {}
          , connect: {}
        }
      , POST: { }
    }
  , admin: { // admins can do everything endusers can do, plus...
        GET:  { }
      , POST: {
            'soft-disconnect': { sample:{ target:'all' } }
          , 'hard-disconnect': { sample:{ target:'all' } }
          , notify:            { sample:{ target:'all', body:'{"message":"Hello Ooms"}' } }
        }
    }
  , status: { // the server responds with one of these HTTP status codes:
        200: 'OK'
      , 401: 'UNAUTHORIZED' // nb, we don’t send a 'WWW-Authenticate' header
      , 404: 'NOT FOUND'
      , 405: 'METHOD NOT ALLOWED'
      , 406: 'NOT ACCEPTABLE'
      // , 500: 'INTERNAL SERVER ERROR'
      , 501: 'NOT IMPLEMENTED'
    }
}

//// Duplicate all enduser actions to admin.
api.admin.GET  = Object.assign(api.admin.GET, api.enduser.GET)
api.admin.POST = Object.assign(api.admin.POST, api.enduser.POST)
```




## Validators

```js
const OOMPSH = ROOT.OOMPSH
    , { c1, c2 } = OOMPSH.configuration
    , { admin, enduser } = OOMPSH.api

OOMPSH.valid = {
    local:  /^https?:\/\/(127\.0\.0\.1|localhost)/
  , domain: /^https?:\/\/[-:.a-z0-9]+\/?$/

    //// Username, password and credentials.
  , user:  new RegExp(`^(${c1}${c2}{0,64})$`)
  , pass:  new RegExp(`^(${c1}${c2}{0,64})$`)
  , creds: new RegExp(`^${c1}${c2}{0,64}:${c1}${c2}{0,64}$`)

  , apiv:   /^v\d+$/
  , domain: /^https?:\/\/[-:.a-z0-9]+\/?$/
  , method: /^(GET|POST|OPTIONS)$/
  , action: new RegExp( `^(`
      + Object.keys(admin.GET).join('|') + '|'
      + Object.keys(admin.POST).join('|') + '|'
      + Object.keys(enduser.GET).join('|') + '|'
      + Object.keys(enduser.POST).join('|')
      + `)$` )
  , target: /^.+$/ //@TODO
  , body:   /^.+$/ //@TODO
}
```
<!-- END config-builder.js -->




## Heroku

Once Heroku is set up, you can check for current environment (config) variables:  
`$ heroku run printenv`  

You’ll need to choose an admin username and password, and record it in the
`OOMPSH_ADMIN_CREDENTIALS` config var:  
`$ heroku config:set OOMPSH_ADMIN_CREDENTIALS=<admin-username>:<admin-password>`

You can define any number of admin credentials this way, eg:  
`$ heroku config:set OOMPSH_ADMIN_CREDENTIALS=jo:pw,sam:pass,pat:foobar`  
Note that oompsh.js throws an error on startup if any credentials are invalid.

After each `$ git commit` and `$ git push`, you should redeploy the app:  
`$ git push heroku master`
