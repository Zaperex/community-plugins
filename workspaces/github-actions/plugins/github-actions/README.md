# GitHub Actions Plugin

Website: [https://github.com/actions](https://github.com/actions)

## Table of Contents

- [GitHub Actions Plugin](#github-actions-plugin)
  - [Table of Contents](#table-of-contents)
  - [Screenshots](#screenshots)
  - [Setup](#setup)
    - [Generic Requirements](#generic-requirements)
    - [Installation](#installation)
    - [Integrating with `EntityPage`](#integrating-with-entitypage)
    - [Integrating with `EntityPage` (New Frontend System)](#integrating-with-entitypage-new-frontend-system)
    - [Self-hosted / Enterprise GitHub](#self-hosted--enterprise-github)
  - [Features](#features)
  - [Limitations](#limitations)
  - [Optional Workflow Runs Card View](#optional-workflow-runs-card-view)

## Screenshots

TBD

## Setup

### Generic Requirements

1. Provide OAuth credentials:
   1. [Create an OAuth App](https://developer.github.com/apps/building-oauth-apps/creating-an-oauth-app/) in the GitHub organization with the callback URL set to `http://localhost:7007/api/auth/github/handler/frame`.
      **Note**: This can be done with a user account also. Depending on if you use a personal account or an organization account will change the repositories this is functional with
1. Take the Client ID and Client Secret from the newly created app's settings page and you can do either:

   1. Put them into `AUTH_GITHUB_CLIENT_ID` and `AUTH_GITHUB_CLIENT_SECRET` environment variables.
   2. Add them to the app-config like below:

   ```yaml
   auth:
     providers:
       github:
         development:
           clientId: ${AUTH_GITHUB_CLIENT_ID}
           clientSecret: ${AUTH_GITHUB_CLIENT_SECRET}
   ```

1. Annotate your component with a correct GitHub Actions repository and owner:

   The annotation key is `github.com/project-slug`.

   Example:

   ```yaml
   apiVersion: backstage.io/v1alpha1
   kind: Component
   metadata:
     name: backstage
     description: backstage.io
     annotations:
       github.com/project-slug: 'backstage/backstage'
   spec:
     type: website
     lifecycle: production
     owner: user:guest
   ```

### Installation

1. Install the plugin dependency in your Backstage app package:

```bash
# From your Backstage root directory
yarn --cwd packages/app add @backstage-community/plugin-github-actions
```

### Integrating with `EntityPage`

1. Add to the app `EntityPage` component:

```tsx
// In packages/app/src/components/catalog/EntityPage.tsx
import {
  EntityGithubActionsContent,
  isGithubActionsAvailable,
} from '@backstage-community/plugin-github-actions';

// You can add the tab to any number of pages, the service page is shown as an
// example here
const serviceEntityPage = (
  <EntityLayout>
    {/* other tabs... */}
    <EntityLayout.Route path="/github-actions" title="GitHub Actions">
      <EntityGithubActionsContent />
    </EntityLayout.Route>
```

3. Run the app with `yarn start` and the backend with `yarn start-backend`.
   Then navigate to `/github-actions/` under any entity.

### Integrating with `EntityPage` (New Frontend System)

Follow this section if you are using Backstage's [new frontend system](https://backstage.io/docs/frontend-system/).

1. Import `githubActionsPlugin` in your `App.tsx` and add it to your app's `features` array:

```typescript
import githubActionsPlugin from '@backstage-community/plugin-github-actions/alpha';

// ...

export const app = createApp({
  features: [
    // ...
    githubActionsPlugin,
    // ...
  ],
});
```

2. Next, enable your desired extensions in `app-config.yaml`

```yaml
app:
  extensions:
    - entity-content:github-actions/entity
    - entity-card:github-actions/latest-workflow-run
    - entity-card:github-actions/latest-branch-workflow-runs
    - entity-card:github-actions/recent-workflow-runs
```

3. Whichever extensions you've enabled should now appear in your entity page.

### Self-hosted / Enterprise GitHub

The plugin will try to use `backstage.io/source-location` or `backstage.io/managed-by-location`
annotations to figure out the location of the source code.

1. Add the `host` and `apiBaseUrl` to your `app-config.yaml`

```yaml
# app-config.yaml

integrations:
  github:
    - host: 'your-github-host.com'
      apiBaseUrl: 'https://api.your-github-host.com'
```

## Features

- List workflow runs for a project
- Dive into one run to see a job steps
- Retry runs
- Pagination for runs

## Limitations

- There is a limit of 100 apps for one OAuth client/token pair

## Optional Workflow Runs Card View

Github Workflow Runs optional UI to show in Card view instead of table, with branch selection option

```tsx

// You can add the tab to any number of pages, the service page is shown as an
// example given here
const serviceEntityPage = (
  <EntityLayout>
    {/* other tabs... */}
    <EntityLayout.Route path="/github-actions" title="GitHub Actions">
      <EntityGithubActionsContent view='cards' />
    </EntityLayout.Route>
```
