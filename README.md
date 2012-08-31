# Linkable
A simple behavior for Yii allowing easy and consistent access to model URLs.
When you attach Linkable to your models, it's easy to write reusable code that
can generate valid URLs and links for those models without having to know which
controller action to link to and which anchor text to use. When linkable is
attached, the behavior makes createUrl() and createLink() available as methods
on the model. As their names suggest, createUrl() returns a URL that points to
the default controller action for the model and createLink() returns a link with
the appropriates URL and anchor text for that model.

# Installation
Extract the files to a folder in your application, e.g. protected/extensions/linkable
and make sure the contents of this directory are imported by specifying the following
in your main application config:
<pre>
    "import" => array(
        "ext.linkable.*"
    ),
    ...
</pre>

# Configuring ALinkable
To add the linkable behavior to a model, add the following to your model's behaviors()
method:
<pre lang="php">
    public function behaviors()
    {
        return array(
            "class" => "ALinkable",
            // set the main route to the controller for this model
            "controllerRoute" => "/moduleName/controllerName"
            // the default action to use when linking to models of this type
            "defaultAction" => "view"
            // the attributes to include in the URL for the model
            "attributes" => array("id"),
            // the template to use when creating the anchor text for links.
            // model attributes can be included in the anchor text by enclosing
            // the attribute name in brackets. By default attributes will be treated
            // as text and will be html encoded before being rendered. It is possible
            // to change this by specifying the format for an attribute preceded by a
            // colon, e.g. "{thumbnailUrl:image}" would display the attribute
            // "thumbnailUrl" formatted as an image, whereas "{someHtml:raw}" would
            // display the attribute "someHtml" without html enconding the value
            template: "{name}, {id:number}"
        );
    }

</pre>

The model can now be automatically linked to using the syntax:
<pre lang="php">
    $model->id = 123456;
    $model->name = "Firstname Lastname"

    echo $model->createUrl(); // http://example.com/moduleName/controllerName/view?id=123456
    echo $model->createUrl("update"); // http://example.com/moduleName/controllerName/update?id=123456
    echo $model->createUrl("view", array("greeting" => "hello world")); // http://example.com/moduleName/controllerName/view?id=123456&greeting=hello%20world

    echo $model->createLink(); // <a href="http://example.com/moduleName/controllerName/view?id=123456">Firstname Lastname, 123.456</a>
    echo $model->createLink("Some label"); // <a href="http://example.com/moduleName/controllerName/view?id=123456">Some label</a>
    echo $model->createLink("Some label", "update"); // <a href="http://example.com/moduleName/controllerName/update?id=123456">Some label</a>
    echo $model->createLink("Some label", array("update", "foo" => "bar")); // <a href="http://example.com/moduleName/controllerName/update?id=123456&foo=bar">Some label</a>
    echo $model->createLink("Some label", "view", array("class" => "test")); // <a class="test" href="http://example.com/moduleName/controllerName/view?id=123456">Some label</a>
</pre>
