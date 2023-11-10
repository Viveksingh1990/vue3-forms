BaseRadioGroup
==============

In our last lesson we created our final base form component for this course, the `BaseRadio`.

However, we left off with a component that is completely flexible and reusable, but sometimes a little hard to comprehend. For someone that is not familiar with the inner working of `BaseRadio`, a group of them all pointing to the same `v-model` state can be confusing. Errors may also occur if one forgets to add a `name` attribute to a certain part of the group.

It would be better if we had a wrapping component that handled all the logic of managing the `v-model` connections for our user, so that they only have to remember to do it once - as they do in most others, and is easier to understand at a glance.

Enter the `BaseRadioGroup`!

As we mentioned in the last lesson, it doesn’t really make sense when radio buttons are by themselves, as they don’t really provide the user with _choice_, which is a key feature of radio buttons. So you almost always want to provide at _least_ two radios per each group, unless you’re doing something like this.

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1609784212903.jpg?alt=media&token=f04bd0ed-d4ba-4b9e-b970-94c8c688d9ce](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F1.1609784212903.jpg?alt=media&token=f04bd0ed-d4ba-4b9e-b970-94c8c688d9ce)

(Just kidding!)

Having said that, since we always want to have our `BaseRadio` elements grouped, it makes sense to create a component that contains the logic for this grouping: the `BaseRadioGroup`.

Let’s dive right in!

* * *

Creating our BaseRadioGroup
---------------------------

Let’s start by going into our components folder and creating a new file, `BaseRadioGroup.vue` which will house our new component. This time we won’t copy-paste code from the demo form since we will need to do a little bit more custom work.

**📃BaseRadioGroup.vue**

    <template>
      <div>
    
      </div>
    </template>
    
    <script>
    export default {
      
    }
    </script>
    

In order to get started, we need to create a prop that will allow the user of this component to pass an array of options that they want to let the user choose from. We also want to make sure that we use the data inside this new `options` prop to loop through the containing objects, and create a new `BaseRadio` component instance for each one.

The `options` property will be an array of objects, and we will want each of the objects inside to hold at least two properties: the `label` and the `value`. For example:

    const radioOptions = [
      { label: 'Gud boi', value: 'dog' },
      { label: 'Angri boi', value: 'cat' }
    ]
    

We will then use the `label` as each of our radio’s label, and the `value` as each radio’s value.

**📃BaseRadioGroup.vue**

    <template>
      <BaseRadio 
        v-for="option in options"
        :key="option.value"
        :label="option.label"
        :value="option.value"
      />
    </template>
    
    <script>
    export default {
      props: {
        options: {
          type: Array,
          required: true
        }
      }
    }
    </script>
    

Notice that we declare the `options` prop as required, since this component simply will not work without it.

As we learned in the last lesson, all radio groups are bound together as a group by the `name` property.

All of the radios in a group need to have the same `name` so that the browser knows that they ought to be grouped together.

So, let’s go ahead and add our second prop, the `name` prop, and make sure its correctly bound into our `BaseRadio` components.

**📃BaseRadioGroup.vue**

    <template>
      <BaseRadio 
        v-for="option in options"
        :key="option.value"
        :label="optio.label"
        :value="option.value"
        :name="name"
      />
    </template>
    
    <script>
    export default {
      props: {
        options: {
          type: Array,
          required: true
        },
        name: {
          type: String,
          required: true
        }
      }
    }
    </script>
    

Once more, we are declaring our prop as `required`, since without a name attribute the radio inputs won’t be grouped correctly.

* * *

Making it v-model capable
-------------------------

Now that we are correctly looping through our radio components, you probably know what’s coming next after building a number of these base form components. It’s time to allow for this component to handle `v-model` two-way binding. Like always, we’ll add the `modelValue` prop.

**📃BaseRadioGroup.vue**

    <template>
      <BaseRadio 
        v-for="option in options"
        :key="option.value"
        :label="option.label"
        :value="option.value"
        :modelValue="modelValue"
        :name="name"
      />
    </template>
    
    <script>
    export default {
      props: {
        options: {
          type: Array,
          required: true
        },
        name: {
          type: String,
          required: true
        },
        modelValue: {
          type: [String, Number],
          required: true
        }
      }
    }
    </script>
    

This time around we’re going to make the prop `required`, since radio groups without anything selected don’t make much sense; there’s not really a “nothing selected” state when using this type of form input.

Don’t forget to also bind the `modelValue` prop to each `BaseRadio` element in the loop!

Next, let’s start listening to the `update:modelValue` event of each of the `BaseRadio` elements and emit it right back to the parent of the `BaseRadioGroup`.

**📃BaseRadioGroup.vue**

    <template>
      <BaseRadio 
        v-for="option in options"
        :key="option.value"
        :label="option.label"
        :value="option.value"
        :modelValue="modelValue"
        :name="name"
        @update:modelValue="$emit('update:modelValue', $event)"
      />
    </template>
    

Let’s now go back to our `SimpleForm.vue` component and replace the “Are pets allowed?” section `BaseRadio` elements with a brand new `BaseRadioGroup`.

**📃SimpleForm.vue**

    <h3>Are pets allowed?</h3>
    <div>
      <BaseRadioGroup
        v-model="event.pets"
        name="pets"
        :options="petOptions"
      />
    </div>
    

We’re also going to add a new array `petOptions` to our component `data`. It will be bound to the `options` prop in our `BaseRadioGroup` for this set of radios.

**📃SimpleForm.vue**

    <script>
    export default {
      data () {
        return {
          categories: [...],
          event: [...],
          petOptions: [
            { label: 'Yes', value: 1 },
            { label: 'No', value: 0 }
          ]
        }
      }
    }
    </script> 
    

We can now go into the browser and bask in the glory of our not-very-nice-looking group of radios.

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1609784272607.jpg?alt=media&token=a6613f05-a42d-44d0-b9d0-d722da01912b](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F2.1609784272607.jpg?alt=media&token=a6613f05-a42d-44d0-b9d0-d722da01912b)

🤔looks like we’re going to have to do some extra work to make it space correctly!

* * *

Allowing different layouts
--------------------------

Before we replaced our `BaseRadio` components in `SimpleForm.vue` they were each wrapped in a div. Because of the block nature of div tags, it separated them each in their own line.

Our `BaseRadio` component is agnostic to presentation, it’s only an input and label tag in the end. It is our job as the users to determine how they must be presented in the screen.

For our `BaseRadioGroup` I would like to create the possibility of the radios being aligned both horizontally and vertically.

To keep it as simple to use as possible, all of this switching will be controlled by one single new prop, `vertical`. We will default the layout of radios in our `BaseRadioGroup` to horizontal, so this prop will be of type boolean, and default to `false`.

It is a very common practice when creating components to set props that are meant to toggle on and off (also known as “flags”) to a default of `false`. That allows us to be able to set them to “on” with a very clean syntax on the instance of the component. For example, with our `vertical` prop, if the user wants to have their group laid out vertically, they would simply add the keyword to the instance like so:

    <BaseRadioGroup
      v-model="event.pets"
      vertical
    />
    

Boolean props in Vue that are not specifically bound are assumed to be **true**. So in the above example, `vertical` is the same as `:vertical="true"`.

Let’s go back to `BaseRadioGroup.vue` and add our `vertical` prop and begin setting up for this new functionality.

**📃BaseRadioGroup.vue**

    <script>
    export default {
      props: {
        options: {
          type: Array,
          required: true
        },
        name: {
          type: String,
          required: true
        },
        modelValue: {
          type: [String, Number],
          required: true
        },
        vertical: {
          type: Boolean,
          default: false
        }
      }
    }
    </script>
    

Now, in order to use our `vertical` prop we have to wrap our `RadioGroup` loop in a component of its own. When the radios are vertical we want it to be a `div`, and when they are horizontal we will use a `span`.

There are of course many ways to solve this particular problem, but this solution in particular allows me to show you how to leverage the power of `<component :is>` for your dynamic form components! We will begin by wrapping everything up in a `<component>` element and moving the `v-for` loop onto it.

**📃BaseRadioGroup.vue**

    <template>
      <component
        v-for="option in options"
        :key="option.value"
        :is=""
      >
        <BaseRadio
          :label="option.label"
          :value="option.value"
          :modelValue="modelValue"
          :name="name"
          @update:modelValue="$emit('update:modelValue', $event)"
        />
      </component>
    </template>
    

(Don’t forget to also bring the `:key` binding along with the `v-for`)

Now we have to determine the logic for the `is` property of the `component` component. Whatever string we pass into there will be what Vue uses when rendering the component. So if we set the `is` property to `div`, this wrapper component will be a div, and if we set it to `span`, it will be of course a span.

**📃BaseRadioGroup.vue**

    <component
      v-for="option in options"
      :key="option.value"
      :is="vertical ? 'div' : 'span'"
    >
    

Since this is not very complex logic, we can get away with leaving it all in the template. Here we are checking if the `vertical` prop is _truthy_, then we set a `div` if not a `span` like we determined a bit ago.

Notice that both `'div'` and `'span'` are wrapped in single quotes, since they **must** be strings.

If we set the `vertical` flag for our `BaseRadioGroup` now on `SimpleForm.vue` and check it on the browser, everything seems to work as expected.

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F3.1609784222129.jpg?alt=media&token=eeb2c50b-fc5c-4b36-912f-178276c4ca5e](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F3.1609784222129.jpg?alt=media&token=eeb2c50b-fc5c-4b36-912f-178276c4ca5e)

However, there is one more adjustment we need to make for the horizontal layout. We need to add a bit of styling in order to separate the elements a bit.

Let’s add a `horizontal` css class, and apply it when the element is using a horizontal layout. Or in code terms, when not vertical.

**📃BaseRadioGroup.vue**

    <template>
      <component
        v-for="option in options"
        :key="option.value"
        :is="vertical ? 'div' : 'span'"
        :class="{
          horizontal: !vertical
        }"
      >
        <BaseRadio
          :label="option.label"
          :value="option.value"
          :modelValue="modelValue"
          :name="name"
          @update:modelValue="$emit('update:modelValue', $event)"
        />
      </component>
    </template>
    
    <script>
    [...]
    </script>
    
    <style scoped>
    .horizontal {
      margin-right: 20px;
    }
    </style>
    

Inside the `component` tag, we are setting a new `class` binding and using the object syntax to toggle the classes on and off.

Whenever the condition `!vertical` evaluates to `true` - so when vertical is `false`, we will want to apply our `horizontal` CSS class.

Let’s remove the `vertical` flag from our `BaseRadioGroup` from `SimpleForm.vue` and then check the browser one more time.

![https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F4.1609784283423.jpg?alt=media&token=62c55460-7ac2-4590-b3ee-c08e868ac89e](https://firebasestorage.googleapis.com/v0/b/vue-mastery.appspot.com/o/flamelink%2Fmedia%2F4.1609784283423.jpg?alt=media&token=62c55460-7ac2-4590-b3ee-c08e868ac89e)

Looks like everything is working nicely!

* * *

Wrapping up
-----------

With the completion of our `BaseRadioGroup` component we wrap up the creation of our base form components. Great job on making it all the way through!

In the next lesson, we will take a small turn and look at how we can handle form submitting correctly in Vue by leveraging the power of a widely used library for making HTTP requests: Axios.

See you there!

### Lesson Resources

##### Source Code:

*   [Starting Code](https://github.com/Code-Pop/Vue-3-Forms/tree/l7-start)
    
*   [Ending Code](https://github.com/Code-Pop/Vue-3-Forms/tree/l7-end)
