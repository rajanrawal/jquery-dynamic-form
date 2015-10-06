# Version 1.0 What's New ? #
jQuery Dynamic Form v1.0 add two long awaited features :
  * Nested forms duplication
  * Data injection for prefilled forms (with the new data options or the inject method when using nested forms)

See the PHP files to see how it works : http://code.google.com/p/jquery-dynamic-form/source/browse/trunk/index.php


## Backward compatibility ##
jQuery Dynamic Form v1.0 requires jquery version >= 1.4

Server side script developed along with jQuery Dynamic Form version prior to 1.0 won't be compatible starting with version 1.0.

The way jQuery Dynamic Form normalizes name attributes of form fields has been completely rewritten to ease the collect of data server side. Here is a PHP script example to collect data from jQuery Dynamic Form :

```
function formatForm($form, $level){
	foreach ($form as $key => $value) {
		if($level == -1){
			echo "\nForm $key\n";
		}
		if(is_array($value)){
			if($level > -1){
				echo str_repeat("\t", $level);
				echo "$key\n";
			}
			formatForm($value, $level+1);
		}else{
			if($level > -1){
				echo str_repeat("\t", $level);
			}
			echo "$key : $value\n";
		}
	}
}
formatForm($_POST, -1);
```



# The plugin #

Give the ability, to form users, to dynamically add fields based on an existing template **[Check the demo](http://sroucheray.org/blog/jquery-dynamic-form/)** (still valid but requires update with version 1.0).

The template is a part of the actual displayed form. It can be a single field or several fields along with their label. jQuery Dynamic Form takes care of normalizing names and ids :
  * Name attributes of each duplicated fields are normalize to ease the collect of data server side. They will be sent as nested arrays.
  * **id** s attributes are indented to make them uniq
  * Labels **for** attributes are updated with the right name in order to match the related field

For example, in a contact form, basically a simple phone field is set. The user can add several other phone numbers adding phone fields with a plus button.

jQuery Dynamic Form adopts different behaviors if the duplicate buttons ( plus & minus )
are inside the template or outside. [Easier to understand when looking at the demo](http://sroucheray.org/blog/samples/jquery-dynamic-form/)

```
$(template).dynamicForm(add, remove, option); 
```

Parameters are :
  * **template** : the node selector to duplicate
  * **add** : the node selector for the add link
  * **remove** : the node selector for the remove link
  * **options** : an object containing options
    * **data**  : A JSON based representation of the data which will prefill the form (equivalent of the inject method). See the PHP files to see how it works : http://code.google.com/p/jquery-dynamic-form/source/browse/trunk/index.php
    * **limit** : max number of duplicate fields authorized
    * **createColor** : for color effect when duplicating (requires jQuery UI Effects module)
    * **removeColor** : for color effect when removing duplicate (idem)
    * **duration** : duration of effect (idem)
    * **formPrefix** : (**new in v 1.0**) the prefix used to identify a form (if not defined will use normalized source selector)
    * **afterClone** : (**new in v 1.0**) a callback function called as soon as a duplication is done, this is useful for custom insertion (you can insert the duplicate anywhere in the DOM), inserting specific validation on cloned field
      * - this function will be passed the cloned element as a unique parameter
      * - return false if the cloned element should not be inserted
    * normalizeFullForm : (**new in v 1.0**) normalize all fields in the form (even outside the template) for better server side script handling (default true)


```
/* Wait for the document to be loaded in order to activate dynamic form */
$(document).ready(function(){
 /* The node with th id "duplicate" will be duplicated 
    when clicking on the node with the id "plus". 
    Clicking on the node with the id "minus" will delete 
    the node */

 $("#duplicate").dynamicForm("#plus", "#minus", {limit:5});


/* Same as before but with some color effects when duplicating
   or removing - requires jQuery UI Effects module */

 $("#duplicate2").dynamicForm("#plus2", "#minus2", 
   {
    limit:3, 
    createColor:'yellow',
    removeColor: 'red',
   }
 );

/* With a callback function and a form name defined */
 $("#duplicate3").dynamicForm("#plus3", "#minus3", 
   {
    formPrefix:"mainForm",
    afterClone:function(clone){
	console.log("I'm a clone", clone);
        //I won't be inserted in the DOM
        return false;
    }
   }
 );

});
```