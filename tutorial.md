
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