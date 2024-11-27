# Containerizing JSPaint and CI/CD Setup

## 1. Containerizing JSPaint

**Resource:** [JSPaint GitHub Repository](https://github.com/)

### Steps Taken:
To containerize JSPaint, a Node.js-based web application, we created a Dockerfile to define the environment and the steps to run the application.

### Dockerfile:
```dockerfile
# Use an official Node.js image as the base
FROM node:18-slim

# Set the working directory inside the container
WORKDIR /usr/src/app

# Copy package.json and package-lock.json for dependency installation
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the application source code
COPY . .

# Expose the port the application runs on
EXPOSE 3000

# Start the development server
CMD ["npm", "run", "dev"]
```

## Check Docker Setup:
To check if Docker is set up properly:

1. **Ensure Docker is running:**
   ```bash
   docker login
   docker ps -a
   ```

2. **Build the Docker Image:**
   ```bash
   docker build -t jspaint-dev .
   ```

3. **Run the container:**
   ```bash
   docker run -p 3000:3000 jspaint-dev
   ```

4. **Access the application:**
   Navigate to http://localhost:3000 to view the JSPaint app in your browser.

## 2. Setting Up a CI/CD Pipeline
### Steps Taken:
To implement CI/CD for JSPaint, we utilized GitHub Actions for automating testing, building, and deployment.

### GitHub Actions Workflow File:
Save this as `.github/workflows/ci-cd.yml` in the repository.

```yaml
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  lint-test-and-build:
    runs-on: ubuntu-latest

    steps:
      # 1. Checkout code
      - name: Checkout code
        uses: actions/checkout@v3

      # 2. Set up Node.js
      - name: Set up Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18

      # 3. Install dependencies
      - name: Install dependencies
        run: npm ci

      # 4. Run Linter
      - name: Run Linter
        run: npm run lint

      # 5. Run Tests
      - name: Run Tests
        run: npm test

  deploy:
    needs: lint-test-and-build
    runs-on: ubuntu-latest

    steps:
      # 1. Checkout code
      - name: Checkout code
        uses: actions/checkout@v3

      # 2. Install dependencies
      - name: Install dependencies
        run: npm ci

      # 3. Deploy to GitHub Pages
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

### Explanation of Workflow:

- **Linting and Testing:**  
  Runs `npm run lint` to check for code quality and `npm test` to execute tests using Cypress.

- **Deployment:**  
  Uses `peaceiris/actions-gh-pages` to deploy the app to GitHub Pages after successfully passing the lint and test steps.

  You can modify the `publish_dir` to the folder containing your build output (e.g., `./dist`).

### Outcome:

- **Automated CI/CD:**  
  The pipeline automatically tests, builds, and deploys the application upon each push to the `main` branch.

- **Reliable Deployments:**  
  Ensures only fully tested code is deployed.
  
### 3. Conclusion

By combining Docker for containerization and GitHub Actions for CI/CD, the entire build and deployment process of JSPaint is streamlined. This setup ensures:

- **Fast and reliable testing.**
- **Easy scalability through containerized environments.**
- **Simplified deployments with GitHub Pages.**
