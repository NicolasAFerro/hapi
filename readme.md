# 📘 Guia Rápido — Hapi Framework

## 🧠 Visão Geral

O **Hapi** é um framework Node.js focado em:

* configuração declarativa
* modularização via plugins
* validação robusta
* controle explícito do ciclo de vida da request

Diferente de frameworks como Express ou NestJS, ele **não é baseado em middlewares**, mas sim em um **lifecycle bem definido**.

---

# 🔁 Request Lifecycle (essencial entender isso)

Fluxo da requisição:

```
Request chega
  ↓
onRequest
  ↓
onPreAuth
  ↓
Auth (JWT, etc)
  ↓
onPostAuth
  ↓
Validação (Joi)
  ↓
onPreHandler
  ↓
Handler (controller)
  ↓
onPreResponse
  ↓
Response sai
```

## 📍 Onde usar cada etapa

* **onRequest** → manipulação inicial (headers, logs)
* **onPreAuth** → antes da autenticação
* **Auth** → JWT e strategies
* **onPostAuth** → lógica após autenticação
* **Validação** → automática via Joi
* **onPreHandler** → lógica antes do controller
* **Handler** → controller (regra de negócio)
* **onPreResponse** → interceptação de resposta / erros

---

# 🔌 Plugins (conceito central)

Tudo no Hapi gira em torno de plugins.

## Exemplo:

```ts
export const plugin = {
  name: 'meu-plugin',
  version: '1.0.0',
  register: async (server, options) => {
    server.route({
      method: 'GET',
      path: '/',
      handler: () => 'ok'
    });
  }
};
```

Uso:

```ts
await server.register(meuPlugin);
```

## O que plugins podem fazer?

* registrar rotas
* configurar autenticação
* usar lifecycle hooks
* adicionar funcionalidades ao servidor

---

# 📦 Modules vs Plugins

## Plugin

* conceito do Hapi
* usado com `server.register`
* integra com lifecycle

## Module

* qualquer pacote npm do ecossistema
* pode ou não ser plugin

👉 Regra:

```
Todo plugin é um module
Nem todo module é um plugin
```

---

# 📦 Principais dependências encontradas

## Boom

* padronização de erros HTTP

```ts
throw Boom.notFound('User not found');
```

---

## Joi

* validação de payload, query e params

```ts
validate: {
  payload: Joi.object({
    name: Joi.string().required()
  })
}
```

---

## Inert

* servir arquivos estáticos

---

## Vision

* renderização de templates (SSR)

---

## uuid

* geração de IDs únicos

---

# 🛣️ Rotas no Hapi

Cada método é uma rota separada:

```ts
server.route([
  {
    method: 'GET',
    path: '/orders',
    handler: getOrders
  },
  {
    method: 'POST',
    path: '/orders',
    handler: createOrder
  }
]);
```

---

## 📁 Prefixo de rotas (simulando nested routes)

```ts
await server.register({
  plugin: ordersRoutes,
  routes: {
    prefix: '/orders'
  }
});
```

---

# 🔐 Autenticação JWT

Configuração via strategy:

```ts
await server.register(require('@hapi/jwt'));

server.auth.strategy('jwt', 'jwt', {
  keys: 'secret',
  validate: async (artifacts) => ({
    isValid: true,
    credentials: artifacts.decoded.payload
  })
});

server.auth.default('jwt');
```

---

# 📥 Acesso a dados da request

```ts
handler: (request, h) => {
  const body = request.payload;
  const query = request.query;
  const params = request.params;

  return { body, query, params };
}
```

---

# ⚙️ TypeScript no Hapi

Instalação:

```
npm install typescript ts-node-dev @types/node -D
```

Script recomendado:

```json
"scripts": {
  "dev": "ts-node-dev --respawn --transpile-only src/server.ts"
}
```

---

# 🔁 Hot Reload (nodemon equivalente)

Hapi não tem hot reload nativo.

## Opções:

### ts-node-dev (recomendado)

* mais rápido
* melhor com TypeScript

---

### nodemon

* opção clássica

---

### node --watch

* nativo do Node (requer build)

---

# 🧱 Estrutura comum de projeto

```
src/
  plugins/
  modules/
    orders/
      routes.ts
      handler.ts
      service.ts
  server.ts
```

---

# ⚖️ Comparação com outros frameworks

| Conceito   | Express    | NestJS     | Hapi              |
| ---------- | ---------- | ---------- | ----------------- |
| Middleware | Sim        | Indireto   | Lifecycle hooks   |
| Estrutura  | Livre      | Opinionada | Modular (plugins) |
| Validação  | Manual     | DTO/Pipes  | Joi               |
| Auth       | Middleware | Guards     | Strategies        |
| CLI        | Não        | Sim        | Não               |

---

# 🧠 Insight Final

O Hapi é baseado em:

* **plugins (extensibilidade)**
* **lifecycle (controle de fluxo)**
* **configuração declarativa**

👉 Ele troca “liberdade total” (Express) por **padronização e previsibilidade**,
mas sem impor uma arquitetura rígida como o NestJS.

---

# 🚀 O que focar para dominar rápido

1. `server.route`
2. `options.validate (Joi)`
3. `auth.strategy`
4. `server.ext (lifecycle hooks)`

---
