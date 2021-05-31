---
layout: post
title:  "Example - getting entity content"
date:   2021-05-29 22:31:18 -0500
categories: php code
---

This post briefly outlines how to get content entities from the database via entity entityQuery and output them in a list via a custom controller, custom template, and array variable.

First, we need to set up a custom module (called test) in /modules/custom/test.  Then, set up the info file called test.info.yml, and put this in it:

```
name: Test Module
description: Test some example code.
package: Custom

type: module
core: 8.x
```

Here is a theme hook with a test_var variable to be used in the template called my-template.html.twig:

{% highlight php %}
function test_theme($existing, $type, $theme, $path) {
  return [
    'my_template' => [
      'variables' => [
        'test_var' => [],
      ],
    ],
  ];
}
{% endhighlight %}

Then, in a controller class in src/Controller/TestController.php

{% highlight php %}

namespace Drupal\test\Controller;

use Drupal\Core\Controller\ControllerBase;

/**
 * Defines TestController class.
 */
class TestController extends ControllerBase {

  /**
   * Get a list of articles.
   *
   * @return array
   *   Return array of articles.
   */
  public function getArticles() {
    // DB query to get articles or other content
    $entity_type = 'node';
    $query = \Drupal::entityQuery($entity_type);
    $query->condition('status', 1);
    $query->condition('type', 'article');
    $article_list = $query->execute();

    // build list of rendered articles
    $article_nodes = [];
    $view_mode = 'default';
    $view_builder = \Drupal::entityTypeManager()->getViewBuilder($entity_type);
    $storage = \Drupal::entityTypeManager()->getStorage($entity_type);
    foreach ($article_list as $nid) {
      $node = $storage->load($nid);
      $build = $view_builder->view($node, $view_mode);
      $output = render($build);
      array_push($article_nodes, $output);
    }

    return $article_nodes;
  }

  /**
   * Display the markup.
   *
   * @return array
   *   Return markup array.
   */
  public function content() {
    return [
     '#theme' => 'my_template',
     '#test_var' => $this->getArticles(),
   ];
  }

}
{% endhighlight %}

Finally, in templates/my-template.html.twig, output the variable in a manner similar to this:

```
{% raw %}
<p>
{% for item in test_var %}
  <div class="item-wrap">{{ item }}</div>
{% endfor %}
</p>
{% endraw %}
```
