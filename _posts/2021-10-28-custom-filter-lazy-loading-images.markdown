---
layout: post
title:  "Creating a basic custom filter for lazy loading images"
date:   2021-10-28 22:31:18 -0500
---

In this post, for my future self and others, I outline a an example of a custom text filter for lazy loading images.  Specifically, the text filter prepares images for lazy loading by switching the values of the src and data-src attributes.  This offers a simplified version of the custom filter in the Lazy Load module.  I think it's helpful to provide this simple alternative for a few reasons:

- You want to use a different lazy loading library the the one the Lazy Load module uses (it uses lazysizes)
- You want to write your own script to lazy load images or iframes, rather than use a full library.
- You don't need all of the other features provided by the Lazy Load module, which includes a custom field formatter.

So to get started, after [creating a custom module](https://www.drupal.org/docs/creating-custom-modules), we'll first need to create the directory structure for a custom text filter.  In the root of the directory for your custom module, create the following directories: src/Plugin/Filter (this means, create the src directory in the root of the custom module, then create the Plugin directory inside the src directory, and then create the Filter directory inside the Plugin directory).  Inside the Filter directory, create a file called LazyLoadFilter.php.  This will hold the class (of the same name as the filename) for the custom filter.

I want to note that the code sample below pulls heavily from the custom text filter in the Lazy Load module, so I want to credit that module.  This just provides a reduced, simplified version of the that module's code.

See directly below for the code sample for the custom filter.  Essentially, the code creates a virtual dom with the Html::load method, and then uses the \DOMXPath native php class to query the dom for image elements.  The code then loops over the result set of image tag objects, and manipulates the attributes and classes to achieve the final result of adding the 'lazyload' class, and switching the src and data-src attribute values.


{% highlight php %}

namespace Drupal\custom_alter\Plugin\Filter;

use Drupal\Component\Utility\Html;
use Drupal\Core\Form\FormStateInterface;
use Drupal\filter\FilterProcessResult;
use Drupal\filter\Plugin\FilterBase;

/**
 * Provides a filter to lazy-load images.
 *
 * @Filter(
 *   id = "lazy_load_filter",
 *   title = @Translation("Lazy load images"),
 *   description = @Translation("Lazy-load images in activated text-formats."),
 *   type = Drupal\filter\Plugin\FilterInterface::TYPE_TRANSFORM_REVERSIBLE,
 *   settings = {
 *     "image" = TRUE,
 *   },
 *   weight = 20
 * )
 */

class LazyLoadFilter extends FilterBase {

  /**
   * {@inheritdoc}
   */
  public function process($text, $langcode): FilterProcessResult {

    $result = new FilterProcessResult($text);
    $html_dom = Html::load($text);
    $xpath = new \DOMXPath($html_dom);

    /** @var \DOMElement $node */
    foreach ($xpath->query('//img') as $node) {
      $classes = empty($node->getAttribute('class')) ?
        [] : explode(' ', $node->getAttribute('class'));

      // Get original source value.
      $src = $node->getAttribute('src');

      if ($node->tagName === 'img') {

        if (in_array('skip-lazy', $classes, TRUE)) {
          // Leave this node unchanged if it has the skip class
          continue;
        }

        // Add Lazysizes selector class name to element attributes.
        $classes[] = 'lazyload';
        $classes = array_unique($classes);
        $node->setAttribute('class', implode(' ', $classes));

        // Change source attribute from `src` to `data-src`
        $opt_src = 'data-src';
        $node->removeAttribute('src');
        $node->setAttribute($opt_src, $src);
      }
    }

    $result->setProcessedText(Html::serialize($html_dom));

    return $result;
  }

}

{% endhighlight %}
