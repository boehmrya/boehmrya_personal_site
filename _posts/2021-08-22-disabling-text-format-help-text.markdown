---
layout: post
title:  "Disabling the Help Text for Text Formats on Formatted Text Fields"
date:   2021-08-22 22:31:18 -0500
---

This post includes a small code snippet showing how to hide the help text on formatted text fields.  By default, on a regular text field (not a long text field), Drupal will include the full help text for the selected text format directly below the field.  This can take up a lot of space on the form, and clutter things up in general.  In particular, if we have more than one formatted (regular) text field on the form, we may want to disable this help text in order to clean things up a bit.  The code below shows a simple way to do so.

First, go through the regular process of creating a custom module.  Then, include the following form_alter hook implementation, which includes a special function as the #after_build callback.  Note - the #after_build callback is only run if the widget is for a text field.

{% highlight php %}

/**
* Implements hook_field_widget_form_alter().
*/

function MODULE_NAME_field_widget_form_alter(&$element, FormStateInterface $form_state, $context) {
 if ($context['widget'] instanceof \Drupal\text\Plugin\Field\FieldWidget\TextfieldWidget) {
   $element['#after_build'][] = '_allowed_formats_remove_textarea_help';
 }
}


/**
* #after_build callback.
*/
function _allowed_formats_remove_textarea_help($form_element, FormStateInterface $form_state) {
 if (isset($form_element['format'])) {
   // All this stuff is needed to hide the help text.
   unset($form_element['format']['guidelines']);
 }

 return $form_element;
}
{% endhighlight %}
