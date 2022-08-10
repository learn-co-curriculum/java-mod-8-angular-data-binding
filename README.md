# Angular Data Binding

## Learning Goals

- Use dynamic values in Angular.
- Describe the different ways of data binding in Angular.

## Data Binding in Angular

Let's consider the following sample `app.component.html` for the project we've
been working with:

```html
My Application is {{ title }}
```

That will produce a very simple output as follows:

![Angular Base](https://curriculum-content.s3.amazonaws.com/java-mod-8/ng-flatiron-angular-base.png)

As we saw in the previous section, the `{{ title }}` value came from
`app.components.ts`. In that sense, it's a somewhat "dynamic" value in that it's
not statically defined in the HTML view itself. And because it's defined in a
TypeScript file, we can imagine using the power of TypeScript to retrieve that
value from an API or a database to make it even more dynamic. However, it's
still a value that doesn't change once its initial value is assigned, meaning
that once the application loads and displays it, the user has no way to impact
its value.

Let's introduce a text field in our simple HTML, where we're going to ask the
user to enter the name of the application:

```html
Enter name of the application: <input type="text" />
<p>My Application is {{ title }}</p>
```

This will give you a page that looks like this:

![Angular Input](https://curriculum-content.s3.amazonaws.com/java-mod-8/ng-flatiron-angular-input.png)

You can now type text inside the input box, but nothing happens to the text you
display regardless of the value you enter in the input box.

To "bind" the value in the input box to the value in the Angular model, we're
going to use a feature of Angular called "directive". Directives allow us to add
custom HTML attributes to HTML files that Angular will recognize as instructions
and process accordingly.

The directive we want to use here is `ngModel`, which is used with the following
notation:

```html
Enter name of the application: <input type="text" [(ngModel)]="title" />
<p>My Application is {{ title }}</p>
```

When you make that change to your HTML, you will get an error from Angular
because we're missing some imports in order to be able to use this new Angular
feature.

In `app.module.ts`, we have the definition for `@NgModule`, where we our
components and imports for our Angular apps. We need to add the Angular Module
that allows us to deal with HTML forms. We do this in 2 steps:

1. Tell TypeScript about the FormsModule:
   `import { FormsModule } from '@angular/forms'`
2. Tell Angular about the FormsModule by adding to our `imports` in the module
   declaration: `imports: [ BrowserModule, FormsModule]`

Your new `app.module.ts` file now looks like this:

```typescript
import { NgModule } from "@angular/core";
import { BrowserModule } from "@angular/platform-browser";
import { FormsModule } from "@angular/forms";

import { AppComponent } from "./app.component";

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule, FormsModule],
  providers: [],
  bootstrap: [AppComponent],
})
export class AppModule {}
```

Now your application should compile, and you should be able to type values in
your text box and see the text below it change accordingly:

![Angular 2-way data binding](https://curriculum-content.s3.amazonaws.com/java-mod-8/ng-flatiron-angular-two-way-input.png)

## Data Binding with complex types

In the previous example, we saw that we can get a value from our model to our
view using a simple string. Let's now take a look at defining a more complex
value in our model and seeing how we can display it in our view.

Let's start by adding a list of `messages` to our `AppComponent` class in
`app.component.ts`:

```typescript
export class AppComponent {
  title = 'flatiron-angular';
  messages = [
    {
      sender: "Ludovic",
      message: "Latest message from Ludovic"
    },
    {
      sender: "Jessica",
      message: "Latest message from Jessica"
    }
  ];
```

Our `AppComponent` now has two properties: `title` and `messages`. Let's refer
to `messages` in our HTML view the same way we referred to `title` by adding the
following to our `app.component.html`:

```html
<p>Messages: {{messages}}</p>
```

This should add the following text to your application page:

```html
Messages: [object Object],[object Object]
```

This is because `messages` is an array of objects (i.e. a collection of complex
types), and our HTML page does not know how to handle those objects. Angular,
however, allows us to refer to any property of any object in our model, so we
can replace the previous line with the following simple line to display the
number of entries in the `messages` array:

```html
<p>Number of messages = {{ messages.length}}</p>
```

This should give you the following output:

```html
Number of messages = 2
```

We can also refer to any of the elements in the array and then refer to any of
the properties of that element. For example, let's access the first element (at
index 0) and access both its properties:

```html
<p>First message is from {{messages[0].sender}}: {{messages[0].message}}</p>
```

This should give you the following output:

```html
First message is from Ludovic: Latest message from Ludovic
```

Being able to refer to a single element is helpful, but I've now hardcoded a
specific index in my view, which is not very flexible. What if I wanted to loop
through all the elements in my `messages` array, so that the application would
automatically adapt based on the content of the array.

Angular has a directive for looping through a data structure: `*ngFor` - we can
use it like this:

```html
<div>
  <ul>
    <li *ngFor="let currentMessage of messages">
      {{currentMessage.sender}}: {{currentMessage.message}}
    </li>
  </ul>
</div>
```

Let's break down this code:

1. We create a container `div` for our list
2. We start an unnumbered list with the plain HTML tag `<ul>`
3. We create a single line item in the list with the plain HTML tag `<li>`
4. Then we ask Angular to loop through that single `<li>` item and create a new
   one for every entry in our `messages` array. The `let message of messages` is
   standard TypeScript notation, and `messages` can be any variable that exists
   in our model that is a collection.
5. Inside our `<li>` element, which is also our `for` loop, we have access to
   the current message through the `currentMessage` variable. The
   `currentMessage` variable gives us access to any of the properties of each
   message.

So now our HTML view in `app.component.html` has the following code in it:

```html
Enter name of the application: <input type="text" [(ngModel)]="title" />
<p>My Application is {{ title }}</p>
<p>Number of messages = {{ messages.length}}</p>
<p>First message is from {{messages[0].sender}}: {{messages[0].message}}</p>
<div>
  <ul>
    <li *ngFor="let currentMessage of messages">
      {{currentMessage.sender}}: {{currentMessage.message}}
    </li>
  </ul>
</div>
```

And the following output from our application looks like this:

```html
Enter name of the application: flatiron-angular My Application is
flatiron-angular Number of messages = 2 First message is from Ludovic: Latest
message from Ludovic Ludovic: Latest message from Ludovic Jessica: Latest
message from Jessica
```

## Property Binding

So far, we have seen 2 ways to get data from our Angular code into our HTML
view:

1. String interpolation, with the `{{ }}` notation
2. Two-way data binding, with the `ngModel` directive

There is a 3rd way - Property Binding. Let's consider for example that we want
to add a button to our page to allow the user to send a new message. Adding this
button to our previous page that displays the number of messages, as well as a
simple welcome message, would give us the following code:

```html
Enter name of the application: <input type="text" [(ngModel)]="title" />
<p>My Application is {{ title }}</p>
<p>Number of messages = {{ messages.length}}</p>

<div>
  <button>Send Message</button>
</div>

<p>First message is from {{messages[0].sender}}: {{messages[0].message}}</p>
<div>
  <ul>
    <li *ngFor="let currentMessage of messages">
      {{currentMessage.sender}}: {{currentMessage.message}}
    </li>
  </ul>
</div>
```

This gives us a button that doesn't do anything (we'll give it some life in the
next section), but for now let's go ahead and disable it, since it shouldn't
give the user the impression it's actually operational. For that, we'll use the
`disabled` attribute:

```html
<div>
  <button disabled>Send Message</button>
</div>
```

As before, the current limitation on this example is that the button is _always_
disabled. What if I wanted to control this `disabled` property from my code?
This is where data binding comes into plays.

First, let's jump back into our model in `app.component.ts` and introduce a
boolean that we can bind our `disabled` attribute to:

```typescript
export class AppComponent {
  title = "flatiron-angular";
  disableNewMessage = true;
  // additional properties...
}
```

Now we can bind our `disabled` property in our HTML code to the
`disableNewMessage` variable in our Angular code:

```html
<div>
  <button [disabled]="disabledNewMessage">Send Message</button>
</div>
```

Let's look at this code more closely:

1. Putting `[]` around the HTML attribute `disabled` tells Angular that we want
   to bind this property to a variable in our model
2. We can then specify which variable to bind to with the `=<expression>`
   notation

Note that here we use an actual variable for the expression
(`disableNewMessage`), but we could use any expression that evaluates to a
boolean value, such as a function that returns `true` or `false`.

You can validate that the `disabled` property is now dynamically set based on
the value of the `disableNewMessage` variable by simply manually changing that
value in your code and seeing that the button status changes accordingly. Let's
make this more fun, however, by actually changing the value in our code after
the application has initialized, so you can see it change without actually
changing your code.

For this, let's add a `constructor` to our `AppComponent` class and use it to
call an anonymous function on delay:

```typescript
export class AppComponent {
  title = 'flatiron-angular';
  disableNewMessage = true;

  // additional properties...

  constructor() {
    console.log("Iniating angular AppCompnent class");
    setTimeout(() => {
      this.disableNewMessage = !this.disableNewMessage;
    }
    , 2000)
  }
```

Now your button will be disabled when the application first loads, and then 2
seconds later it will become enabled.

## Event Binding - Reacting to User Actions

Now that we've added a button and enabled it (after 2 seconds) in the
application code, let's actually make it do something.

In plain HTML and JavaScript, this would be done with the `onClick` event that
JavaScript supports by default. With Angular, we will use Event Binding:

```html
<div>
  <button [disabled]="getDisableNewMessage()" (click)="onSendMessage()">
    Send Message
  </button>
</div>
```

Let's break this code down:

1. Event Binding uses the following notation: `(eventName)="eventAction"`
2. In this case, we are asking Angular to bind this button to the `(click)`
   event and telling it that when the user clicks on the button, we want to call
   a function named `onSendMessage`
3. The name of function can be anything, but by convention it usually starts
   with `on` to indicate that it's an event handler

Let's create this method in our `AppComponent` class in `app.component.ts`:

```typescript
  onSendMessage() {
    let message = {
      sender: "Michael",
      message: "New message from Michael"
    }
    this.messages.push(message);
  }
```

You have now set up code that brings together a couple of the Angular concepts
we've discussed thus far:

1. The "Send Message" button is disabled when the application is first
   initialized, and then enabled 2 seconds later, which demonstrates attribute
   binding
2. The application shows a list of messages based on the `messages` variables
   defined in your model, which demonstrates how to use String Interpolation
   with complex data types
3. When the button is clicked, the `onSendMessage()` function is called, which
   demonstrates Event Binding
4. When `onSendMessages` is called, a new message is added to your `messages`
   variable, which in turn causes your display of messages to be updated, which
   demonstrates that we now have the capability to respond to user input
5. We also have a text field associated with our `title` variable in our model,
   which demonstrates Angular's two-way data binding

This is exciting because you can start to see how we might put together code
that reads input from the user, reacts to it, and updates the application
display accordingly.
