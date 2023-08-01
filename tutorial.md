
## Objectives

- This tutorial teaches you how to do the following:
    - Build an Angular form with a component and template
    - Use `ngModel` to create two-way data bindings for reading and writing input-control values
    - Provide visual feedback using special CSS classes that track the state of the controls
    - Display validation errors to users and conditionally allow input from form controls based on the form status
    - Share information across HTML elements using *template reference variables*


## Build a template-driven form

- Template-driven forms rely on directives defined in the `FormsModule`.

| DIRECTIVES      | DETAILS
| :-------------- | :---------------------------------------------------------
| `NgModel`       | Reconciles value changes in the attached form element with changes in the data model, allowing you to respond to user input with input validation and error handling.
| `NgForm`        | Creates a top-level `FormGroup` instance and binds it to a `<form>` element to track aggregated form value and validation status. As soon as you import `FormsModule`, this directive becomes active by default on all `<form>` tags. You don't need to add a special selector.
| `NgModelGroup`  | Creates and binds a `FormGroup` instance to a DOM element.



## Build the form

- You can recreate the sample application from the code provided here, or you can examine or download the live example / download example.
- https://angular.io/generated/zips/forms/forms.zip

1. The provided sample application creates the `Hero` class which defines the data model reflected in the form.
- **src/app/hero.ts**
```ts
export class Hero {

  constructor(
    public id: number,
    public name: string,
    public power: string,
    public alterEgo?: string
  ) {  }

}
```

2. The form layout and details are defined in the `HeroFormComponent` class.
- **src/app/hero-form/hero-form.component.ts**
```ts
import { Component } from '@angular/core';

import { Hero } from '../hero';

@Component({
  selector: 'app-hero-form',
  templateUrl: './hero-form.component.html',
  styleUrls: ['./hero-form.component.css']
})
export class HeroFormComponent {

  powers = ['Really Smart', 'Super Flexible',
            'Super Hot', 'Weather Changer'];

  model = new Hero(18, 'Dr. IQ', this.powers[0], 'Chuck Overstreet');

  submitted = false;

  onSubmit() { this.submitted = true; }

}
```
- The component's selector value of "app-hero-form" means you can drop this form in a parent template using the `<app-hero-form>` tag.


3. The following code creates a new hero instance, so that the initial form can show an example hero.
```ts
const myHero =  new Hero(42, 'SkyDog',
                       'Fetch any object at any distance',
                       'Leslie Rollover');
console.log('My hero is called ' + myHero.name); // "My hero is called SkyDog"
```

- This demo uses dummy data for `model` and `powers`. In a real app, you would inject a data service to get and save real data, or expose these properties as inputs and outputs.


4. The application enables the Forms feature and registers the created form component.
- **src/app/app.module.ts**
```ts
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';
import { CommonModule } from '@angular/common';
import { FormsModule } from '@angular/forms';

import { AppComponent } from './app.component';
import { HeroFormComponent } from './hero-form/hero-form.component';

@NgModule({
  imports: [
    BrowserModule,
    CommonModule,
    FormsModule
  ],
  declarations: [
    AppComponent,
    HeroFormComponent
  ],
  providers: [],
  bootstrap: [ AppComponent ]
})
export class AppModule { }
```


5. The form is displayed in the application layout defined by the root component's template.
- **src/app/app.component.html**
```html
<app-hero-form></app-hero-form>
```

- The initial template defines the layout for a form with two form groups and a submit button. The form groups correspond to two properties of the Hero data model, name and alterEgo. Each group has a label and a box for user input.
    - The **Name** `<input>` control element has the HTML5 required attribute
    - The **Alter Ego** `<input>` control element does not because alterEgo is optional

- The **Submit** button has some classes on it for styling. At this point, the form layout is all plain HTML5, with no bindings or directives.



6. The sample form uses some style classes from *Twitter Bootstrap*: **container**, **form-group**, **form-control**, and **btn**. To use these styles, the application's style sheet imports the library.

- **src/styles.css**
```css
@import url('https://unpkg.com/bootstrap@3.3.7/dist/css/bootstrap.min.css');
```


7. The form makes the hero applicant choose one superpower from a fixed list of agency-approved powers. The predefined list of `powers` is part of the data model, maintained internally in `HeroFormComponent`. The Angular `NgForOf directive` iterates over the data values to populate the `<select>` element.
- **src/app/hero-form/hero-form.component.html**
```html
<div class="form-group">
  <label for="power">Hero Power</label>
  <select class="form-control" id="power" required>
    <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
  </select>
</div>
```



## Bind input controls to data properties

- The next step is to bind the input controls to the corresponding `Hero` properties with two-way data binding, so that they respond to user input by updating the data model, and also respond to programmatic changes in the data by updating the display.

- The `ngModel` directive declared in the `FormsModule` lets you bind controls in your template-driven form to properties in your data model. When you include the directive using the syntax for two-way data binding, `[(ngModel)]`, Angular can track the value and user interaction of the control and keep the view synced with the model.

1. Edit the template file `hero-form.component.html`.
2. Find the `<input>` tag next to the **Name** label.
3. Add the `ngModel` directive, using two-way data binding syntax `[(ngModel)]="..."`.


- **src/app/hero-form/hero-form.component.html**
```html
<input type="text" class="form-control" id="name"
       required
       [(ngModel)]="model.name" name="name">
TODO: remove this: {{model.name}}
```

- This example has a temporary diagnostic interpolation after each input tag, `{{model.name}}`, to show the current data value of the corresponding property. The comment reminds you to remove the diagnostic lines when you have finished observing the two-way data binding at work.



### Access the overall form status

- When you imported the `FormsModule` in your component, Angular automatically created and attached an `NgForm` directive to the `<form>` tag in the template (because `NgForm` has the selector `form` that matches `<form>` elements).

- To get access to the `NgForm` and the overall form status, declare a *template reference variable*.

1. Edit the template file `hero-form.component.html`.

2. Update the `<form>` tag with a template reference variable, `#heroForm`, and set its value as follows.

- **src/app/hero-form/hero-form.component.html**
```html
<form #heroForm="ngForm">
```

- The `heroForm` template variable is now a reference to the `NgForm` directive instance that governs the form as a whole.

3. Run the app.

4. Start typing in the Name input box. As you add and delete characters, you can see them appear and disappear from the data model.



### Naming control elements

- When you use `[(ngModel)]` on an element, you must define a `name` attribute for that element. Angular uses the assigned name to register the element with the `NgForm` directive attached to the parent `<form>` element.

- The example added a `name` attribute to the `<input>` element and set it to "name", which makes sense for the hero's name. Any unique value will do, but using a descriptive name is helpful.

1. Add similar `[(ngModel)]` bindings and `name` attributes to **Alter Ego** and **Hero Power**.

2. You can now remove the diagnostic messages that show interpolated values.

3. To confirm that two-way data binding works for the entire hero model, add a new text binding with the `json` pipe at the top to the component's template, which serializes the data to a string. After these revisions, the form template should look like the following.

- **src/app/hero-form/hero-form.component.html**
```html
{{ model | json }}
<div class="form-group">
<label for="name">Name</label>
<input type="text" class="form-control" id="name"
        required
        [(ngModel)]="model.name" name="name">
</div>

<div class="form-group">
<label for="alterEgo">Alter Ego</label>
<input type="text"  class="form-control" id="alterEgo"
        [(ngModel)]="model.alterEgo" name="alterEgo">
</div>

<div class="form-group">
<label for="power">Hero Power</label>
<select class="form-control"  id="power"
        required
        [(ngModel)]="model.power" name="power">
    <option *ngFor="let pow of powers" [value]="pow">{{pow}}</option>
</select>
</div>
```

- Notice that each `<input>` element has an `id` property. This is used by the `<label>` element's `for` attribute to match the label to its input control. This is a standard HTML feature.

- Each `<input>` element also has the required `name` property that Angular uses to register the control with the form.

4. When you have observed the effects, you can delete the `{{ model | json }}` text binding.




### Track form states

- Angular applies the `ng-submitted` class to `form` elements after the form has been submitted. This class can be used to change the form's style after it has been submitted.




### Track control states

- Adding the `NgModel` directive to a control adds class names to the control that describe its state. These classes can be used to change a control's style based on its state.

- The following table describes the class names that Angular applies based on the control's state.

| STATES                            | CLASS IF TRUE  |	CLASS IF FALSE
| :-----------------------------    | :------------- | :-----------------
| The control has been visited.	    | `ng-touched`	 | `ng-untouched`
| The control's value has changed.	| `ng-dirty`     | `ng-pristine`
| The control's value is valid.	    | `ng-valid`     | `ng-invalid`

- Angular also applies the `ng-submitted` class to `form` elements upon submission, but not to the controls inside the `form` element.

- You use these CSS classes to define the styles for your control based on its status.



#### Observe control states

- To see how the classes are added and removed by the framework, open the browser's developer tools and inspect the `<input>` element that represents the hero name.

1. Using your browser's developer tools, find the `<input>` element that corresponds to the **Name** input box. You can see that the element has multiple CSS classes in addition to "form-control".

2. When you first bring it up, the classes indicate that it has a valid value, that the value has not been changed since initialization or reset, and that the control has not been visited since initialization or reset.

```html
<input … class="form-control ng-untouched ng-pristine ng-valid" …>
```

3. Take the following actions on the Name `<input>` box, and observe which classes appear.
    - Look but don't touch. The classes indicate that it is untouched, pristine, and valid.
    - Click inside the name box, then click outside it. The control has now been visited, and the element has the `ng-touched` class instead of the `ng-untouched` class.
    - Add slashes to the end of the name. It is now touched and dirty.
    - Erase the name. This makes the value invalid, so the `ng-invalid` class replaces the `ng-valid` class.



### Create visual feedback for states

- The `ng-valid`/`ng-invalid` pair is particularly interesting, because you want to send a strong visual signal when the values are invalid. You also want to mark required fields.

- You can mark required fields and invalid data at the same time with a colored bar on the left of the input box.

- To change the appearance in this way, take the following steps.

1. Add definitions for the `ng-*` CSS classes.
2. Add these class definitions to a new `forms.css` file.
3. Add the new file to the project as a sibling to `index.html`.

- **src/assets/forms.css**
```css
.ng-valid[required], .ng-valid.required  {
  border-left: 5px solid #42A948; /* green */
}

.ng-invalid:not(form)  {
  border-left: 5px solid #a94442; /* red */
}
```

4. In the index.html file, update the `<head>` tag to include the new style sheet.
- **src/index.html**
```html
<link rel="stylesheet" href="assets/forms.css">
```


### Show and hide validation error messages

- The **Name** input box is required and clearing it turns the bar red. That indicates that something is wrong, but the user doesn't know what is wrong or what to do about it. You can provide a helpful message by checking for and responding to the control's state.

- The Hero Power select box is also required, but it doesn't need this kind of error handling because the selection box already constrains the selection to valid values.

- To define and show an error message when appropriate, take the following steps.

1. Extend the `<input>` tag with a template reference variable that you can use to access the input box's Angular control from within the template. In the example, the variable is `#name="ngModel"`.

- The template reference variable (`#name`) is set to "ngModel" because that is the value of the `NgModel.exportAs` property. This property tells Angular how to link a reference variable to a directive.

2. Add a `<div>` that contains a suitable error message.

3. Show or hide the error message by binding properties of the name control to the message `<div>` element's `hidden` property.
- **src/app/hero-form/hero-form.component.html**
```html
<div [hidden]="name.valid || name.pristine"
     class="alert alert-danger">
```

4. Add a conditional error message to the `name` input box, as in the following example.
- **src/app/hero-form/hero-form.component.html**
```html
<label for="name">Name</label>
<input type="text" class="form-control" id="name"
       required
       [(ngModel)]="model.name" name="name"
       #name="ngModel">
<div [hidden]="name.valid || name.pristine"
     class="alert alert-danger">
  Name is required
</div>
```

- **ILLUSTRATING THE "PRISTINE" STATE**
- In this example, you hide the message when the control is either valid or *pristine*. Pristine means the user hasn't changed the value since it was displayed in this form. If you ignore the `pristine` state, you would hide the message only when the value is valid. If you arrive in this component with a new, blank hero or an invalid hero, you'll see the error message immediately, before you've done anything.

- You might want the message to display only when the user makes an invalid change. Hiding the message while the control is in the `pristine` state achieves that goal. You'll see the significance of this choice when you add a new hero to the form in the next step.



## Add a new hero

- This exercise shows how you can respond to a native HTML button-click event by adding to the model data. To let form users add a new hero, you will add a **New Hero** button that responds to a click event.

1. In the template, place a "New Hero" `<button>` element at the bottom of the form.

2. In the component file, add the hero-creation method to the hero data model.

- **src/app/hero-form/hero-form.component.ts**
```ts
newHero() {
  this.model = new Hero(42, '', '');
}
```

3. Bind the button's click event to a hero-creation method, `newHero()`.

- **src/app/hero-form/hero-form.component.html**
```html
<button type="button" class="btn btn-default" (click)="newHero()">New Hero</button>
```

4. Run the application again and click the **New Hero** button. The form clears, and the required bars to the left of the input box are red, indicating invalid name and power properties. Notice that the error messages are hidden. This is because the form is pristine; you haven't changed anything yet.

5. Enter a name and click **New Hero** again. Now the application displays a `Name is required` error message, because the input box is no longer pristine. The form remembers that you entered a name before clicking **New Hero**.

6. To restore the pristine state of the form controls, clear all of the flags imperatively by calling the form's `reset()` method after calling the `newHero()` method.

- **src/app/hero-form/hero-form.component.html**
```html
<button type="button" class="btn btn-default" (click)="newHero(); heroForm.reset()">New Hero</button>
```

- Now clicking **New Hero** resets both the form and its control flags.



## Submit the form with *ngSubmit*

- The user should be able to submit this form after filling it in. The **Submit** button at the bottom of the form does nothing on its own, but it does trigger a form-submit event because of its type (`type="submit"`). To respond to this event, take the following steps.

1. Bind the form's `ngSubmit` event property to the hero-form component's `onSubmit()` method.

- **src/app/hero-form/hero-form.component.html**
```html
<form (ngSubmit)="onSubmit()" #heroForm="ngForm">
```

2. Use the template reference variable, `#heroForm` to access the form that contains the **Submit** button and create an event binding. You will bind the form property that indicates its overall validity to the Submit button's `disabled` property.

- **src/app/hero-form/hero-form.component.html**
```html
<button type="submit" class="btn btn-success" [disabled]="!heroForm.form.valid">Submit</button>
```

3. Run the application. Notice that the button is enabled —although it doesn't do anything useful yet.

4. Delete the **Name** value. This violates the "required" rule, so it displays the error message —and notice that it also disables the **Submit** button.
You didn't have to explicitly wire the button's enabled state to the form's validity. The FormsModule did this automatically when you defined a template reference variable on the enhanced form element, then referred to that variable in the button control.


### Respond to form submission

- To show a response to form submission, you can hide the data entry area and display something else in its place.

1. Wrap the entire form in a `<div>` and bind its `hidden` property to the `HeroFormComponent.submitted` property.

- **src/app/hero-form/hero-form.component.html**
```html
<div [hidden]="submitted">
  <h1>Hero Form</h1>
  <form (ngSubmit)="onSubmit()" #heroForm="ngForm">

     <!-- ... all of the form ... -->

  </form>
</div>
```

- The main form is visible from the start because the `submitted` property is false until you submit the form, as this fragment from the `HeroFormComponent` shows:

- **src/app/hero-form/hero-form.component.ts**
```ts
submitted = false;

onSubmit() { this.submitted = true; }
```

- When you click the **Submit** button, the `submitted` flag becomes true and the form disappears.


2. To show something else while the form is in the submitted state, add the following HTML below the new `<div>` wrapper.

- **src/app/hero-form/hero-form.component.html**
```html
<div [hidden]="!submitted">
  <h2>You submitted the following:</h2>
  <div class="row">
    <div class="col-xs-3">Name</div>
    <div class="col-xs-9">{{ model.name }}</div>
  </div>
  <div class="row">
    <div class="col-xs-3">Alter Ego</div>
    <div class="col-xs-9">{{ model.alterEgo }}</div>
  </div>
  <div class="row">
    <div class="col-xs-3">Power</div>
    <div class="col-xs-9">{{ model.power }}</div>
  </div>
  <br>
  <button type="button" class="btn btn-primary" (click)="submitted=false">Edit</button>
</div>
```

- This `<div>`, which shows a read-only hero with interpolation bindings, appears only while the component is in the submitted state.

- The alternative display includes an *Edit* button whose click event is bound to an expression that clears the `submitted` flag.

3. Click the *Edit* button to switch the display back to the editable form.