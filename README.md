## Steps Directive [wsSteps]

Demo: [https://wpdas.github.io/angular-ws-steps/](https://wpdas.github.io/angular-ws-steps/)

- Screenshots:

![Screenshot 1](https://user-images.githubusercontent.com/3761994/31696207-7efddffa-b38e-11e7-90a0-921ac3defebb.png)
![Screenshot 2](https://user-images.githubusercontent.com/3761994/31696212-841d8756-b38e-11e7-940a-baf889fe3768.png)

### Install:
 - Bower
```
bower install angular-ws-steps --save
```

- NPM
```
npm install angular-ws-steps --save
```

### How to use:

The following files must be used:

- Bower:
``` html
<link rel="stylesheet" href="bower_components/angular-ws-steps/src/steps.css">
<script src="bower_components/angular-ws-steps/src/steps.directive.js"></script>
```
- NPM:
``` html
<link rel="stylesheet" href="node_modules/angular-ws-steps/src/steps.css">
<script src="node_modules/angular-ws-steps/src/steps.directive.js"></script>
```

The component can be used in the same document as many times as long as the id of the steps is different. Otherwise a warning will be issued on the console.

If the requested step is larger than the enabled step, it will not be displayed.

When the enabled step is changed, if the current step is larger than it, the first step will be automatically selected.

Note: title, ref and across parameter from step directive are optional.

``` html
<steps step="3" enabled-step="3" id="idName">
  <step icon="icon_name" title="title" ref="idRef1"></step>
  <step icon="icon_name" title="title" ref="idRef2"></step>
  <step icon="icon_name" title="title" ref="idRef3"></step>
</steps>

<step-content id="idRef1">HTML content</step-content>
<step-content id="idRef2">HTML content</step-content>
<step-content id="idRef3">HTML content</step-content>
```

You can use optional parameter "colors" to set colors of steps in sequence: enabled-color, 
disabled-color and enabled-icon-color. Caution, in this mode, you only can use HEX colors type. 
If you want to use rgb, rgba or similar, use StepAPI.init() instance.
``` html
<steps step="3" enabled-step="3" id="idName" colors="#ff0000, #765fff, #0000ff">
  ...
</steps>
```

### Instance / Control
- The object can be controlled using the AngularJS bind or using an instance of the StepsAPI object as in the example below:

``` js
angular.module('app', ['wsSteps'])
.controller('AppCtrl', function(StepsAPI){
  var myNav = new StepsAPI.init('idName');
});
```

- Default colors:
  enabled-color (```css #1976D2;```), disabled-color(```css rgba(0,0,0,.38)```), enabled-icon-color(```css rgba(25, 118, 210, 0.70)```).
 To change default colors inject and use StepColors object like this:

 ``` js
angular.module('app', ['wsSteps'])
.controller('AppCtrl', function(StepsAPI, StepColors) {

  //(enabledColor, disabledColor, enabledIconColor)
  var stepColors = new StepColors('#ff0000', 'rgba(180, 76, 93, 0.70)', '#0000ff');
  var myNav = new StepsAPI.init('idName', stepColors);
});
```

You can made too:

``` js
angular.module('app', ['wsSteps'])
.controller('AppCtrl', function(StepsAPI) {
  var myNav = new StepsAPI.init('idName');
  myNav.onReady(function() {
    myNav.setEnabledColor('#ff0000');
    myNav.setDisabledColor('rgba(180, 76, 93, 0.70)');
    myNav.setEnabledIconColor('#0000ff');
  });
});
```

- You can pass an Object to the Step using "across" parameter and later, rescues it using ```
myNav.getStepAcrossObject(myNav.getStep());``` Something like:

``` html
<steps step="2" enabled-step="2" id="idName">
  <step icon="icon_name" title="title" ref="idRef1" across="vm.myFunction"></step>
  <step icon="icon_name" title="title" ref="idRef2" across="vm.myObject"></step>
</steps>

<script>
angular.module('app', ['wsSteps'])
.controller('AppCtrl as vm', function(StepsAPI) {
  var vm = this;
  vm.myFunction = function() {...};
  vm.myObject = {name: 'User Name'};
  
  var myNav = new StepsAPI.init('idName');
  myNav.onStepChange(function() {
    console.log(myNav.getStepAcrossObject(myNav.getStep())); //Return across vm.myFunction / vm.myObject
  };
});
</script>
```

- To use the simple bind of AngularJS simply set the same in the desired parameter:

``` html
<div ng-controller="AppCtrl as vm">
  <steps step="{{vm.currentStep}}" enabled-step="{{vm.currentEnabledStep}}" id="idName">
    ...
  </steps>
</div>
```

- To control the component using StepsAPI just create an instance of it by passing the id of the html element as parameter:

``` js
.controller('AppCtrl', function(StepsAPI){
  var myNav = new StepsAPI.init('idName');

  //API events

  //Shot when every component (including child objects) has been drawn. This event should only be used if immediate use of the API is required
  myNav.onReady(function() {
    myNav.getElement(); //HTML element being controlled.
    myNav.getElementId(); //Element ID.
    myNav.getTotalSteps(); //Total steps of component/directive.
    myNav.getStep(); //Current step.
    myNav.setStep(1); //Change to Step passed by parameter.
    myNav.getEnabledStep(); //Current position of Enabled Step.
    myNav.setEnabledStep(4); //Sets a new position for Enabled Step.
    myNav.setEnabledColor('#ff0000'); //Sets enabled color.
    myNav.setDisabledColor('rgba(180, 76, 93, 0.70)'); //Sets disabled color.
    myNav.setEnabledIconColor('#0000ff'); //Sets enabled icon color.
    myNav.getStepAttributes(myNav.getStep()); //Return all attributes of the requested Step (current)
    myNav.getStepAttributes(myNav.getStep()).myParameter; //Return "my-parameter" from current Step (html example: <step ... my-parameter="value"></step>)
    myNav.getStepAcrossObject(myNav.getStep()); //Will return any object passed on "across" parameter on Step (Object, Function...)
    myNav.update() //Updates the step and enabled-step based on current value setted on HTML tag.
  });

  //When the Step is changed
  myNav.onStepChange(function() {
    console.log('Step Changes');
  });

  //When the Enabled Step is changed
  myNav.onEnabledStepChange(function() {
    console.log('Enabled Step Changes');
  });

  //When the Step can not be assigned due to the Enable Step lock.
  myNav.onStepChangeNotAllowed(function(err) {
    console.error(err);
  });

  //When AngularJS destroys myNav Controller (when it exits outside the Screen Context)
  myNav.onDestroy(function() {
    console.log('myNav was destroyed');
    myNav = null;
  })
});
```

### Dependencies:
- This component depends on the [Angular Material](https://material.angularjs.org/latest/) and its assets (icons) that must be loaded in a sequence similar to this:

```html
<!-- css -->
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto">
<link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Open+Sans">
<link rel="stylesheet" href="https://fonts.googleapis.com/icon?family=Material+Icons">
<link rel="stylesheet" href="/bower_components/angular-material/angular-material.css">

<!-- js -->
<script src="/bower_components/angular/angular.min.js"></script>
<script src="/bower_components/angular-aria/angular-aria.js"></script>
<script src="/bower_components/angular-animate/angular-animate.js"></script>
<script src="/bower_components/angular-material/angular-material.js"></script>
```

## Change Logs
#### Version 1.1.1 - 2017-10-24
- New API access method: getStepAcrossObject(step);

#### Version 1.1.0 - 2017-10-19
- New API access method: getStepAttributes(step);
- New Event: onDestroy(callback);
- Remove events automatically when the Step controller is destroyed.

See examples from API use in the above lines.

## License
License MIT.