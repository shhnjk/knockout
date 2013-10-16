---
layout: documentation
title: The "value" binding
---

### Purpose
The `value` binding links the associated DOM element's value with a property on your view model. This is typically useful with form elements such as `<input>`, `<select>` and `<textarea>`. 
	
When the user edits the value in the associated form control, it updates the value on your view model. Likewise, when you update the value in your view model, this updates the value of the form control on screen.

Note: If you're working with checkboxes or radio buttons, use [the `checked` binding](checked-binding.html) to read and write your element's checked state, not the `value` binding.

### Example
    <p>Login name: <input data-bind="value: userName" /></p>
    <p>Password: <input type="password" data-bind="value: userPassword" /></p>
    
    <script type="text/javascript">
	    var viewModel = {
			userName: ko.observable(""),        // Initially blank
			userPassword: ko.observable("abc"), // Prepopulate
	    };
    </script>

### Parameters

 * Main parameter
   
   KO sets the element's `value` property to your parameter value. Any previous value will be overwritten.
   
   If this parameter is an observable value, the binding will update the element's value whenever the value changes. If the parameter isn't observable, it will only set the element's value once and will not update it again later.   
   
   If you supply something other than a number or a string (e.g., you pass an object or an array), the displayed text will be equivalent to `yourParameter.toString()` (that's usually not very useful, so it's best to supply string or numeric values).
   
   Whenever the user edits the value in the associated form control, KO will update the property on your view model. KO will always attempt to update your view model when the value has been modified and a user transfers focus to another DOM node (i.e., on the `change` event), but you can also trigger updates based on other events by using the `valueUpdate` parameter described below.
   
 * Additional parameters 

   * `valueUpdate`
   
   	 If your binding also includes a parameter called `valueUpdate`, this defines additional browser events KO should use to detect changes besides the `change` event. The following string values are the most commonly useful choices:

   	 * `"keyup"` - updates your view model when the user releases a key
   	 * `"keypress"` - updates your view model when the user has typed a key. Unlike `keyup`, this updates repeatedly while the user holds a key down
   	 * `"afterkeydown"` - updates your view model as soon as the user begins typing a character. This works by catching the browser's `keydown` event and handling the event asynchronously.
   	 
   	 Of these options, `"afterkeydown"` is the best choice if you want to keep your view model updated in real-time. For example:
            
            <p>Your value: <input data-bind="value: someValue, valueUpdate: 'afterkeydown'" /></p>
            <p>You have typed: <span data-bind="text: someValue"></span></p> <!-- updates in real-time -->
            
            <script type="text/javascript">
                var viewModel = {
                    someValue: ko.observable("edit me")
                };
            </script>   

### Note 1: Working with drop-down lists (i.e., SELECT nodes)

Knockout has special support for drop-down lists (i.e., `<select>` nodes). The `value` binding works in conjunction with the `options` binding to let you read and write values that are arbitrary JavaScript objects, not just string values. This is very useful if you want to let the user select from a set of model objects. For examples of this, see [the `options` binding](options-binding.html) or for handling multi-select lists, see the documentation for [the `selectedOptions` binding](selectedOptions-binding.html).

You can also use the `value` binding with a `<select>` element that does not use the `options` binding. In this case, you can choose to specify your `<option>` elements in markup or build them using the `foreach` or `template` bindings. You can even nest options within `<optgroup>` elements and Knockout will set the selected value appropriately.

### Note 2: Updating observable and non-observable property values

If you use `value` to link a form element to an observable property, KO is able to set up a 2-way binding so that changes to either affect the other.

However, if you use `value` to link a form element to a *non*-observable property (e.g., a plain old string, or an arbitrary JavaScript expression), KO will do the following:

 * If you reference a *simple property*, i.e., it is just a regular property on your view model, KO will set the form element's initial state to the property value, and when the form element is edited, KO will write the changes back to your property. It cannot detect when the property changes (because it isn't observable), so this is only a 1-way binding.
   
 * If you reference something that is *not* a simple property, e.g., the result of a function call or comparison operation, KO will set the form element's initial state to that value, but it will not be able to write any changes back when the user edits the form element. In this case it's a one-time-only value setter, not an ongoing binding that reacts to changes.

Example:

    <!-- Two-way binding. Populates textbox; syncs both ways. -->
    <p>First value: <input data-bind="value: firstValue" /></p>

    <!-- One-way binding. Populates textbox; syncs only from textbox to model. -->
    <p>Second value: <input data-bind="value: secondValue" /></p>

    <!-- No binding. Populates textbox, but doesn't react to any changes. -->
    <p>Third value: <input data-bind="value: secondValue.length > 8" /></p>
    
    <script type="text/javascript">
	    var viewModel = {
			firstValue: ko.observable("hello"), // Observable
			secondValue: "hello, again"         // Not observable
	    };
    </script>

### Dependencies

None, other than the core Knockout library.