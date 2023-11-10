Submitting Forms
================

Welcome back!

With all of the components that we’ve built in the last lessons, we can construct any type of component-powered forms. But what good is a form unless we can send the user’s input to be processed or stored in our backend?

In the current state of frontend development, the most common approach to submitting form data to our backend is through XMLHTTPRequests or XHR for short. We’ll talk about this more in a bit, but for now just know that Axios is a library that will allow you to create XHR requests without having to deal with the vanilla XHR JavaScript API, which is a bit cumbersome.

* * *

First things first
------------------

There’s some form basics we have to learn and understand before we dive into plugging Axios into our application to start making our XHR requests. Let’s dig into the defaults of forms behavior, and some fundamental accessibility concepts.

1.  The default behavior of a `form` in HTML is to send a bunch of data into a specified URL by triggering a browser navigation event. What this means is that without the use of a library like Axios, your HTML forms will _by default_ make the browser load a completely new page.

In the age of SPAs (Single Page Applications) where we want to provide a seamless navigation to the user without the constant full reloading of pages, this is not a great experience.

2.  Every. Single. Form. Needs a wrapping `<form>` tag. Screen readers often switch to “Forms Mode” when they are processing content within a `<form>` element. This gives users that use accessible technologies a better experience when navigating forms, and there is no reason to ever not do it.
    
3.  There are many ways to trigger the submission of a form. A user can click or tab into a submit button and click it or hit enter. A user may also hit the enter key inside one of our fields. Screen readers will look for buttons with the `type="submit"` type on them.
    

How then do we correctly capture the user’s attempt to submitting our form?

* * *

The submit event
----------------

Whenever a user submits a form within a wrapping `form` tag, this `form` tag emits a `submit` event that we can listen to — and even though it is common practice to set a `click` listener to the **submit** button of a form, this should be avoided.

Listening to the click event of a button within the `form` tag (and preventing its default behavior) effectively blocks the default behavior of HTML form, which is to submit this `submit` event, and afterwards send the data within said form. However, it does not cover us from other possible ways of submitting said form, such as the ones we have learned about earlier.

Let’s navigate into our `SimpleForm.vue`.

As you can see, we already have our submit button set up with the `type` of submit. Whenever a button with the type of `submit` that lives within a wrapping `form` element is clicked, it fires off that wrapping form’s submit event. This is the behavior that we want for our forms.

You may be wondering, what happens if we forget to add this `type` of `submit` to one of our buttons inside the form?

Browsers will assume that any buttons within `form` tags that don’t have a set `type` are meant to submit the form, however the extra specificity will help accessibility tools to identify it as the button meant to be used to submit our form.

If our form required other types of buttons, such as a **Cancel** button, for example, adding the specific `type="button"` to it will effectively stop it from triggering the form’s submit event.

We can now go to our `form` tag and start listening to the `@submit` event of our form.

**📃SimpleForm.vue**

    [...]
    <form @submit.prevent="sendForm">
    [...]
    

Notice that we are are using the event modifier `.prevent` on the `@submit` event listener. This modifier will call `preventDefault` on the submit _event_ for us, so that our `sendForm` method can focus exclusively on the logic of submitting our form, and not on event handling.

Setting `preventDefault` on a `submit` event for a form element will block the default behavior of having the form submit the data by itself and reloading the browser. We want to keep control on how our form is being processed, and we want to post our data using Axios — so preventing this default behavior is exactly what we need.

Now that we are listening to the event, let’s go ahead and add our new `sendForm` method inside the `script` section of our `SimpleForm.vue` component.

**📃SimpleForm.vue**

    <script>
    export default {
      data () { ... },
      methods: {
        sendForm () {
          // We will handle form submission here!
        }
      }
    }
    </script>
    

* * *

Setting up Axios and our API
----------------------------

In order to be able to capture our user’s input once they submit the form within our new `sendForm` method, we need to first get ourselves set up with both Axios—the library we discussed in the beginning of the lesson to make XHR requests—and an API endpoint where we can send our dummy data to.

Let’s start with getting Axios set up.

Head over to your terminal on the project root, and run the following command to add Axios to your project with your favorite package manager.

    npm install axios
    
    // OR
    
    yarn add axios
    

Once Axios is done installing, we can head over to the top of the `<script>` block in our `SimpleForm.vue` and import the library so we can make use of it later.

**📃SimpleForm.vue**

    <script>
    import axios from 'axios'
    
    export default {
      [...]
    }
    </script>
    

Now that we have Axios set up, we need to create an endpoint that will receive our data once the user has posted the form. In a real world scenario, this endpoint will usually be provided by the backend of your application, or by a third-party service such as FireBase.

For the sake of learning, we are going to be using “My JSON Server” ([https://my-json-server.typicode.com/](https://my-json-server.typicode.com/)) - you will already be familiar with this if you’ve taken our Real World Vue 3 course - a free way to create our own fake online endpoint so that we can learn how to post our form.

Setting up for your own Github repos is really easy, just go ahead and follow the instructions on their website and add a `db.json` file to the `main` or `master` branch of your repo, then you can access and use it through the URL structure that they provide as a REST API endpoint.

I’ve already gone ahead and created this for us, so that we can use it with the course’s repo here on Vue Mastery. The URL for our REST API is: [https://my-json-server.typicode.com/Code-Pop/Vue-3-Forms/](https://my-json-server.typicode.com/Code-Pop/Vue-3-Forms/)

When you open the server, you will notice that under resources we have `events` listed, with the following URL: [https://my-json-server.typicode.com/Code-Pop/Vue-3-Forms/events](https://my-json-server.typicode.com/Code-Pop/Vue-3-Forms/events)

This is our API endpoint for events. I will not go into details of what a REST API is because that is completely out of the scope of this course - but unless you are a full-stack developer and are going to be creating the REST API on your own, the most important thing that you need to know at this moment is that we can grab this URL and make XHR requests to it.

In particular, when saving the data from a form you will almost always want to make a particular type of request - a `POST` request, which allows us to send a chunk of data back to the server with it.

Let’s swing back to the code and take a look at how to achieve sending our form’s data to the REST API with Axios.

We are going to add an Axios POST call to our `sendForm` method.

**📃SimpleForm.vue**

    sendForm (e) {
      axios.post(
        'https://my-json-server.typicode.com/Code-Pop/Vue-3-Forms/events', 
        this.event
      )
        .then(function (response) {
          console.log('Response', response)
        })
        .catch(function (err) {
          console.log('Error', err)
        })
    }
    

Since we already imported Axios, we can access its `post` method directly from the `axios` object.

The first argument that it requires is the URL onto where its going to send the information, in this case, the one provided to us for the `events` endpoint of our my-json-server mock API.

The second argument is the payload, an object containing all the information we want to send over to our server. Since we already have all the form’s information neatly wrapped into our `event` data object, we can make use of it directly here and just send it as is to the server.

Be warned! In a real life scenario you will want to validate your users input _before_ sending it to theserver _even_ if your backend is going to perform server-side validation. Doing a pre-validation on the frontend, or client-side-validation, is a highly recommended practice.

In general, any type of pre-checks or UX changes you want to make while the form is posting should be done here in the `sendForm` method. Displaying a loading spinner, or changing the text of the submit button to “Sending” are a few nice UX tricks you can leverage at this state.

Validations and UX are out of the scope of this lesson though, so we are going to skip them for now, and revisit in another course for more advanced form topics.

An important thing to know is that Axios methods return JavaScript `Promises`. This means that we can append a `then` method to our `post` call that will return the `response` from the server for us to work with — and a `catch` method that we can use to handle any errors that may occur when sending the data.

For the purposes of this lesson, we are only going to console log the results here to see both of these methods in action.

* * *

Back to the browser
-------------------

Now that our submit function is ready, and after we’ve learned all that theory, we are finally ready to go back to the browser and test out our form.

Serve your project and fill out the form, make sure you have the `network` tab open in your browser so you can see the requests going out — and finally hit that submit button to see all the information swiftly sent to our API.

In the network tab we can see our successful request and the response, mirroring our payload:

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1611767281120.jpg?alt=media&token=a250c27b-8362-4bed-b0f9-2eb6400ee5b3](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1611767281120.jpg?alt=media&token=a250c27b-8362-4bed-b0f9-2eb6400ee5b3)

And in the console, our log with the full response data:

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1611767281121.jpg?alt=media&token=a4225f9d-92a6-42c5-8728-80b0d692457d](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1611767281121.jpg?alt=media&token=a4225f9d-92a6-42c5-8728-80b0d692457d)

* * *

Wrapping up
-----------

That was a lot of theory! But we now have a fully functional form that submits data to our server.

There’s one more thing though — arguably one of the most important bits of all.

A few lessons ago I mentioned that our components were not accessible, and while in a real-life scenario accessibility should be a main consideration _while_ developing your components, it simply was too much information to put on top of our previous lessons.

We’re now ready to circle back and take a look at some basic accessibility concepts that you should always keep in mind when building forms in Vue.

See you there!

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/Vue-3-Forms/tree/l8-start)
    
*   [Ending Code](https://github.com/Code-Pop/Vue-3-Forms/tree/l8-end)
