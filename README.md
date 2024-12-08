# fetch-data

Step 1: Fetch Data Using SSR and SSG
In this assignment, we will fetch data using SSR for real-time data fetching on each request and SSG for static data fetching at build time.

1.1 Server-Side Rendering (SSR)
SSR is useful when you need the data to be fetched on each request, ensuring the user always gets the most up-to-date content.

Let's create an SSR page that fetches data from a public API, such as the JSONPlaceholder API.

File: pages/ssr.js

javascript
Copy code
import React from 'react';

const SSRPage = ({ posts }) => {
  return (
    <div className="p-4">
      <h1 className="text-3xl font-bold mb-4">SSR - Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id} className="border-b py-2">
            <h2 className="font-semibold">{post.title}</h2>
            <p>{post.body}</p>
          </li>
        ))}
      </ul>
    </div>
  );
};

// Fetch data using SSR
export async function getServerSideProps() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();
  
  return {
    props: {
      posts,
    },
  };
}

export default SSRPage;
Explanation:

getServerSideProps: Fetches data on each request from the server before rendering the page.
The data from jsonplaceholder.typicode.com/posts is passed to the component as a prop.
1.2 Static Site Generation (SSG)
SSG is useful when the data doesn’t change frequently, and you want to generate static HTML at build time.

Let’s create an SSG page that fetches data from the same API but at build time.

File: pages/ssg.js

javascript
Copy code
import React from 'react';

const SSGPage = ({ posts }) => {
  return (
    <div className="p-4">
      <h1 className="text-3xl font-bold mb-4">SSG - Posts</h1>
      <ul>
        {posts.map((post) => (
          <li key={post.id} className="border-b py-2">
            <h2 className="font-semibold">{post.title}</h2>
            <p>{post.body}</p>
          </li>
        ))}
      </ul>
    </div>
  );
};

// Fetch data using SSG
export async function getStaticProps() {
  const res = await fetch('https://jsonplaceholder.typicode.com/posts');
  const posts = await res.json();

  return {
    props: {
      posts,
    },
    revalidate: 60, // Optional: Revalidate data every 60 seconds
  };
}

export default SSGPage;
Explanation:

getStaticProps: Fetches data at build time. The page is generated once during the build and served statically.
revalidate: You can add this to refresh the static content at a given interval (for incremental static regeneration).
Step 2: Basic User Authentication with a Simple Auth API
Next, let’s implement a basic user authentication system using a simple API.

We'll use Next.js API routes to simulate a simple authentication system.

2.1 Create the Authentication API
We'll create an API route to simulate a login process.

File: pages/api/auth.js

javascript
Copy code
export default function handler(req, res) {
  if (req.method === 'POST') {
    const { username, password } = req.body;

    // Simple username and password check (for demo purposes)
    if (username === 'user' && password === 'password123') {
      res.status(200).json({ message: 'Authenticated', user: { username } });
    } else {
      res.status(401).json({ message: 'Invalid credentials' });
    }
  } else {
    res.status(405).json({ message: 'Method not allowed' });
  }
}
Explanation:

This API route handles POST requests to authenticate a user by checking the provided username and password. In a real-world scenario, you'd use a database and hashing to store passwords securely.
2.2 Create a Login Form
Now, we’ll create a simple login form that communicates with the /api/auth endpoint.

File: pages/login.js

javascript
Copy code
import { useState } from 'react';

const LoginPage = () => {
  const [username, setUsername] = useState('');
  const [password, setPassword] = useState('');
  const [message, setMessage] = useState('');
  const [isAuthenticated, setIsAuthenticated] = useState(false);

  const handleLogin = async (e) => {
    e.preventDefault();

    const res = await fetch('/api/auth', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({ username, password }),
    });

    const data = await res.json();
    if (res.status === 200) {
      setIsAuthenticated(true);
      setMessage(`Welcome ${data.user.username}`);
    } else {
      setIsAuthenticated(false);
      setMessage(data.message);
    }
  };

  return (
    <div className="p-4">
      <h1 className="text-3xl font-bold mb-4">Login</h1>
      <form onSubmit={handleLogin}>
        <input
          type="text"
          placeholder="Username"
          value={username}
          onChange={(e) => setUsername(e.target.value)}
          className="mb-2 p-2 border rounded"
        />
        <input
          type="password"
          placeholder="Password"
          value={password}
          onChange={(e) => setPassword(e.target.value)}
          className="mb-2 p-2 border rounded"
        />
        <button type="submit" className="bg-blue-600 text-white p-2 rounded">
          Log In
        </button>
      </form>

      {message && <p className="mt-4">{message}</p>}
    </div>
  );
};

export default LoginPage;
Explanation:

This form sends a POST request to /api/auth with the username and password.
If the login is successful, it displays a welcome message. If not, it shows an error message.
Deliverables
SSR Page: Fetches data from a public API and renders it server-side on each request.
SSG Page: Fetches data from a public API and generates static content at build time.
Basic Authentication System: A simple login page that interacts with an authentication API.
Deployment
Once completed, you can deploy the application on Vercel or any platform that supports Next.js.

