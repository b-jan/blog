# Upgrade your React App with Server-Side Rendering: Next.js

![Next.js](assets/ssr-next.jpg?raw=true "React SSR with Next.js")


## Why SSR (Server-Side Rendering)?

This is not the point of this article, but here is a quick sum-up of what SSR can bring to your application:

- Improve your SEO
- Speed up your first page load
- Avoid blank page flicker before rendering

If you want to know more about it, please read this great article:
[Benefits of Server Side Over Client Side Rendering](https://medium.com/walmartlabs/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8).

Let's focus on the how rather than on the why here.

## Look how painful manual SSR is...

![Manually](assets/painful.jpg?raw=true "React SSR manually")

In that part, we will see how to implement SSR on an existing React app. Let's take this starter code with:

- `package.json` for dependencies
- Webpack and Babel configuration
- `index.html` - the HTML for the app
- `index.js` - loads React and renders the Hello component

If you don't have any existing React app, clone this repository I made:

```
git clone ...
```

Install the dependencies with `npm install` and start the development server with `npm run start`.

Browse to http://localhost:3000 to view the app.

## And still painful with Create React App

Create React App is ...

It is also possible to make SSR work on top of Create React App, we won't go through that painful work
twice in this article. Still, if you're interested in it, this great article is

https://medium.com/bucharestjs/upgrading-a-create-react-app-project-to-a-ssr-code-splitting-setup-9da57df2040a


## Next.js helps you building server rendered React.js Application

![Easy Next](assets/relax.jpg?raw=true "Easy SSR with Next.js")

### What is Next.js?

Next.js is a minimalistic framework for server-rendered React applications with:

- a very simple page based routing
- Webpack hot reloading
- deployment facilities
- automatic code splitting (loads page faster)
- built in css support

### Get started in 1 minute

In this short example, we are going to see how crazy simple it is to have a SSR app ready with Next.js.

First, generate your package.json with `npm init` and install Next.js with `npm install --save next react react-dom`.
Then, add a script to your package.json like this:

```
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

Create a pages/ folder. Every .js file becomes a route that gets automatically processed and rendered.
No need to import any library at the top of our index.js file, Next.js already knows that we are using React.

```
const Index = ({ title = 'Hello from our Theodo Next.js tutorial' }) => (
  <div>
    <h1>{title}</h1>
  </div>
);

export default Index;
```

Now enter ```npm run dev``` into your terminal and go to http://localhost:3000: Tadaaaaa!

![Hello World Result](assets/1.png?raw=true "Hello World Result")

Repeat the same operation to create a new page 'About':

```
const About = ({ title = 'Hello from the About page' }) => (
  <div>
    <h1>{title}</h1>
  </div>
);

export default About;
```

Navigate to http://localhost:3000/about:

![Hello World Result](assets/1.png?raw=true "Hello World Result")

You're ready to go! You're already doing SSR.


## How to use the power of Next.js in my existing React.js application?

![Trick](assets/cave.jpg?raw=true "Next.js in existing app")

We are going to see a trick here. If you start from scratch and you know that you want to make an SSR React app,
you know that Next is the best choice. But what if your React app is already 6 months old?

About how to migrate, you can create a single Next.js page with your React Router components and then gradually
migrate each route to a Next.js one and remove the React Router route.

First make it work as a SPA, then progressively break up into pages.

https://blog.eleven-labs.com/en/migrate-a-react-client-side-application-to-server-side-with-nextjs/

https://gist.github.com/jaredpalmer/a73bc00cac8926ff0ad5281879b1eb90


## Conclusion

If you want to know more, visit : https://learnnextjs.com/basics/getting-started
Vue.js has its own Next.js named Nuxt.js, visit : https://nuxtjs.org/guide
Caching
Better performance for end users, Better customer engagement
