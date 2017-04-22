---
title: 라우팅
description: Nuxt.js 는 파일 시스템을 사용하여 당신의 웹 어플리 케이션의 라우팅을 생성합니다.
---

> Nuxt.js 는 `pages` 폴더에 있는 `Vue 파일들의 트리`에 따라 [vue-router](https://github.com/vuejs/vue-router) config 파일을 만듭니다.
<!--> Nuxt.js generates automatically the vue-router configuration according to your file tree of Vue files inside the pages directory.-->

## 정적 라우팅
<!--## Basic Route-->

현재 파일 트리:

```bash
pages/
--| user/
-----| index.vue
-----| one.vue
--| index.vue
```

해당 트리는 아래와 같은 config 파일을 만듭니다.
<!--will automatically generate:-->

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'user',
      path: '/user',
      component: 'pages/user/index.vue'
    },
    {
      name: 'user-one',
      path: '/user/one',
      component: 'pages/user/one.vue'
    }
  ]
}
```

## 동적 라우팅
<!--## Dynamic Routes-->

라우터에 `파라미터`를 정의하고 싶다면, `.vue 파일` 또는 폴더에 **_ 를 접두어**로 사용하시면 됩니다.
<!--To define a dynamic route with a param, you need to define a .vue file OR a directory **prefixed by an underscore**.-->

현재 파일 트리:

```bash
pages/
--| _slug/
-----| comments.vue
-----| index.vue
--| users/
-----| _id.vue
--| index.vue
```

해당 트리는 아래와 같은 config 파일을 만듭니다.
<!--will automatically generate:-->

```js
router: {
  routes: [
    {
      name: 'index',
      path: '/',
      component: 'pages/index.vue'
    },
    {
      name: 'users-id',
      path: '/users/:id?',
      component: 'pages/users/_id.vue'
    },
    {
      name: 'slug',
      path: '/:slug',
      component: 'pages/_slug/index.vue'
    },
    {
      name: 'slug-comments',
      path: '/:slug/comments',
      component: 'pages/_slug/comments.vue'
    }
  ]
}
```

`users-id` 경로에서는 :id 파라미터를 선택적인 옵션으로 만들었습니다. 만약 선택적인 옵션이 아닌, 반드시 필요한 옵션으로 만들어야 하는 경우에는 `index.vue` 파일을 `users/_id` 폴더에 만드세요.
<!--As you can see the route named `users-id` has the path `:id?` which makes it optional, if you want to make it required, create an `index.vue` file in the `users/_id` directory.-->

### 파라미터 유효성 검사
<!--### Validate Route Params-->

Nuxt.js 에서는 validator 메소드를 동적 라우트 컴포넌트에 정의할 수 있습니다.
<!--Nuxt.js lets you define a validator method inside your dynamic route component.-->

<!--In this example: `pages/users/_id.vue`-->
`pages/users/_id.vue` 에 사용되는 예제입니다.

```js
export default {
  validate ({ params }) {
    // Must be a number
    return /^\d+$/.test(params.id)
  }
}
```

만약 validate 메소드가 `true` 를 반환하지 않는다면, Nuxt.js 는 자동으로 404 error page 를 띄웁니다.
<!--If the validate method does not return `true`, Nuxt.js will automatically load the 404 error page.-->

validate method 에 대해 좀 더 알고 싶다면, [API Pages validate](/api/pages-validate) 를 확인해보세요.
<!--More information about the validate method: [API Pages validate](/api/pages-validate)-->

## 중복 라우팅
<!--## Nested Routes-->

Nuext.js 에서는 vue-router 를 사용하여, 중복되는 라우팅을 만들 수 있습니다.
<!--Nuxt.js lets you create nested route by using the children routes of vue-router.-->

중복된 라우팅에 기본 컴포넌트를 정의하려면, `하위 컴포넌트`를 가지는 라우팅 폴더의 이름과 같은 Vue 파일이 필요합니다.
<!--To define the parent component of a nested route, you need to create a Vue file with the **same name as the directory** which contain your children views.-->

<p class="Alert Alert--info">부모 컴포넌트의 .vue 파일에 `<nuxt-child/>` 를 **반드시** 작성해야 합니다.</p>
<!--<p class="Alert Alert--info">Don't forget to write `<nuxt-child/>` inside the parent component (.vue file).</p>-->

현재 파일 트리:
<!--This file tree:-->

```bash
pages/
--| users/
-----| _id.vue
-----| index.vue
--| users.vue
```

해당 트리는 아래와 같은 config 파일을 만듭니다.
<!--will automatically generate:-->

```js
router: {
  routes: [
    {
      path: '/users',
      component: 'pages/users.vue',
      children: [
        {
          path: '',
          component: 'pages/users/index.vue',
          name: 'users'
        },
        {
          path: ':id',
          component: 'pages/users/_id.vue',
          name: 'users-id'
        }
      ]
    }
  ]
}
```

## 동적 중복 라우팅
<!--## Dynamic Nested Routes-->


Nuxt.js 에서는 동적 라우팅 내에서 동적 라우팅을 가능하게 합니다. 하지만 이런 라우팅은 자주 사용되면 안 됩니다.
<!--This scenario should not often append, but it is possible with Nuxt.js: having dynamic children inside dynamic parents.-->

해당 파일 트리:
<!--This file tree:-->

```bash
pages/
--| _category/
-----| _subCategory/
--------| _id.vue
--------| index.vue
-----| _subCategory.vue
-----| index.vue
--| _category.vue
--| index.vue
```

해당 트리는 아래와 같은 config 파일을 만듭니다.
<!--will automatically generate:-->

```js
router: {
  routes: [
    {
      path: '/',
      component: 'pages/index.vue',
      name: 'index'
    },
    {
      path: '/:category',
      component: 'pages/_category.vue',
      children: [
        {
          path: '',
          component: 'pages/_category/index.vue',
          name: 'category'
        },
        {
          path: ':subCategory',
          component: 'pages/_category/_subCategory.vue',
          children: [
            {
              path: '',
              component: 'pages/_category/_subCategory/index.vue',
              name: 'category-subCategory'
            },
            {
              path: ':id',
              component: 'pages/_category/_subCategory/_id.vue',
              name: 'category-subCategory-id'
            }
          ]
        }
      ]
    }
  ]
}
```

## 트랜지션
<!--## Transitions-->

Nuxt.js 에서는 [&lt;transtion&gt;](http://vuejs.org/v2/guide/transitions.html#Transitioning-Single-Elements-Components) 를 사용하여, 라우팅 간에 놀랍도록 멋진 transitions/animations 를 만들 수 있습니다.
<!--Nuxt.js uses the  [&lt;transition&gt;](http://vuejs.org/v2/guide/transitions.html#Transitioning-Single-Elements-Components) component to let you create amazing transitions/animations between your routes.-->

### 전역 설정
<!--###  Global Settings-->

<p class="Alert Alert--info">Nuxt.js 의 기본 트랜지션 이름은 `"page"` 입니다.</p>
<!--<p class="Alert Alert--info">Nuxt.js default transition name is `"page"`.</p>-->

`fade` 트랜지션을 모든 페이지에 추가하고 싶다면, 모든 라우팅에 공유되는 css 파일이 필요하므로, `assets` 폴더를 만들어야 합니다.
<!--To add a fade transition to every page of your application, we need a CSS file that is shared across all our routes, so we start by creating a file in the `assets` folder.-->

<!--Our global css in `assets/main.css`:-->
이것은 `assets/main.css` 의 내용입니다.

```css
.page-enter-active, .page-leave-active {
  transition: opacity .5s;
}
.page-enter, .page-leave-to {
  opacity: 0;
}
```

nuxt.config.js 파일에 원하는 css 의 path 를 추가할 수 있습니다.
<!--We add its path in our `nuxt.config.js` file:-->
```js
module.exports = {
  css: [
    'assets/main.css'
  ]
}
```

트랜지션 key 에 대해 좀 더 알고 싶다면, [API Configuration transition](/api/pages-transition) 를 확인해보세요.

### 페이지 설정
<!--### Page Settings-->

커스텀 트랜지션을 하나의 페이지에 설정할 수도 있습니다.
<!--You can also define a custom transition for only one page with the `transition` property.-->

`assets/main.css` 에 새로운 css 클래스를 만듭니다.
<!--We add a new class in our global css in `assets/main.css`:-->

```css
.test-enter-active, .test-leave-active {
  transition: opacity .5s;
}
.test-enter, .test-leave-active {
  opacity: 0;
}
```

그런 다음, 페이지에 `transition` 프로퍼티를 사용하여 정의합니다.
<!--then, we use the transition property to define the class name to use for this page transition:-->
```js
export default {
  transition: 'test'
}
```

트랜지션 property 에 대해 좀 더 알고 싶다면, [API Pages transition](/api/pages-transition) 를 확인해보세요.

## 미들웨어
<!--## Middleware-->

> 미들웨어는 커스텀 함수를 페이지 또는 그룹을 렌더링하기 전에 실행시킵니다.
<!--> The middleware lets you define custom function to be ran before rendering a page or a group of pages.-->

**모든 미들웨어는 `middleware` 폴더에 위치해야 합니다.** 파일이름은 곧 미들웨어의 이름이 됩니다. (`middleware/auth.js` 는 `auth` 로 사용됩니다.)
<!--**Every middleware should be placed in the `middleware/` directory.** The filename will be the name of the middleware (`middleware/auth.js` will be the `auth` middleware).-->

미들웨어는 [the context](/api#context) 를 첫 번째 매개변수로 받습니다.
<!--A middleware receive [the context](/api#context) as first argument:-->

```js
export default function (context) {
  context.userAgent = context.isServer ? context.req.headers['user-agent'] : navigator.userAgent
}
```

미들웨어는 아래와 같은 작동을 합니다.

1. nuxt.config.js
2. 매칭되는 레이아웃을 찾습니다.
3. 매칭되는 페이지를 찾습니다.
<!--The middleware will be executed in series in this order:-->
<!--1. `nuxt.config.js`
2. Matched layouts
3. Matched pages-->

미들웨어가 비동기일 경우, `Promise` 를 반환하거나, 두 번째 callback 매개변수를 사용하면 됩니다.
<!--A middleware can be asynchronous, simply return a `Promise` or use the 2nd `callback` argument:-->

`middleware/stats.js`
```js
import axios from 'axios'

export default function ({ route }) {
  return axios.post('http://my-stats-api.com', {
    url: route.fullPath
  })
}
```

그런 다음, `nuxt.config.js`, 레이아웃 또는 페이지에 `middleware` 키를 사용하면 됩니다.

`nuxt.config.js`
```js
module.exports = {
  router: {
    middleware: 'stats'
  }  
}
```

`stats` 미들웨어는 라우팅 될 때 호출됩니다.
<!--The `stats` middleware will be called for every route changes.-->

미들웨어가 사용된 실제 예제를 보고싶다면, [example-auth0](https://github.com/nuxt/example-auth0) 를 확인해보세요.
<!--To see a real-life example using the middleware, please see [example-auth0](https://github.com/nuxt/example-auth0) on GitHub.-->
