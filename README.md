# Remix Grunge Stack

![The Remix Grunge Stack](https://repository-images.githubusercontent.com/463325363/edae4f5b-1a13-47ea-b90c-c05badc2a700)

Learn more about [Remix Stacks](https://remix.run/stacks).

```
npx create-remix --template remix-run/grunge-stack
```


## Development

- Validate the app has been set up properly (optional):

  ```sh
  npm run validate
  ```

- Start dev server:

  ```sh
  npm run dev
  ```

This starts your app in development mode, rebuilding assets on file changes.

## Deployment

This Remix Stack comes with two GitHub Actions that handle automatically deploying your app to production and staging environments. By default, Arc will deploy to the `us-west-2` region, if you wish to deploy to a different region, you'll need to change your [`app.arc`](https://arc.codes/docs/en/reference/project-manifest/aws)

Prior to your first deployment, you'll need to do a few things:

- Create a new [GitHub repo](https://repo.new)

- [Sign up](https://portal.aws.amazon.com/billing/signup#/start) and login to your AWS account

- Add `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` to [your GitHub repo's secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets). Go to your AWS [security credentials](https://console.aws.amazon.com/iam/home?region=us-west-2#/security_credentials) and click on the "Access keys" tab, and then click "Create New Access Key", then you can copy those and add them to your repo's secrets.

- Along with your AWS credentials, you'll also need to give your CloudFormation a `SESSION_SECRET` variable of its own for both staging and production environments, as well as an `ARC_APP_SECRET` for Arc itself.

  ```sh
  npx arc env --add --env staging ARC_APP_SECRET $(openssl rand -hex 32)
  npx arc env --add --env staging SESSION_SECRET $(openssl rand -hex 32)
  npx arc env --add --env production ARC_APP_SECRET $(openssl rand -hex 32)
  npx arc env --add --env production SESSION_SECRET $(openssl rand -hex 32)
  ```

  If you don't have openssl installed, you can also use [1password](https://1password.com/password-generator) to generate a random secret, just replace `$(openssl rand -hex 32)` with the generated secret.



## Testing

### Cypress

We use Cypress for our End-to-End tests in this project. You'll find those in the `cypress` directory. As you make changes, add to an existing file or create a new file in the `cypress/e2e` directory to test your changes.

We use [`@testing-library/cypress`](https://testing-library.com/cypress) for selecting elements on the page semantically.

To run these tests in development, run `npm run test:e2e:dev` which will start the dev server for the app as well as the Cypress client. Make sure the database is running in docker as described above.

We have a utility for testing authenticated features without having to go through the login flow:

```ts
cy.login();
// you are now logged in as a new user
```

We also have a utility to auto-delete the user at the end of your test. Just make sure to add this in each test file:

```ts
afterEach(() => {
  cy.cleanupUser();
});
```

That way, we can keep your local db clean and keep your tests isolated from one another.

### Vitest

For lower level tests of utilities and individual components, we use `vitest`. We have DOM-specific assertion helpers via [`@testing-library/jest-dom`](https://testing-library.com/jest-dom).

### Type Checking

This project uses TypeScript. It's recommended to get TypeScript set up for your editor to get a really great in-editor experience with type checking and auto-complete. To run type checking across the whole project, run `npm run typecheck`.

### Linting

This project uses ESLint for linting. That is configured in `.eslintrc.js`.

### Formatting

We use [Prettier](https://prettier.io/) for auto-formatting in this project. It's recommended to install an editor plugin (like the [VSCode Prettier plugin](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)) to get auto-formatting on save. There's also a `npm run format` script you can run to format all files in the project.
