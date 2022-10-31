# react-oauth2-pkce

Authenticate against generic OAuth2 using PKCE.  \
Browser compatible. No Node.js Buffers nor Node.js crypto.

Forked from [gardner/react-oauth2-pkce](https://github.com/gardner/react-oauth2-pkce/commit/b0d9fea1e9e405037fdb094b2d3a59581f9a8f74) at [b0d9fea](https://github.com/gardner/react-oauth2-pkce/commit/b0d9fea1e9e405037fdb094b2d3a59581f9a8f74)

[![GitHub license](https://img.shields.io/badge/license-MIT-blue.svg)](https://github.com/bng5/react-oauth2-pkce/blob/master/LICENSE)
[![NPM](https://img.shields.io/npm/v/@bng5/react-oauth2-pkce.svg)](https://www.npmjs.com/package/@bng5/react-oauth2-pkce)

## Install

```bash
npm install @bng5/react-oauth2-pkce
```

## Basic usage

Wrap your application in the `AuthProvider` component.

```js
// App.jsx

import { AuthProvider } from '@bng5/react-oauth2-pkce';

import Home from './Home';

const providers = {
  spotify: {
    clientId: import.meta.env.VITE_SPOTIFY_CLIENT_ID,
    authorizeEndpoint: 'https://accounts.spotify.com/authorize',
    tokenEndpoint: 'https://accounts.spotify.com/api/token',
    refreshTokenEndpoint: 'https://accounts.spotify.com/api/token',
    scopes: ['user-library-read'],
  },
};

const App = () => (
  <AuthProvider providers={providers}>
    <Home />
  </AuthProvider>
);

export default App;
```

Access authentication data and methods with the `useAuth` hook.

```jsx
// Home.jsx

import { useAuth } from '@bng5/react-oauth2-pkce';

export const Home = () => {
  const {
    accessToken,
    authorize,
    isAuthenticated,
    isLoading,
    logout,
  } = useAuth();

  const login = () => {
    authorize().catch(err => console.log(err));
  }

  if (isLoading) {
    return <p>Loading...</p>;
  }

  if (isAuthenticated) {
    return (
      <div>
        <p>Access token: {accessToken}</p>
        <button onClick={() => logout()}>Logout</button>
      </div>
    );
  }

  return (
    <div>
      <button onClick={() => login()}>Login</button>
    </div>
  );
}

export default Home;
```

## The `AuthProvider` component

### props

#### providers
`Record<string, AuthProviderConfig>` \
*Required*

#### callbackPathname
`string` \
Default: '/callback'

#### storagePrefix
`string` \
Default: ''

#### suspense
`boolean` \
Default: false

#### throwOnError
`boolean` \
Default: false

## `AuthProviderConfig` type
```
export interface AuthProviderConfig {
  audience?: string
  authorizeEndpoint?: string
  autoRefresh?: boolean
  clientId: string
  clientSecret?: string
  contentType?: string
  issuer: string
  logoutEndpoint?: string
  redirectUri: string
  refreshSlack?: number
  scopes: string[]
  tokenEndpoint?: string
}
```

## The `useAuth` hook

```typescript
const {
  accessToken,
  authorize,
  fetchToken,
  isAuthenticated,
  isLoading,
  logout,
} = useAuth()
```

### isLoading

`boolean`

Indicates whether an access token is being requested or refreshed when mounting the component.

###  accessToken

`string` | `undefined`

### isAuthenticated

`boolean` | `undefined`

### authorize
`(authorizeOptions?: AuthorizeOptions) => Promise<void>`

#### authorizeOptions

- provider \
  `string` \
  One of the keys of `providers`. If not set the first provider in the `providers` object is used.

- rememberMe \
  `boolean` \
  Default: true \
  If set to true the refresh token will persisted after the window is closed.

### logout
`() => void`

## Examples

### Suspense

```typescript
import { FC, Suspense } from 'react';
import { AuthProvider } from '@bng5/react-oauth2-pkce';

import Home from './Home';

const providers: AuthProvidersConfig = {
  exampleProvider: {
    clientId: import.meta.env.VITE_SPOTIFY_CLIENT_ID,
    issuer: 'http://localhost:8080',
    scopes: ['user'],
  },
};

const App: FC = () => (
  <Suspense fallback={<p>Loading...s</p>}>
    <AuthProvider
      providers={providers}
      suspense
    >
      <Home />
    </AuthProvider>
  </Suspense>
);

export default App;
```

## License

[MIT license](./LICENSE).
