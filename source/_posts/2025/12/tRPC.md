---
title: tRPC 简介
tags: [typescript. trpc, deepseek]
date: 2025-12-01 16:47:22
---



**tRPC** 是一个用于构建类型安全 API 的 TypeScript 框架，它让你在客户端和服务器之间调用 API 函数时，就像调用普通的异步函数一样简单，同时享受完整的 TypeScript 类型安全。

## 核心特点

### 1. **端到端类型安全**

- 无需手动定义 API 类型或生成客户端代码
- TypeScript 类型自动从服务器传播到客户端
- 类型错误在编译时就能发现，而不是运行时

### 2. **开发体验极佳**

```typescript
// 服务器端定义
const appRouter = router({
  user: {
    get: procedure
      .input(z.object({ id: z.string() }))
      .query(({ input }) => {
        return db.user.findUnique({ where: { id: input.id } })
      }),
    
    create: procedure
      .input(z.object({ name: z.string(), email: z.string() }))
      .mutation(({ input }) => {
        return db.user.create({ data: input })
      })
  }
})

// 客户端调用 - 完全类型安全！
const user = await trpc.user.get.query({ id: "123" })
const newUser = await trpc.user.create.mutate({ 
  name: "Alice", 
  email: "alice@example.com" 
})
```

### 3. **轻量且灵活**

- 不绑定特定传输协议（支持 HTTP、WebSocket 等）
- 可与现有 Next.js、Express、Fastify 等框架集成
- 中间件支持（认证、日志、缓存等）

## 主要概念

### **路由器 (Router)**

组织 API 端点为层次结构

```typescript
const appRouter = router({
  post: postRouter,
  user: userRouter,
  auth: authRouter
})
```

### **过程 (Procedure)**

定义单个 API 端点：

- **查询 (Query)** - 获取数据（通常 GET）
- **变更 (Mutation)** - 修改数据（通常 POST/PUT/DELETE）
- **订阅 (Subscription)** - 实时更新（WebSocket）

### **中间件**

```typescript
const isAuthed = middleware(({ ctx, next }) => {
  if (!ctx.user) throw new TRPCError({ code: 'UNAUTHORIZED' })
  return next({ ctx: { user: ctx.user } })
})

const protectedProcedure = procedure.use(isAuthed)
```

## 使用场景

### **非常适合**

- **全栈 TypeScript 项目** - 特别是 Next.js + React 应用
- **需要强类型 API 的项目** - 减少接口文档和维护成本
- **内部工具和仪表板** - 快速迭代，类型安全
- **实时应用** - 配合 WebSocket 订阅

### **不太适合**

- 需要公开 REST/GraphQL 接口给第三方
- 非 TypeScript 项目
- 极其简单的 API（可能过度设计）

## 简单示例

```typescript
// 1. 定义路由器 (server)
import { initTRPC } from '@trpc/server'

const t = initTRPC.create()
const router = t.router
const publicProcedure = t.procedure

const appRouter = router({
  hello: publicProcedure
    .input(z.object({ name: z.string() }))
    .query(({ input }) => `Hello ${input.name}!`)
})

// 2. 创建客户端 (client)
import { createTRPCReact } from '@trpc/react-query'

const trpc = createTRPCReact<typeof appRouter>()

// 3. 在 React 组件中使用
function MyComponent() {
  const hello = trpc.hello.useQuery({ name: 'World' })
  
  return <div>{hello.data}</div> // "Hello World!"
}
```

## 生态系统

- **@trpc/server** - 服务器端核心
- **@trpc/client** / **@trpc/react-query** - 客户端集成
- **@trpc/next** - Next.js 适配器
- **@trpc/server/adapters/fastify** - Fastify 适配器

## 优点 vs 缺点

**优点**：

- ✅ 极致开发体验，类型即文档
- ✅ 减少样板代码
- ✅ 自动类型同步
- ✅ 良好的错误处理

**缺点**：

- ❌ 学习曲线（需要理解其概念）
- ❌ TypeScript 强依赖
- ❌ 生态系统相对年轻
- ❌ 不适合多语言后端

## 与类似技术对比

- **REST**：tRPC 更类型安全，开发更快，但 REST 更通用
- **GraphQL**：tRPC 更简单直接，GraphQL 更灵活但复杂
- **gRPC**：tRPC 更适合 Web 前端，gRPC 性能更好但需要 Protocol Buffers

tRPC 特别适合中小型全栈 TypeScript 项目，能显著提升开发效率和代码质量。