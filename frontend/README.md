# Multi-Tenant SaaS Platform – Frontend (React)

This frontend application was built using **Create React App (CRA)** and serves as the user interface for the **Multi-Tenant SaaS Platform**. It provides secure authentication, role-based dashboards, and tenant-specific project and task management.

---

## Available Scripts

In the project directory, you can run the following commands:

---

### npm start

Runs the application in **development mode**.

- Opens automatically at: http://localhost:3000
- The page reloads on code changes
- Any lint or runtime errors will be displayed in the console

This mode is intended for local development and debugging.

---

### npm test

Launches the test runner in **interactive watch mode**.

- Useful for running unit and component tests
- Supports continuous testing during development

More details:
https://facebook.github.io/create-react-app/docs/running-tests

---

### npm run build

Builds the application for **production**.

- Generates an optimized build in the `build/` folder
- React is bundled in production mode
- Output files are minified and include hashed filenames for caching
- The build is ready for deployment to a web server or container

More details:
https://facebook.github.io/create-react-app/docs/deployment

---

### npm run eject

WARNING: This is a **one-way operation**.

- Removes the single CRA build dependency
- Copies all configuration files (Webpack, Babel, ESLint, etc.) into the project
- Gives full control over the build configuration
- Once ejected, this action cannot be undone

You generally do NOT need to eject unless advanced customization is required.

---

## Project Usage Notes

- This frontend communicates with the backend via REST APIs
- Authentication is handled using JWT tokens
- Role-based routing ensures users only access authorized pages
- Tenant context is maintained across sessions

---

## Learn More

Create React App Documentation:
https://facebook.github.io/create-react-app/docs/getting-started

React Documentation:
https://reactjs.org/

---

## Additional References

Code Splitting:
https://facebook.github.io/create-react-app/docs/code-splitting

Bundle Size Analysis:
https://facebook.github.io/create-react-app/docs/analyzing-the-bundle-size

Progressive Web App (PWA):
https://facebook.github.io/create-react-app/docs/making-a-progressive-web-app

Advanced Configuration:
https://facebook.github.io/create-react-app/docs/advanced-configuration

Deployment:
https://facebook.github.io/create-react-app/docs/deployment

Build Troubleshooting:
https://facebook.github.io/create-react-app/docs/troubleshooting#npm-run-build-fails-to-minify

