# Linkable
A simple behavior for Yii allowing easy and consistent access to the URLs of
controller actions that relate to a particular model.
When linkable is attached, the behavior makes createUrl() and createLink() available as methods
on the model. As their names suggest, createUrl() returns a URL that points to
the default controller action for the model and createLink() returns a link with
the appropriates URL and anchor text for that model.

For example, if you have a User model that is primarily associated with UserController,
you can attach the ALinkable behavior to your User model, and use
    $user->createLink("view")
to retrieve a link to the view action on the UserController with the appropriate GET
parameters for that user. Linkable also takes care of generating the appropriate anchor text
for links, so it's easy to simply call $model->createLink() on any model with the behavior
attached and by default you should always get back a sensible link. This makes it possible
to write components that consume any kind of model and can always provide a user friendly way
to link back to the primary page for that model. For example you might have a commenting system
that allows commenting on any kind of model, and you want to be able to link back to the parent
model with the appropriate anchor text etc, ALinkable provides that in a consistent way.

Additionally, if you need to change your link strategy in future, perhaps tweaking anchor text or switching to
friendlier URLs relying on a different model attribute, you can simply change the behavior configuration
to have your settings applied site-wide.


# Installation
Extract the files to a folder in your application, e.g. protected/extensions/linkable
and make sure the contents of this directory are imported by specifying the following
in your main application config:
<pre lang="php">
    "import" => array(
        "ext.linkable.*",
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
            "ALinkable" => array(
                "class" => "ALinkable",
                // set the main route to the controller for this model
                "controllerRoute" => "/moduleName/controllerName",
                // the default action to use when linking to models of this type
                "defaultAction" => "view",
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
                "template" => "{name}, {id:number}",
            ),
        );
    }

</pre>

The model can now be automatically linked to using the syntax:
<pre lang="php">
    $model->id = 123456;
    $model->name = "Firstname Lastname";

    echo $model->createUrl(); // http://example.com/moduleName/controllerName/view?id=123456
    echo $model->createUrl("update"); // http://example.com/moduleName/controllerName/update?id=123456
    echo $model->createUrl("view", array("greeting" => "hello world")); // http://example.com/moduleName/controllerName/view?id=123456&greeting=hello%20world

    echo $model->createLink(); // &lt;a href="http://example.com/moduleName/controllerName/view?id=123456"&gt;Firstname Lastname, 123.456&lt;/a&gt;
    echo $model->createLink("Some label"); // &lt;a href="http://example.com/moduleName/controllerName/view?id=123456"&gt;Some label&lt;/a&gt;
    echo $model->createLink("Some label", "update"); // &lt;a href="http://example.com/moduleName/controllerName/update?id=123456"&gt;Some label&lt;/a&gt;
    echo $model->createLink("Some label", array("update", "foo" => "bar")); // &lt;a href="http://example.com/moduleName/controllerName/update?id=123456&foo=bar"&gt;Some label&lt;/a&gt;
    echo $model->createLink("Some label", "view", array("class" => "test")); // &lt;a class="test" href="http://example.com/moduleName/controllerName/view?id=123456"&gt;Some label&lt;/a&gt;
</pre>

# Doesn't this break MVC separation?

Yeah, kinda. It depends how you view (ha!) your models. If you're prepared to think
of models as resources, implicitly accessed via certain controllers (which is the case
in probably 90% of apps), then I think this is a pragmatic solution, if not the "purest".

