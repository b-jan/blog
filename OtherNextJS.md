![3 logos](assets/logos.png?raw=true "Technologies we're gonna use")



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

