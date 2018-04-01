# Next.js: Use Server-Side Rendering in your React App 

![Next.js](assets/ssr-next.jpg?raw=true "React SSR with Next.js")

You are usually completely running ReactJS on the browser. For sure, the simplest setting for your React application is client-side rendering.
The initial html rendered by the server is a placeholder `<div id="root"></div>` and the entire UI is rendered in the browser once all your scripts load.

The problem is that, in the meantime, your visitor sees... nothing, a blank page!

Looking for how to get rid of this crappy blank page for a personal project, I discovered Next.js: the current best framework for making server-side rendering React applications.


### Why SSR (Server-Side Rendering)?

This is not the point of this article, but here is a quick sum-up of what SSR can bring to your application:

- Improve your SEO
- Speed up your first page load
- Avoid blank page flicker before rendering

If you want to know more about it, please read this great article:
[Benefits of Server Side Over Client Side Rendering](https://medium.com/walmartlabs/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8).

Let's focus on the how rather than the why here.


### What's the plan?

Facebook has created a node module [create-react-app](https://github.com/facebook/create-react-app) to generate a boilerplate version of a React application.
Many React applications are close to its settings. That's why I chose it as a code base for this article.

This article is split in 3 short parts:

- How to upgrade manually your React app to SSR
- How to start with Next.js from scratch
- How to bring Next.js to your existing React app

I won't go through all the steps, but I will bring your attention on the main points of interesting.
I also provide a repository for each of the 3 strategies.


## 1) Look how twisted manual SSR is...

![Manually](assets/injury.jpg?raw=true "React SSR manually")

In that part, we will see how to implement SSR manually on an existing React app. Let's take the [create-react-app] starter code:

- `package.json` for dependencies
- Webpack configuration included
- `App.js` - loads React and renders the Hello component
- `index.js` - putting all together into a root component

I just added to the code base a simple function `isClientOrServer`:

```
const isClientOrServer = () => {
  return (typeof window !== 'undefined' && window.document) ? 'client' : 'server';
};
```

so that we display on the page what is rendering the application.

If you want to test it by yourself:

- clone [this repository](https://github.com/b-jan/manual-react-ssr) I made.
- checkout the initial commit
- install the dependencies with `yarn`
- launch the dev server with `yarn start`
- browse to http://localhost:3000 to view the app.

I am now simulating a 'good 3G network' in Chrome so that we really understand what is going on.
An here is the result:

![CSR](assets/cra-csr.gif?raw=true "Client-side rendering on a create-react-app")

Now we will turn that flickering into a server rendering!




Launch And here is the result:


![SSR](assets/cra-ssr.gif?raw=true "Server-side rendering on a create-react-app")



### What we miss now

It is possible to make SSR work perfectly on top of create-react-app,
we won't go through all the painful work in this article.
Still, if you're interested in it,
[this great article](https://medium.com/@cereallarceny/server-side-rendering-with-create-react-app-fiber-react-router-v4-helmet-redux-and-thunk-275cb25ca972)
is giving detailed explanations.


we still miss code splitting

https://medium.com/bucharestjs/upgrading-a-create-react-app-project-to-a-ssr-code-splitting-setup-9da57df2040a





Guess what: Next.js can bring you all these features!


## Next.js helps you building server rendered React.js Application

![Easy Next](assets/cucumber.jpg?raw=true "Easy SSR with Next.js")

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

![Trick](assets/tunnel.jpg?raw=true "Next.js in existing app")

We are going to see a trick here. If you start from scratch and you know that you want to make an SSR React app,
you know that Next is the best choice. But what if your React app is already 6 months old?

About how to migrate, you can create a single Next.js page with your React Router components and then gradually
migrate each route to a Next.js one and remove the React Router route.

First make it work as a SPA, then progressively break up into pages.

https://blog.eleven-labs.com/en/migrate-a-react-client-side-application-to-server-side-with-nextjs/

https://gist.github.com/jaredpalmer/a73bc00cac8926ff0ad5281879b1eb90


## Improving again


If you want to know more, visit : https://learnnextjs.com/basics/getting-started
Vue.js has its own Next.js named Nuxt.js, visit : https://nuxtjs.org/guide
Caching
Better performance for end users, Better customer engagement



### Do both

Here, the initial render is on the server. Hence, the html received by the browser has the UI as it should be.
Once the scripts are loaded, the virtual DOM is re-rendered once again to set up your components' event handlers.

Over here, you need to make sure that you re-render the exact same virtual DOM (root ReactJS component) with
the same props that you used to render on the server. Otherwise, ReactJS will complain that the server-side and
client-side virtual DOMs don't match.


### Pre-rendering

https://medium.com/@dtinth/prerendering-a-create-react-app-app-6e8571d7d662

### Caching


### Vue.js and Nuxt

