# Tutorial

- [Tutorial](#tutorial)
- [Setup](#setup)
- [Initial Files](#initial-files)
  - [CSS styling](#css-styling)
- [Home Component](#home-component)



# Setup

Check node version

    node --version

Install latest Angular CLI

    npm install -g @angular/cli

Download src code: first-app

Project install

    npm install

Run and serve the app on localhost:4200

    ng serve

# Initial Files

package.json

package.json is used by npm (the node package manager) to run the finished app.

```json
{
  "name": "angular.dev",
  "version": "0.0.0",
  "scripts": {
    "ng": "ng",
    "start": "ng serve",
    "build": "ng build",
    "watch": "ng build --watch --configuration development"
  },
  "private": true,
  "dependencies": {
    "@angular/common": "^21.1.0-next",
    "@angular/compiler": "^21.1.0-next",
    "@angular/core": "^21.1.0-next",
    "@angular/forms": "^21.1.0-next",
    "@angular/platform-browser": "^21.1.0-next",
    "@angular/router": "^21.1.0-next",
    "rxjs": "~7.8.0",
    "tslib": "^2.3.0",
    "zone.js": "~0.16.0"
  },
  "devDependencies": {
    "@angular/build": "^21.1.0-next",
    "@angular/cli": "^21.1.0-next",
    "@angular/compiler-cli": "^21.1.0-next",
    "@types/node": "^16.11.35",
    "typescript": "~5.9.2"
  }
}
```
index.html

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <title>Default</title>
    <base href="/" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <link rel="icon" type="image/x-icon" href="favicon.ico" />
    <link
      href="https://fonts.googleapis.com/css2?family=Be+Vietnam+Pro:ital,wght@0,400;0,700;1,400;1,700&display=swap"
      rel="stylesheet"
    />
  </head>
  <body>
    <app-root></app-root>
  </body>
</html>
```

styles.css

```css
/* You can add global styles to this file, and also import other style files */
* {
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Be Vietnam Pro', sans-serif;
}
:root {
  --primary-color: #605DC8;
  --secondary-color: #8B89E6;
  --accent-color: #e8e7fa;
  --shadow-color: #E8E8E8;
}

button.primary {
  padding: 10px;
  border: solid 1px var(--primary-color);
  background: var(--primary-color);
  color: white;
  border-radius: 8px;
}
```

main.ts

```ts
import {bootstrapApplication, provideProtractorTestingSupport} from '@angular/platform-browser';
import {App} from './app/app';

bootstrapApplication(App, {providers: [provideProtractorTestingSupport()]}).catch((err) =>
  console.error(err),
);

```

app.ts

app.ts is the source file that describes the app-root component. This is the top-level Angular component in the app. A component is the basic building block of an Angular application. The component description includes the component's code, HTML template, and styles, which can be described in this file, or in separate files.

```ts
import {Component} from '@angular/core';

@Component({
  selector: 'app-root',
  imports: [],
  template: ` <h1>Default</h1> `,
  styleUrls: ['./app.css'],
})
export class App {
  title = 'default';
}
```

app.css

```css
:host {
  --content-padding: 10px;
}
header {
  display: block;
  height: 60px;
  padding: var(--content-padding);
  box-shadow: 0px 5px 25px var(--shadow-color);
}
.content {
  padding: var(--content-padding);
}

```

other files:

.angular has files required to build the Angular app.

.e2e has files used to test the app.

.node_modules has the node.js packages that the app uses.

angular.json describes the Angular app to the app building tools.

tsconfig.* are the files that describe the app's configuration to the TypeScript compiler.

## CSS styling

- :root (in styles.css) defines global CSS variables
- --shadow-color is defined there, so all components can use it
- :host defines variables on the component’s own element
- CSS variables cascade normally through Angular components
- box-shadow: var(--shadow-color) just reads that global value


# Home Component

    ng generate component home

app.ts

move template to own html

```ts
import { Component } from "@angular/core";
import { Home } from "./home/home";

@Component({
  selector: "app-root",
  imports: [Home],
  templateUrl: "./app.html",
  styleUrls: ["./app.css"],
})
export class App {
  title = "default";
}

```

app.html

add the app-home tag

```html
<main>
    <header class="brand-name">
        <img class="brand-logo" src="/public/logo.svg" alt="logo" aria-hidden="true" />
    </header>
    <section class="content">
        <app-home />
    </section>
</main>

```

And now the home component

home.ts

```ts
import { Component } from "@angular/core";

@Component({
  selector: "app-home",
  imports: [],
  templateUrl: "./home.html",
  styleUrls: [`./home.css`],
})
export class Home {}

```

home.html

```html
<section>
    <form>
        <input type="text" placeholder="Filter by city" />
        <button class="primary" type="button">Search</button>
    </form>
</section>  
```

home.css

```css
.results {
  display: grid;
  column-gap: 14px;
  row-gap: 14px;
  grid-template-columns: repeat(auto-fill, minmax(400px, 400px));
  margin-top: 50px;
  justify-content: space-around;
}
input[type="text"] {
  border: solid 1px var(--primary-color);
  padding: 10px;
  border-radius: 8px;
  margin-right: 4px;
  display: inline-block;
  width: 30%;
}
button {
  padding: 10px;
  border: solid 1px var(--primary-color);
  background: var(--primary-color);
  color: white;
  border-radius: 8px;
}
@media (min-width: 500px) and (max-width: 768px) {
  .results {
      grid-template-columns: repeat(2, 1fr);
  }
  input[type="text"] {
      width: 70%;
  }   
}
@media (max-width: 499px) {
  .results {
      grid-template-columns: 1fr;
  }    
}

```
