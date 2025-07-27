# Deployment & DevOps Interview Questions & Answers

## Senior Engineer Deployment Questions & Answers

**1. How do you deploy a Next.js project to AWS (EC2, S3+CloudFront, Amplify, etc.)?**

- **EC2:** Provision an EC2 instance, install Node.js, clone your repo, install dependencies, build the app, and run with PM2 or a process manager. Use NGINX as a reverse proxy. Set up a CI/CD pipeline (e.g., GitHub Actions) to automate deployments.
- **S3 + CloudFront (Static Export):** Use `next export` for static sites, upload the `out` directory to S3, and serve via CloudFront for global CDN. Configure invalidations for cache updates.
- **Amplify:** Connect your repo to AWS Amplify, configure build settings, and Amplify handles build, deploy, and hosting (supports SSR/ISR with Lambda@Edge).
- **Elastic Beanstalk:** Deploy as a Node.js app, Elastic Beanstalk manages scaling and load balancing.
- **Best Practices:** Use environment variables via AWS Parameter Store or Secrets Manager, automate with CI/CD, monitor with CloudWatch.

**2. How do you deploy a Next.js project to DigitalOcean?**

- **App Platform:** Connect your repo to DigitalOcean App Platform, set build and run commands, and configure environment variables. App Platform supports SSR and static sites.
- **Droplet:** Provision a Droplet (VM), install Node.js, clone repo, install dependencies, build, and run with PM2. Use NGINX as a reverse proxy.
- **Container Registry + Kubernetes:** Build a Docker image, push to DigitalOcean Container Registry, and deploy with DigitalOcean Kubernetes (DOKS) for scalable production.
- **Best Practices:** Use managed databases, enable automatic deploys, and monitor with DigitalOcean Monitoring.

**3. What are best practices for CI/CD with Next.js?**

- Use GitHub Actions, GitLab CI, or CircleCI to automate build, test, and deploy.
- Run linting, type checks, and tests in the pipeline.
- Use environment-specific variables and secrets.
- Deploy to staging before production.
- Use blue/green or canary deployments for zero-downtime.
- Roll back automatically on failure.

**4. How do you handle environment variables and secrets in production?**

- Never commit secrets to version control.
- Use platform-specific secret managers (AWS SSM, Secrets Manager, Vercel/Netlify/DigitalOcean env vars).
- For Docker, use `docker secrets` or pass env vars at runtime.
- Use `.env.production` for build-time variables, and runtime env vars for serverless/SSR.

**5. How do you ensure zero-downtime deployments?**

- Use blue/green or canary deployment strategies.
- Deploy new version alongside old, switch traffic after health checks.
- Use load balancers to manage traffic.
- Automate rollbacks on failure.
- Use process managers (PM2) for graceful restarts.

**6. How do you monitor and roll back deployments?**

- Integrate monitoring (Datadog, New Relic, Sentry, CloudWatch) for errors and performance.
- Use health checks in CI/CD.
- Automate rollbacks on failed health checks or error spikes.
- Keep previous build artifacts for quick rollback.

**7. What are the differences between static and serverless deployments for Next.js?**

- **Static:** Uses `next export` or SSG, deploys static files to CDN (S3, Netlify, Vercel). Fast, cheap, but no SSR.
- **Serverless:** Deploys SSR/ISR pages as serverless functions (AWS Lambda, Vercel, Netlify). Supports dynamic content, scales automatically, but cold starts and execution limits apply.
- **Hybrid:** Next.js supports hybrid apps with both static and serverless pages.

**8. How do you optimize Next.js for production performance and cost?**

- Use SSG/ISR for as many pages as possible.
- Optimize images with `next/image`.
- Use CDN for static assets.
- Minimize bundle size (analyze with `next build` and bundle analyzer).
- Use serverless for dynamic routes, but monitor cold starts and costs.
- Cache API responses and use edge caching.
- Monitor and tune serverless function memory/timeouts.

---

## Additional Basic & Advanced Deployment Questions

**1. What is the difference between a rolling deployment and a blue/green deployment?**

- **Rolling:** Gradually replaces old instances with new ones, some users may hit old/new during deploy.
- **Blue/Green:** Deploys new version (green) alongside old (blue), switch all traffic at once after validation, enables instant rollback.

**2. How do you use Docker to containerize a Next.js app for deployment?**

- Write a `Dockerfile` that installs dependencies, builds the app, and runs it.
- Use multi-stage builds to keep images small.
- Example:
  ```dockerfile
  FROM node:18-alpine AS builder
  WORKDIR /app
  COPY . .
  RUN yarn install --frozen-lockfile
  RUN yarn build

  FROM node:18-alpine
  WORKDIR /app
  COPY --from=builder /app ./
  EXPOSE 3000
  CMD ["yarn", "start"]
  ```
- Push to a registry and deploy to any container platform.

**3. How do you handle SSL/HTTPS in production deployments?**

- Use managed certificates (AWS ACM, Let's Encrypt).
- Terminate SSL at the load balancer or CDN (CloudFront, NGINX).
- Redirect HTTP to HTTPS.
- Renew certificates automatically.

**4. How do you automate infrastructure provisioning for deployments?**

- Use Infrastructure as Code (IaC) tools: Terraform, AWS CloudFormation, Pulumi.
- Version control your infrastructure.
- Automate with CI/CD pipelines.

**5. How do you scale a Next.js app to handle high traffic?**

- Use CDN for static assets and SSG pages.
- Use auto-scaling groups or serverless for SSR.
- Optimize database connections (pooling, managed DBs).
- Cache API responses.
- Monitor and tune for bottlenecks.
