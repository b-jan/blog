# Next.js: Use Server-Side Rendering in your React App (Part 1 / 2)

![Next.js](assets/ssr-next.jpg?raw=true "React SSR with Next.js")

Usually, we are completely running React.js on client-side: Javascript is interpreted by your browser.
The initial html returned by the server contains a placeholder, e.g. `<div id="root"></div>`, and then,
once all your scripts are loaded, the entire UI is rendered in the browser. We call it client-side rendering.

The problem is that, in the meantime, your visitor sees... nothing, a blank page!

Looking for how to get rid of this crappy blank page for a personal project, I discovered Next.js:
in my opinion the current best framework for making server-side rendering React applications.


### Why SSR (Server-Side Rendering)?

This is not the point of this article, but here is a quick sum-up of what SSR can bring to your application:

- Improve your SEO
- Speed up your first page load
- Avoid blank page flicker before rendering

If you want to know more about it, please read this great article:
[Benefits of Server Side Over Client Side Rendering](https://medium.com/walmartlabs/the-benefits-of-server-side-rendering-over-client-side-rendering-5d07ff2cefe8).

Let's focus on the how rather than the why here.


### What's the plan?

For this article, I chose to start with a basic app created with [create-react-app](https://github.com/facebook/create-react-app).
Your own React applications is probably using similar settings.

This article is split in 3 sections matching 3 server-side-rendering strategies:

- How to upgrade manually your React app to get SSR
- How to start with Next.js from scratch
- How to migrate your existing React app to server-side with Next.js

I won't go through all the steps, but I will bring your attention on the main points of interesting.
I also provide a repository for each of the 3 strategies.
As the article is a bit long, I split it in 2 articles, this one will only deal with the first 2 sections.
If your main concern is to migrate your app to Next.js, you can go directly to the second article [here]().


## 1) Look how twisted manual SSR is...

![Manually](assets/injury.jpg?raw=true "React SSR manually")

In that part, we will see how to implement SSR manually on an existing React app.
Let's take the [create-react-app](https://github.com/facebook/create-react-app) starter code:

- `package.json` for dependencies
- Webpack configuration included
- `App.js` - loads React and renders the Hello component
- `index.js` - puts all together into a root component

### Checking rendering type

I just added to the code base a simple function `isClientOrServer` based on the availability of `window`:

```
const isClientOrServer = () => {
  return (typeof window !== 'undefined' && window.document) ? 'client' : 'server';
};
```

so that we display on the page what is rendering the application: server or client.

To test it by yourself:

- clone [this repository](https://github.com/b-jan/manual-react-ssr)
- checkout the initial commit
- install the dependencies with `yarn`
- launch the dev server with `yarn start`
- browse to http://localhost:3000 to view the app

I am now simulating a '3G network' in Chrome so that we really understand what is going on:

![CSR](assets/cra-csr.gif?raw=true "Client-side rendering on a create-react-app")

### Implementing SSR

Let's fix that crappy flickering with server-side rendering!
I won't show all the code (check the repo to see it in details) but here are the main steps.

We first need a node server using Express: `yarn add express`.

In our React app, Webpack only loads the src/ folder, we can thus create a new folder named server/ next to it.
Inside, create a file `index.js` where we use express and a server renderer.

```
// use port 3001 because 3000 is used to serve our React app build
const PORT = 3001;
const path = require('path');

// initialize the application and create the routes
const app = express();
const router = express.Router();

// root (/) should always serve our server rendered page
router.use('^/$', serverRenderer);
```

To render our html, we use a server renderer that is replacing the root component with the built html:

```
// index.html file created by create-react-app build tool
const filePath = path.resolve(__dirname, '..', '..', 'build', 'index.html');

fs.readFile(filePath, 'utf8', (err, htmlData) => {
  // render the app as a string
  const html = ReactDOMServer.renderToString(<App />);

    // inject the rendered app into our html
    return res.send(
      htmlData.replace(
        '<div id="root"></div>',
        `<div id="root">${html}</div>`
      )
    );
  }
```

This is possible thanks to `ReactDOMServer.renderToString` which fully render the HTML markup of a page to a string.

We finally need an entry point that will tell Node how to interpret our React JSX code. We achieve this with Babel.

```
require('babel-register')({
  ignore: [ /(node_modules)/ ],
  presets: ['es2015', 'react-app']
});
```

Test it by yourself:

- checkout last changes on master branch
- install the dependencies with `yarn`
- build the application with `yarn build`
- declare babel environment in your terminal: `export BABEL_ENV=development`
- launch your node server with `node server/bootstrap.js`
- browse to http://localhost:3001 to view the app

Still simulating the '3G network' in Chrome, here is the result:

![SSR](assets/cra-ssr.gif?raw=true "Server-side rendering on a create-react-app")

Do not be mistaken, the page is rendered by server. But as soon as the javascript is fully loaded,
window.document is available and the `isClientOrServer()` function returns `client`.

We proved that we can do SSR, but what the heck with that React logo?

### We miss many features

Our example is a good proof of concept but very limited. We would like to see more feature like:

- import images in js files (logo problem)
- several routes usage or route management (check [this article](https://medium.com/@benlu/ssr-with-create-react-app-v2-1b8b520681d9))
- deal with the `</head>` and the metatags (for SEO improvements)
- code splitting (here is [an article](https://medium.com/bucharestjs/upgrading-a-create-react-app-project-to-a-ssr-code-splitting-setup-9da57df2040a) solving the problem)
- manage the state of our app or use Redux (check this [great article](https://medium.com/@cereallarceny/server-side-rendering-with-create-react-app-fiber-react-router-v4-helmet-redux-and-thunk-275cb25ca972)

and performance is bad on large pages:
`ReactDOMServer.renderToString()` is a synchronous CPU bound call and can starve out incoming requests to the server.
Walmart worked on [an optimization](https://github.com/walmartlabs/react-ssr-optimization) for their e-commerce website.

It is possible to make SSR work perfectly on top of create-react-app, we won't go through all the painful work in this article.
Still, if you're interested in it, I attached just above some great articles giving detailed explanations.

Seriously... Next.js can bring you all these features!


## 2) Next.js helps you building server rendered React.js Application

![Easy Next](assets/cucumber.jpg?raw=true "Easy SSR with Next.js")

### What is Next.js?

Next.js is a minimalistic framework for server-rendered React applications with:

- a very simple page based routing
- Webpack hot reloading
- automatic transpilation (with babel)
- deployment facilities
- automatic code splitting (loads page faster)
- built in css support
- ability to run server-side actions
- simple integration with Redux using next-redux-wrapper.

### Get started in 1 minute

In this short example, we are going to see how crazy simple it is to have a SSR app ready with Next.js.

First, generate your package.json with `npm init` and install Next.js with `npm install --save next react react-dom`.
Then, add a script to your package.json like this:

```
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
```

Create a pages/ folder. Every .js file becomes a route that gets automatically processed and rendered.
Add a index.js file in that pages/ folder (with the execution of our `isClientOrServer` function):

```
const Index = ({ title = 'Hello from Next.js' }) => (
  <div>
    <h1>{title}</h1>
    <p className="App-intro">
      Is my application rendered by server or client?
    </p>
    <h2><code>{isClientOrServer()}</code></h2>
  </div>
);

export default Index;
```

No need to import any library at the top of our index.js file, Next.js already knows that we are using React.

Now enter ```npm run dev``` into your terminal and go to http://localhost:3000: Tadaaaaa!

![Next Hello World](assets/next.gif?raw=true "Next.js Hello World Result")

Repeat the same operation inside your pages/ folder to create a new page.
The name you give to the file will directly be matched by the url to access it.

You're ready to go! You're already doing SSR.

### Use create-next-app

You want to start a server-side rendered React app, you can now stop using create-react-app,
and start using [create-next-app](https://github.com/segmentio/create-next-app):

```
npm install -g create-next-app

create-next-app my-app
cd my-app/
npm run dev
```

This is all you need to do to create a React app with server-side rendering thanks to Next.js.

Better than a simple Hello World app, check this [Hacker News clone](https://github.com/now-examples/next-news) implementing Next.js.
It is fully server-rendered, queries the data over Firebase and updates in realtime as new votes come in.


### Vue.js and Nuxt

You're a Vue.js developer. Just after Next.js first release, two french brothers made the same for Vue.js: Nuxt was born!
Like Vue, the [Nuxt documentation]((https://nuxtjs.org/)) is very clear and you can use the same starter template `vue-cli` for you app:

```
vue init nuxt-community/starter-template <project-name>
```

### What's Next?

Hope you liked this article which was mainly written in order to introduce server-side rendering with Next.
In the following, I am going to demonstrate how to migrate your existing create-react-app to Next.js.
