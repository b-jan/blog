Framework minimalist to build server rendered ReactJS Application with Next.js

Next js photo

Generate your package.json with ```npm init```

Install next:
```
npm install --save next react react-dom
```

and add a script to your package.json like this:

```
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```

install other dependencies for the aim of the tutorial :
```
npm install --save dotenv express material-ui react-tap-event-plugin
```

Create a pages/ folder. Every .js file becomes a route that gets automatically processed and rendered.
No need to import any library at the top of our index.js file, Next knows that we are using React.

```
const Index = ({ title = 'Hello from our Theodo Next.js tutorial' }) => (
  <div>
    <h1>{title}</h1>
  </div>
);

export default Index;
```

Now enter ```npm run dev``` into your terminal and go to http://localhost:3000

![Hello World Result](assets/1.png?raw=true "Hello World Result")

```
const About = ({ title = 'Hello from the About page' }) => (
  <div>
    <h1>{title}</h1>
  </div>
);

export default About;
```

Navigate to http://localhost:3000/about

Implement a custom material UI theme on the server
create a shared folder and inside a MaterialUI folder

Inside, create a component withMaterialUI.js which will be a Higher Order component

When implementing  Material UI from withing a server rendered env, we need to make sure that our app is loading the same code on server and client
=> getInitialProps lifecycle hooks

create withMaterialUI.js file like this :

```
import React, {Component} from 'react';
import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';
import getMuiTheme from 'material-ui/styles/getMuiTheme';
import injectTapEventPlugin from 'react-tap-event-plugin';
import Head from 'next/head';
import {
  PRIMARY_COLOR_1,
  PRIMARY_COLOR_2,
  PRIMARY_COLOR_3,
  ACCENT_COLOR_1,
  ACCENT_COLOR_2,
  ACCENT_COLOR_3
} from "./theme";

try {
  injectTapEventPlugin();
} catch (e) {

}

const withMaterialUI = ComposedComponent => {
  class HOC extends Component {

    static async getInitialProps(ctx) {
      const {req} = ctx;
      const userAgent = req ? req.headers['user-agent'] : navigator.userAgent;
      const subProps = await ComposedComponent.getInitialProps(ctx)

      return {
        ...subProps,
        userAgent
      };
    }

    render() {
      const {userAgent} = this.props;
      const Lato = 'lato, sans-serif';
      const muiTheme = getMuiTheme(
        {
          fontFamily: Lato,
          palette: {
            primary1Color: PRIMARY_COLOR_1,
            primary2Color: PRIMARY_COLOR_2,
            primary3Color: PRIMARY_COLOR_3,
            accent1Color: ACCENT_COLOR_1,
            accent2Color: ACCENT_COLOR_2,
            accent3Color: ACCENT_COLOR_3
          },
          appBar: {
            height: 50
          }
        },
        {
          userAgent
        }
      );
      return (
        <div>
          <Head>
            <title>Next Theodo Blog</title>
            <meta name='viewport' content='initial-scale=1.0, width=device-width'/>
            <link href='https://fonts.googleapis.com/css?family=Lato' rel='stylesheet'/>
          </Head>
          <MuiThemeProvider muiTheme={muiTheme}>
            <ComposedComponent {...this.props} />
          </MuiThemeProvider>
        </div>
      )
    }
  }
  return HOC;
}

export default withMaterialUI;
```


Render Material UI component with Next

create a components folder where we will put all our reusable components

create header.js
```
import AppBar from 'material-ui/AppBar';

const Header = ({title = 'Next.js Theodo blog app'}) =>
  <AppBar title={title} showMenuIconButton={false} />

export default Header;
```

and change index.js to use that header using our HOC withMaterialUI
```
import Header from '../components/header';
import withMaterialUI from '../shared/MaterialUI/withMaterialUI';

const Index = ({ title = 'Hello from our Theodo Next.js tutorial' }) => (
  <div>
    <Header />
    <h1>{title}</h1>
  </div>
);

export default withMaterialUI(Index);

```

![First Material UI Result](assets/2.png?raw=true "First Material UI Result")


Populate our application with data using Async/Await

![API Data Result](assets/3.png?raw=true "Theodo Github API Result")






