# Next.js Interview Questions & Answers

## Core Features

**1. Explain the difference between SSG, SSR, and ISR in Next.js.**

**Static Site Generation (SSG):**
- Pages generated at build time
- Served as static files
- Best performance
- Good for content that doesn't change often

```jsx
// getStaticProps - runs at build time
export async function getStaticProps() {
  const posts = await fetchPosts();
  
  return {
    props: { posts },
    // Regenerate page at most once per day
    revalidate: 86400
  };
}

// For dynamic routes
export async function getStaticPaths() {
  const posts = await fetchPosts();
  const paths = posts.map(post => ({
    params: { id: post.id }
  }));
  
  return {
    paths,
    fallback: 'blocking' // or true, false
  };
}
```

**Server-Side Rendering (SSR):**
- Pages generated on each request
- Fresh data on every request
- Slower than SSG but always up-to-date

```jsx
export async function getServerSideProps(context) {
  const { req, res, params, query } = context;
  const user = await fetchUser(params.id);
  
  if (!user) {
    return {
      notFound: true
    };
  }
  
  return {
    props: { user }
  };
}
```

**Incremental Static Regeneration (ISR):**
- Combines benefits of SSG and SSR
- Static pages that can be updated after build
- Background regeneration

```jsx
export async function getStaticProps() {
  const data = await fetchData();
  
  return {
    props: { data },
    revalidate: 60 // Revalidate at most once per minute
  };
}
```

**2. How does Next.js handle API routes? What are the best practices?**

API routes provide serverless functions for backend logic.

**Basic API Route:**
```jsx
// pages/api/users/[id].js
export default async function handler(req, res) {
  const { method, query: { id } } = req;
  
  switch (method) {
    case 'GET':
      try {
        const user = await getUserById(id);
        res.status(200).json(user);
      } catch (error) {
        res.status(404).json({ error: 'User not found' });
      }
      break;
      
    case 'PUT':
      try {
        const updatedUser = await updateUser(id, req.body);
        res.status(200).json(updatedUser);
      } catch (error) {
        res.status(400).json({ error: error.message });
      }
      break;
      
    default:
      res.setHeader('Allow', ['GET', 'PUT']);
      res.status(405).end(`Method ${method} Not Allowed`);
  }
}
```

**Best Practices:**

1. **Middleware for common functionality:**
```jsx
// middleware/auth.js
export function withAuth(handler) {
  return async (req, res) => {
    const token = req.headers.authorization?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({ error: 'No token provided' });
    }
    
    try {
      const user = await verifyToken(token);
      req.user = user;
      return handler(req, res);
    } catch (error) {
      return res.status(401).json({ error: 'Invalid token' });
    }
  };
}

// Usage
export default withAuth(async function handler(req, res) {
  // Handler logic with authenticated user
});
```

2. **Input validation:**
```jsx
import Joi from 'joi';

const userSchema = Joi.object({
  name: Joi.string().required(),
  email: Joi.string().email().required()
});

export default async function handler(req, res) {
  if (req.method === 'POST') {
    const { error, value } = userSchema.validate(req.body);
    
    if (error) {
      return res.status(400).json({ error: error.details[0].message });
    }
    
    // Process valid data
  }
}
```

3. **Error handling:**
```jsx
// utils/apiHandler.js
export function apiHandler(handler) {
  return async (req, res) => {
    try {
      await handler(req, res);
    } catch (error) {
      console.error('API Error:', error);
      res.status(500).json({ error: 'Internal Server Error' });
    }
  };
}
```

**3. How would you implement dynamic routing in Next.js?**

- Use file-based routing with brackets for dynamic segments:
  ```jsx
  // pages/posts/[id].js
  import { useRouter } from 'next/router';
  export default function Post() {
    const router = useRouter();
    const { id } = router.query;
    return <div>Post ID: {id}</div>;
  }
  ```
- For catch-all routes: `[...slug].js`
- For optional catch-all: `[[...slug]].js`

---

## Performance & SEO

**1. How do you optimize a Next.js application for SEO?**

- Use SSR/SSG for content pages.
- Set meta tags, titles, canonical URLs, and Open Graph tags with `<Head>`.
- Generate sitemaps and robots.txt.
- Use semantic HTML and ARIA attributes.
- Optimize page speed (image optimization, code splitting, minimal JS).

**2. Explain how Next.js handles image optimization. What are the benefits of using the `next/image` component?**

- The `next/image` component optimizes images on-demand: lazy loading, resizing, WebP/AVIF support, responsive images, CDN integration.
- Benefits: smaller payloads, better LCP, improved SEO and UX.

**Example:**
```jsx
import Image from 'next/image';
<Image src="/images/photo.jpg" alt="Description" width={600} height={400} quality={80} priority />
```

**3. How would you implement internationalization (i18n) in Next.js?**

- Use built-in i18n routing in `next.config.js`:
  ```js
  module.exports = {
    i18n: {
      locales: ['en', 'fr', 'es'],
      defaultLocale: 'en',
    },
  };
  ```
- Use libraries like `next-i18next` or `react-intl` for translations.
- Structure content and routes to support locale subpaths or domains.

---

## Advanced Topics

**1. How does Next.js handle middleware? Provide a use case for it.**

- Middleware runs before a request is completed, allowing you to modify the request/response.
- In Next.js 12+, middleware is placed in `middleware.ts` at the root or in specific folders.
- Use cases: authentication, redirects, logging, A/B testing.

**Example:**
```ts
// middleware.ts
import { NextResponse } from 'next/server';
export function middleware(request) {
  const { pathname } = request.nextUrl;
  if (pathname.startsWith('/admin') && !request.cookies.get('token')) {
    return NextResponse.redirect('/login');
  }
  return NextResponse.next();
}
```

**2. What are the benefits of using Next.js over a custom React setup?**

- Built-in SSR/SSG/ISR for SEO and performance.
- File-based routing and API routes.
- Image optimization, static asset handling.
- Middleware, internationalization, and analytics.
- Zero-config production builds and Vercel integration.

**3. How would you migrate a legacy React app to Next.js?**

- Audit the current app for SSR/SSG needs.
- Move components/pages into the `pages` or `app` directory.
- Refactor routing to use Next.js file-based routing.
- Move API logic to `/api` routes if needed.
- Update data fetching to use `getStaticProps`, `getServerSideProps`, or React Server Components.
- Test SSR/SSG output and optimize for SEO.
