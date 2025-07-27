# System Design & Problem-Solving Interview Questions & Answers

## Design a Scalable Next.js App

**1. How would you architect a Next.js app with millions of users?**

- Use SSR/ISR for dynamic and static content.
- Deploy on scalable infrastructure (Vercel, AWS, GCP).
- Use CDN for static assets and edge caching.
- Implement horizontal scaling for API routes.
- Use managed databases (e.g., AWS RDS, MongoDB Atlas).
- Monitor performance and errors (Datadog, Sentry).

**2. How do you handle rate limiting, caching, and load balancing in a Next.js API?**

- Use middleware or API gateways (NGINX, API Gateway) for rate limiting.
- Cache responses at the CDN or server level.
- Use Redis or similar for server-side caching.
- Deploy behind a load balancer (AWS ELB, GCP Load Balancer).

---

## Debugging & Troubleshooting

**1. A Next.js page is loading slowly. How would you diagnose and fix it?**

- Profile server and client performance (DevTools, Lighthouse).
- Check for large bundle sizes and optimize code splitting.
- Optimize images and static assets.
- Analyze API response times and database queries.
- Use caching and CDN for static content.

**2. A React component is re-rendering unnecessarily. How would you debug and optimize it?**

- Use React DevTools to inspect re-renders.
- Check props and state changes.
- Use `React.memo`, `useMemo`, and `useCallback` to prevent unnecessary renders.
- Refactor large components into smaller, focused ones.

---

## Case Study Questions

**1. Walk us through how you would build a real-time dashboard with Next.js and WebSockets.**

- Set up a WebSocket server (Socket.IO, ws) for real-time data.
- Create a Next.js page/component that connects to the WebSocket.
- Use state management (Context, Redux, Zustand) to store live data.
- Render dashboard widgets that update on new events.
- Handle reconnection and error states gracefully.

**2. How would you implement a feature like "dark mode" in a Next.js app with SSR support?**

- Store theme preference in a cookie or localStorage.
- On SSR, read the cookie and set the initial theme.
- Use a context/provider to manage theme state.
- Apply CSS variables or Tailwind dark mode classes.
- Provide a toggle button for users.
