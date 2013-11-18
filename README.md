XClick
=============

Predict and prevent x-clicks (jQuery plugin).
-------------------------

As of 2013/11/17, x-clicks happen in Internet Explorer *only*, at least from version 8 and above it seems.

What I call an "x-click" is actually a regular click event which is triggered in the unusual circumstances described below.

An x-click event is triggered on an element when this element is the closest common parent of two children elements, one of which having triggered a mousedown event, the other having triggered a mouseup event. So "x-click" stands for "a click spread across elements". See the demo file.

At some point in your code, you may feel x-clicks are not really clicks. The XClick jQuery plugin helps you catch these tricky x-click events so you can take action against them. There is currently no native way to detect or prevent x-clicks. Side note : only real mouse events trigger x-clicks, not events triggered by a script.

Explanations and how-to
-------------------------

Requires jQuery >=1.8

Just make this call : `$.XClick()`

The plugin will start listening to mouse events (mouse down and up) to predict upcoming x-clicks and then sets a handler to catch them when they fire. By default, x-clicks are immediately stopped from propagating.

***Warning*** : the plugin needs all mousedown and mouseup events to bubble up to the body tag. If for some reason you need to stop these events, then launch custom "mousedownSilent" and "mouseupSilent" events instead to keep the plugin working. See the demo file.

Options
-------------------------

Specify options in your calls : `$.XClick(options)`

- You might want to know when an x-click has been triggered. The `onTriggered` option lets you specify a callback function to achieve this.

This callback function is called in the context of the common ancestor element which triggered the click event, and receives a data object as its first parameter. This object has the following properties : `event` is the click event, `mousedownElement` is the HTML object which triggered the mousedown event, and `mouseupElement` (same).

- When an x-click is triggered, you might want to let it propagate (I'd be curious to know why, please share your use case). You can do that by having the onTriggered callback return true.

- You might want to turn the plugin off to stop listening to mouse events, this can be achieved with the `enable` option. Call `$.XClick({ enable: false })`

- By default, the plugin does nothing in browsers other than IE. To change this, set the `enableForAllBrowsers` option to true. It's mainly for debugging purposes.

The origin of x-clicks
-------------------------

Why do x-clicks exist and why only in IE ? It seems to be the only browser which implements this W3 directive :

"...in general should fire click and dblclick events when the event target of the associated mousedown and mouseup events is the same element with no mouseout or mouseleave events intervening, and should fire click and dblclick events on the nearest common ancestor when the event targets of the associated mousedown and mouseup events are different."
http://www.w3.org/TR/DOM-Level-3-Events/#events-mouseevent-event-order

Humble thoughts for browser and directive makers
-------------------------

The main problem with these x-click events is that it is hard to predict on which element they will be triggered. If the user presses the mouse button on an element and releases it on another one in the page, the click event is triggered on a third element that remains to be determined. It makes them very hard to listen to and catch if you need to.

Besides, if I wanted to listen for mousedown-and-up events accross two elements, I wouldn't rely on a click-listener on the common parent because this parent can change whenever I change the structure of my HTML.

Furthermore, one might not even consider them as real functional clicks, it's a question of definition. In my use case, I came accross this issue when using select2 which tries to emulate select fields, which do not trigger click events in these circumstances. Well, select2 just cannot keep this annoying click event from firing (not without a plugin like XClick), since it is fired outside of the HTML it generates.

Speaking of which, it is annoying that using preventDefault on the mousedown and/or mouseup events won't help you prevent x-clicks. Although regular clicks on a unique element rightfully also work this way, it would make some sense to work differently with x-clicks. Finally, x-click events have no special properties that differentiate them from regular click events, that would be a nice thing to add.

As far as I'm concerned, since x-clicks are so confusing, hard to use, have almost no real purpose but are real troublemakers, I'd like them gone or at least have them trigger a different type of event. What about disabling them in IE until the W3 makes a new stand on this ? Feel free to share your thoughts.