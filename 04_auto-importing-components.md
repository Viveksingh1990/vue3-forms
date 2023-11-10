Auto-importing components
=========================

In our last two lessons, I’ve mentioned that our components `BaseInput` and `BaseSelect` have been magically imported without having to add an `import` state or adding them globally. In this lesson, we finally unravel this mystery and take a look at what’s happening behind the scenes.

Often inside our Vue projects we create collections of components such as these that are the “base”, or building blocks, for other more complex components. In our case, we’re creating a collection of base components that can be used to build forms.

Manually importing each of them by using `app.component` can quickly get tedious and repetitive. Luckily for us there is an automatic way of doing so.

Let’s navigate to the file `main.js` and take a look at what’s happening there together.

📃**main.js**

    import { createApp } from 'vue'
    import App from './App.vue'
    import upperFirst from 'lodash/upperFirst'
    import camelCase from 'lodash/camelCase'
    
    const requireComponent = require.context(
      './components',
      false,
      /Base[A-Z]\w+\.(vue|js)$/
    )
    
    const app = createApp(App)
    
    requireComponent.keys().forEach(fileName => {
      const componentConfig = requireComponent(fileName)
    
      const componentName = upperFirst(
        camelCase(fileName.replace(/^\.\/(.*)\.\w+$/, '$1'))
      )
    
      app.component(componentName, componentConfig.default || componentConfig)
    })
    
    app.mount('#app')
    

First we import `createApp` from Vue, in order to create and later mount our application. We also import the `App.vue` component to set it as the entry point of our application.

Please note that this is not specific to auto-importing components, but part of the process for creating and mounting our Vue 3 application.

Finally, we import `upperFirst` and `camelCase` from [Lodash](https://lodash.com/), a JavaScript library that provides utility functions for common programming tasks.

*   [upperCase](https://lodash.com/docs/4.17.10#upperFirst) converts the first character of a string to upper case
*   [camelCase](https://lodash.com/docs/4.17.10#camelCase) converts a string to [camel case](https://en.wikipedia.org/wiki/CamelCase) (camelCase isWritten likeThis)

To actually be able to use `Lodash` in our project, we first need to add it to our application.

We’ll open up a terminal and run the following command on our project root.

    npm i --save lodash
    

Next up, we use Webpack’s function `require.context` to allow webpack to know that it needs to require all of the files in the `components` folder that start with the `Base` prefix. Notice that we are also allowing it to require both `.vue`and `.js` extensions.

**📃main.js**

    const requireComponent = require.context(
      './components',
      false,
      /Base[A-Z]\w+\.(vue|js)$/
    )
    

Next, we create our Vue application by calling `createApp` with our `App.vue` component reference, and store it in an `app` constant so that we can reference our app context later.

**📃main.js**

    const app = createApp(App)
    

Further down into the code we’re going to call `keys()` on our `requireComponent` to get an array of `fileName`s, which will contain the route to each of our “Base” components.

**📃main.js**

    // ['./BaseInput.vue, './BaseSelect.vue' ]'
    requireComponent.keys().forEach(fileName => {
    

We are going to loop through each of our components in this array. Ready?

First, we get the component object by using the filename to extract if from the array.

**📃main.js**

    const componentConfig = requireComponent(fileName)
    

Next, we use Lodash’s `upperFirst` and `camelCase` functions to ensure that both file names written like `BaseInput.vue` and `base-input.vue`, or even `baseInput.vue` get turned into `BaseInput.vue`.

The regular expression here will strip anything before the component name and the extension, leaving us with just the name of the component `BaseInput`.

**📃main.js**

    const componentName = upperFirst(
      camelCase(fileName.replace(/^\.\/(.*)\.\w+$/, '$1'))
    )
    

Finally, we use the component name that we just extracted and the component configuration that we got earlier to register the component as a global component.

**📃main.js**

    app.component(componentName, componentConfig.default || componentConfig)
    

We wrap up the file by mounting our application into the element with an id of `app`.

**📃main.js**

    app.mount('#app')
    

* * *

Wrapping up
-----------

This bit of code may seem daunting at first, but once you add it to your application, you can forget about it and let it handle all the busywork of importing base components for you. You just need to be aware of what it’s doing, and to understand the benefits that it can bring to automatic component registration.

In our next lesson, we’ll keep on building components — this time, the `BaseCheckbox`.

See you there!

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/Vue-3-Forms/tree/l4-start)
    
*   [Ending Code](https://github.com/Code-Pop/Vue-3-Forms/tree/l4-end)
