To create a Next.js project with NextAuth and Google provider for authentication without using a database, follow these steps:

Create a new Next.js project:

```bash
npx create-next-app your-app-name
cd your-app-name
```

Install the required dependencies:
```bash
npm install next-auth
npm install @types/next-auth --save-dev
```

Now, let's create the necessary files and add the code.

File: pages/api/auth/[...nextauth].ts

```typescript
import { NextApiRequest, NextApiResponse } from 'next';
import NextAuth, { NextAuthOptions } from 'next-auth';
import Providers from 'next-auth/providers';

const options: NextAuthOptions = {
  providers: [
    Providers.Google({
      clientId: process.env.GOOGLE_CLIENT_ID,
      clientSecret: process.env.GOOGLE_CLIENT_SECRET,
    }),
  ],
  pages: {
    signIn: '/auth/signin',
    signOut: '/auth/signout',
    error: '/auth/error',
  },
  session: {
    jwt: true,
  },
  callbacks: {
    async jwt(token, _, account) {
      if (account) {
        token.id = account.id;
        token.email = account.email;
      }
      return token;
    },
    async session(session, token) {
      session.user.id = token.id;
      session.user.email = token.email;
      return session;
    },
  },
};

export default (req: NextApiRequest, res: NextApiResponse) =>
  NextAuth(req, res, options);
```

File: pages/auth/signin.tsx

```typescript
import { signIn } from 'next-auth/client';
import React from 'react';

const SignInPage: React.FC = () => {
  return (
    <div>
      <h1>Sign In</h1>
      <button onClick={() => signIn('google')}>Sign in with Google</button>
    </div>
  );
};

export default SignInPage;
```

File: pages/auth/signout.tsx
```typescript
import { signOut } from 'next-auth/client';
import React from 'react';

const SignOutPage: React.FC = () => {
  return (
    <div>
      <h1>Sign Out</h1>
      <button onClick={() => signOut()}>Sign out</button>
    </div>
  );
};

export default SignOutPage;
```

File: pages/auth/error.tsx

```typescript
import React from 'react';

const ErrorPage: React.FC = () => {
  return (
    <div>
      <h1>An error occurred</h1>
      <p>Please try again later.</p>
    </div>
  );
};

export default ErrorPage;
```

Add environment variables for the Google provider:
Create a .env.local file in the root directory and add the following environment variables. Replace your_google_client_id and your_google_client_secret with your actual credentials.

```bash
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
```

This should set up a basic Next.js application with NextAuth using the Google provider for authentication without a database.
