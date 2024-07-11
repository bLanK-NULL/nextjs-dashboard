# 目录结构

- **`/app`**：包含应用程序的所有路由、组件和逻辑，这是您主要使用的地方。
  - `/app/page.tsx`：用于导出React组件，并且需要它才能访问路由，访问page文件可以默认被读取，只有page可以被访问。
  - `layout.tsx`： 会接收`page.tsx`作为children
  - **`/app/lib`**：包含应用程序中使用的函数，例如可重用实用程序函数和数据获取函数。
  - **`/app/ui`**：包含应用程序的所有 UI 组件，例如卡片、表格和窗体。为了节省时间，我们为您预先设置了这些组件的样式。
  - 里面的`global.css`是总样式
  - `/app/seed/route.ts` 是数据库种子

- **`/public`**：包含应用程序的所有静态资产，例如图像。



Layout.tsx 是布局组件（根布局是必须的，而其他可以省略），可以包裹Page.tsx ，而且路由切换时布局组件内容不会更新。

​	



##路由

next有两种路由

* app router
  * 较新的路由器，允许您使用 React 的最新功能，例如服务器组件和流式处理。
* pages router
  * 原始的 Next.js 路由器，它允许您构建服务器渲染的 React 应用程序，并继续支持较旧的 Next.js 应用程序。



# 样式

* 全局样式
* css模块化和 Tailwind
* clsx





# 内置组件

## Image

使用Image组件的src可以以public目录为静态资源为根目录。



## Link

防止a标签导致整个页面刷新

```tsx
import Link from 'next/link'
```



为了改善导航体验，Next.js 会自动按路由段对应用程序进行代码拆分。这与传统的 React 不同[SPA](https://developer.mozilla.org/en-US/docs/Glossary/SPA)，浏览器在初始加载时加载所有应用程序代码。

按路由拆分代码意味着页面变得孤立。如果某个页面抛出错误，应用程序的其余部分仍将正常工作。

此外，在生产环境中，每当 [Link](https://nextjs.org/docs/api-reference/next/link) 组件出现在浏览器的视口中时，Next.js 都会自动在后台**预取**链接路由的代码。当用户单击链接时，目标页面的代码已经加载到后台，这就是使页面转换近乎即时的原因！





# 客户端

把组件变成客户端组件,从当前节点开始以后的所有节点都是client 组件

```tsx
'use client'
```



# 路由

## 路由处理程序

使用 Web 为给定路由创建自定义请求处理程序[请求](https://developer.mozilla.org/docs/Web/API/Request)和[响应](https://developer.mozilla.org/docs/Web/API/Response)

**约定**

在目录路径下添加 `route.ts`

```ts
export const dynamic = 'force-dynamic' // defaults to auto
export async function GET(request: Request) {}
```

> 注意： 在有page.tsx的同一目录下不能有route.ts



## 动态路由

### params

1. 设置文件目录结构为

   ```
   app/items/[slug]/route.js
   
   访问 /items/a  =>   得到params { slug: 'a' }
   ```

2. 

```typescript
export async function GET(
  request: Request,
  { params }: { params: { slug: string } }
) {
  const slug = params.slug // 'a', 'b', or 'c'
}
```

### searchParams

使用`request.nextUrl.searchParams`

```typescript
export function GET(request: NextRequest) {
  const searchParams = request.nextUrl.searchParams
  const query = searchParams.get('query')
  // query is "hello" for /api/search?query=hello
}
```

## 平行路由
在目录下创建`@folder1/page.tsx`和`@folder2/page.tsx`以创建两个平行页面
他们会以props的形式传递给`layout.tsx` 
```
 /app
----> /@folder1
-------->  page.tsx
----> /@folder2
--------> page.tsx
----> layout.tsx
----> page.tsx 
```
```tsx
export default Layout({
  children,
  folder1,
  folder2
}){
  
}
```


## 响应

在全局上下文自带`Response`类

**用法1** `Response.json()`

```typescript
export async function POST(request: Request) {
  const res = await request.json()
  return Response.json({ res })
}
```

**用法2** `new Response()`

```typescript
export async function GET(request: Request) {
  return new Response('Hello, Next.js!', {
    status: 200,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Methods': 'GET, POST, PUT, DELETE, OPTIONS',
      'Access-Control-Allow-Headers': 'Content-Type, Authorization',
    },
  })
}
```





## 数据层

如果使用服务器组件，那么可以直接向查询数据库。



# 静态渲染

使用静态渲染，则

1. 数据提取和呈现或revalidate 在服务器上进行
2. 每当用户访问您的应用程序时，都会提供缓存的结果



# 动态渲染

使用动态呈现时，在**请求时**在服务器上为每个用户呈现内容

