---
layout: post
title:  "Next.js with Express.js and GraphQL"
---

# Setting up a monolith with Next.js as frontend with Express.js and GraphQL as backend
---
> *Disclaimer*:
- This whole setup was done in about a few hours on day-1 for a new project. The aim was to have a monolith up and running as soon as possible without ignoring the high-level separation of concerns and intuitive folder structure. Other architectural concerns definitely took a back stage
- It is **not** a step by step guide for you to follow in order to create a similar setup
- The intention here is to highlight the small challenges I encountered while setting up this combination and how I slowly moved towards a better folder structure.

I was starting a new side project, [Predeect](https://predeect.com), that would allow people with scientific knowledge about any topic to make forecasts (informed predictions) and earn credibility in the respective field. For example, somebody with a good understanding about weather science, can publish their forecasts and build a reputation on the way as the forecasts get validated in time. Other visitors can either consume these future predictions to satisfy their curiosity, or ask interesting questions about the future that would attract forecasters.

This tool would have quite a few public facing pages with dynamic content. For SSR and SSG, I chose Next.js. But I was also aware of [the risk of choosing Next.js for the wrong reasons](https://blog.webf.zone/you-dont-need-next-js-and-ssr-7c6bd27e78d8), and wanted to see if there is an option to merge it with an appropriate backend stack. After exploring a few options, I settled with Express.js and GraphQL.

### Step 1: Project initiation with Next.js

- From inside the project folder `npx create-next-app@latest .`
- Opted to use the Pages router instead of the App router by creating `pages/index.tsx`
  ```tsx
  export default function Page() {
    return <h1>Hello, Next.js!</h1>
  }
  ```

### Step 2: Introducing Express.js

- `npm install express`
- Created a `server.ts` file at the root, and
  ```typescript
  ..
  ..
  const dev = process.env.NODE_ENV !== 'production';
  const app = next({ dev });
  ..
  ..
  app.prepare().then(() => {
    const server = express();

    server.get('/api/something', (req, res) => {
  ..
  ..
  ```
- Set Express.js to handle the dynamic root handling
  ```typescript
  server.get('/collection/:id', (req, res) => {
    const actualPage = '/collection/[id]';
    const queryParams = { id: req.params.id };

    app.render(req, res, actualPage, queryParams);
  });
  ```
- Updated the `scripts` in `package.json`
  ```json
  "scripts": {
    "dev": "node server.js",
    "build": "next build",
    "start": "NODE_ENV=production node server.js"
  }
  ```

At that time, the folder structure approximately looked like this:
  ```bash
  /project_root
  ├── /src
      ├── /pages      # Page Router
      ├── /components # Shared components
      ├── /public     # Static files served by Next.js
  ├── /node_modules
  ├── package.json
  ├── server.js       # Custom Next.js server handling Express.js
  └── ...
  ```

Unfortunately, I missed an obvious problem with this. Although, it worked fine on local with `npm run dev` and did run both Next and Express servers, when I deployed the app on Vercel, the frontend came up fine but the Express.js was completely ignored. Vercel doesn’t support custom Node.js servers like Express.js directly in the same way it does on local environments. Vercel is designed to work with serverless functions, and therefore, it won't run a custom server.js as it would locally.

While I fixed this, I also wanted to introduce GraphQL.

### Step 3: Vercel fix and adding GraphQL

- Removed `server.ts`
- Installed `graphql`
  ```bash
  npm install graphql express-graphql graphql-tag
  npm install @graphql-tools/schema @graphql-tools/merge
  npm install --save-dev @types/node @types/express @types/graphql
  ```
- Reverted the `package.json` changes
  ```json
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start"
  }
  ```
- Created a helper function to allow Next.js to run Express middleware within API routes. This is necessary because Next.js API routes don’t natively support Express middleware out of the box.
  ```typescript
  // lib/runMiddleware.js

  export function runMiddleware(req, res, fn) {
    return new Promise((resolve, reject) => {
      fn(req, res, (result) => {
        if (result instanceof Error) {
          return reject(result);
        }

        return resolve(result);
      });
    });
  }
  ```
- Moved setting up Express.js to a Vercel serverless function by adding `/pages/api/index.ts` to handle the GraphQL
  ```typescript
  import { buildSchema } from 'graphql';
  import { graphqlHTTP } from 'express-graphql';
  import express from 'express';
  import { runMiddleware } from '../../../lib/runMiddleware';

  const app = express(); // Express instance

  // GraphQL schema
  const schema = buildSchema(`
    type Query {
      hello: String
    }
  `);

  // GraphQL root resolver
  const root = {
    hello: () => 'Hello from GraphQL!',
  };

  app.use(
    '/api/graphql',
    graphqlHTTP({
      schema: schema,
      rootValue: root,
      graphiql: process.env.NODE_ENV === 'development'
    }))
  );

  // Middleware wrapper for serverless function execution
  export default async function handler(req, res) {
    await runMiddleware(req, res, app);
  }

  export const config = {
    api: {
      bodyParser: false,
    },
  };
  ```

Now, the Vercel deployment worked fine and `https://www.predeect.com/api` responded as expected. The folder structure somewhat looked like this:
```bash
/project_root
  ├── /src
      ├── /pages          # Page Router files
          ├── /api
              ├── index.ts
      ├── /public         # Static files
  ├── /lib
      ├── runMiddleware.js # Helper to run Express as middleware
  ├── /node_modules
  ├── package.json
  └── ...
```

But more problems. The GraphQL root resolver being inside `/pages/api/index.ts`, wasn't really scalable. So, I wanted to structure  and modularize the GraphQL resolvers and schema for better separation of concerns. This would mean separating schemas, resolvers, and business logic into different modules and keeping API route files lightweight, focusing mainly on importing the required resolvers and schemas.

### Step 4: Organizing GraphQL resolvers and schema

- Added a dedicated GraphQL router under `/src/graphql/index.ts`:
  ```ts
  import { makeExecutableSchema } from '@graphql-tools/schema';
  import { mergeResolvers, mergeTypeDefs } from '@graphql-tools/merge';
  import { userTypeDefs } from './schemas/userSchema';
  import { postTypeDefs } from './schemas/postSchema';
  import { userResolvers } from './resolvers/userResolver';
  import { postResolvers } from './resolvers/postResolver';
  import { createContext } from './context';

  // Merge the type definitions and resolvers
  const typeDefs = mergeTypeDefs([userTypeDefs, postTypeDefs]);
  const resolvers = mergeResolvers([userResolvers, postResolvers]);

  // Create the executable schema
  export const schema = makeExecutableSchema({
    typeDefs,
    resolvers,
  });

  // Context to be passed to each request
  export const context = createContext();
  ```
- Updated the Next API route to ensure correct context handling
  ```tsx
  // src/pages/api/index.tsx

  import { schema, context } from '../../graphql';
  import { graphqlHTTP } from 'express-graphql';
  import express from 'express';
  import { NextApiRequest, NextApiResponse } from 'next';
  import { runMiddleware } from '../../../lib/runMiddleware';

  const app = express();

  // GraphQL middleware with context
  app.use(
    '/api',
    graphqlHTTP({
      schema,
      context,
      graphiql: process.env.NODE_ENV === 'development',
    })
  );

  // Middleware wrapper for running Express within the Next.js API route
  export default async function handler(req: NextApiRequest, res: NextApiResponse) {
    await runMiddleware(req, res, app);
  }

  export const config = {
    api: {
      bodyParser: false, // Disable body parser to allow GraphQL to handle it
    },
  };
  ```
- The `/lib` folder moved in parallel with `/src` under the project root, and a new `/datasources` folder added to define the data sources.
- Data Sources: Created `userAPI.ts` and `postAPI.ts` to manage user and post-related data.
- GraphQL Context: The `createContext()` function now includes `userAPI` and `postAPI`
  ```ts
  // /src/graphql/context.ts

  import { UserAPI } from '../../datasources/userAPI';
  import { PostAPI } from '../../datasources/postAPI';

  export interface GraphQLContext {
    dataSources: {
      userAPI: UserAPI;
      postAPI: PostAPI;
    };
  }

  export const createContext = (): GraphQLContext => {
    return {
      dataSources: {
        userAPI: new UserAPI(),
        postAPI: new PostAPI(),
      },
    };
  };
  ```
- The reorganized folder structure:
  ```bash
  /project_root
    ├── /src
        ├── /graphql
            ├── /resolvers         # All resolver logic
                ├── userResolver.ts
                ├── postResolver.ts
            ├── /schemas           # GraphQL schemas (by feature)
                ├── userSchema.ts
                ├── postSchema.ts
            ├── /types.ts          # Type definitions
            ├── /context.ts        # GraphQL context definitions
            ├── index.ts           # Combines schemas and resolvers into a single executable schema
        ├── /pages
            ├── /api
                ├── index.tsx      # API route for GraphQL
    ├── /lib
        ├── runMiddleware.ts       # Helper to run middleware in Next.js API routes
    ├── /datasources
        ├── userAPI.ts             # Datasource logic for user-related data
        ├── postAPI.ts             # Datasource logic for post-related data
    ├── package.json
    ├── tsconfig.json
    └── ...
  ```
- For a minute, I thought about my choice to keep the `/lib` and '/datasources' outside `/src`. It had its pros and cons. But finally I decided to keep everything related to the application inside `/src` for consistency and organization, as the otherwise possible modularization for "reuse" across projects wasn't a good reason yet.
  ```bash
  /project_root
  ├── /src
      ├── /graphql
          ├── /resolvers
          ├── /schemas
          ├── /types.ts
          ├── /context.ts
          ├── index.ts
      ├── /pages
          ├── /api
              ├── index.tsx
      ├── /datasources       # Moved inside src
          ├── userAPI.ts
          ├── postAPI.ts
      ├── /lib               # Moved inside src
          ├── runMiddleware.ts
  ├── /public
  ├── package.json
  ├── tsconfig.json
  ```

Next question that popped up was - *what about the clear separation between the frontend and backend code?*

### Step 5: Separating frontend/backend

- It was a bit silly to try the following structure:
  ```bash
  /project_root
  ├── /src
      ├── /frontend
          ├── /components      # Moved layout.tsx here
          ├── /pages           # Next.js pages for the frontend
          ├── /styles          # Moved globals.css here
      ├── /backend
          ├── /graphql         # GraphQL schemas, resolvers, types
          ├── /datasources     # Data sources (e.g., DB queries, API calls)
          ├── /lib             # Utilities or middleware
          ├── /api             # Next.js API routes
  ├── /public
      ├── /fonts               
      ├── favicon.ico          
  ├── package.json
  ├── tsconfig.json
  ```

Next.js expected the `/pages` folder to be at the root of the `/src` directory (or at the root of the project if there's no `/src`). In my case, I had moved the `/pages` folder under `/src/frontend/pages/`, which is not where Next.js automatically looks for the pages. Naturally, the home page showed `404`.

### Step 6 - The final structure

- Moved `/pages` back to `/src/pages` and kept `/src/frontend` for all my frontend components and logic. This approach allowed for everything to be organized without breaking Next.js conventions.
- Moved the API routes to `/src/pages/api` from `/src/backend/api`
- After quite a few fixes and tweaks, it finally shaped up to:
  ```bash
  /project_root
  ├── /public
  │   ├── /fonts
  │   ├── favicon.ico
  │   └── logo.png
  ├── /src
  │   ├── /backend
  │   │   ├── /datasources
  │   │   │   ├── postAPI.ts
  │   │   │   └── userAPI.ts
  │   │   ├── /graphql
  │   │   │   ├── /resolvers
  │   │   │   │   ├── postResolver.ts
  │   │   │   │   └── userResolver.ts
  │   │   │   ├── /schemas
  │   │   │   │   ├── baseSchema.ts
  │   │   │   │   ├── postSchema.ts
  │   │   │   │   └── userSchema.ts
  │   │   │   ├── context.ts
  │   │   │   ├── index.ts
  │   │   │   └── types.ts
  │   │   └── /lib
  │   │       └── runMiddleware.js
  │   ├── /frontend
  │   │   ├── /components
  │   │   │   └── layout.tsx
  │   │   ├── /pages
  │   │   │   └── Home.tsx
  │   │   └── /styles
  │   │       └── globals.css
  │   ├── /pages
  │   │   ├── /api
  │   │   │   └── index.ts
  │   │   ├── _app.tsx
  │   │   └── index.tsx
  │   └── next-env.d.ts
  ├── package.json
  └── tsconfig.json
  ```

It was satisfactory to look at the overall structure and how iteratively I arrived at it. At this point, the next plan was to hook in:
- Databases (thinking of Redis and PostgreSQL)
- GraphQL Code Generator
- Testing libraries (unit, integration, and e2e) for both, the frontend and the backend
- UI component library (maybe Adobe Spectrum)

But all that would be much easier than the challenges above with combining Next.js, Express.js, GraphQL and setting up a clean file/folder structure for the Predeect monolith.