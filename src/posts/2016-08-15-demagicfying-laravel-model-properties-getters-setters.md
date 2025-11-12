---
title: 'Demagicfying Laravel: Model properties; getters and setters'
date: 2016-08-15
---

I mostly enjoy working with Laravel. It provides a range of tools that makes it really fast to create a proof of concept of an idea, allowing you to test out your ideas without spending too much time on ideas before knowing if they are actually worth spending time on.

When you're starting out with Laravel a lot of what's going on behind the scene can feel like magic. This is really nice in the sense that you don't often have to worry about what is actually going on, but on the other hand, it can make things pretty hard to debug, it is not clear what is causing a bug when you're not sure what is going.

In this post I'll look into Eloquent models' object properties, and how Laravel's Eloquent ORM handles getting and setting property values.

<h2>Table of content</h2>
<ul>
<li><a href="#setting-up">Setting up</a></li>
<li><a href="#getters">Getting object properties</a></li>
<li><a href="#setters">Setting object properties</a></li>
<li><a href="#summary">Summary</a></li>
<li><a href="#pitfalls">Common pitfalls</a></li>
</ul>

<h2 id="setting-up">Setting up</h2>

To have an example to work with, we'll start by setting up a database table for a simple Todolist.

<pre lang="shell">
php artisan make:migration create_todolists_table --create=todolists
</pre>

The Todolist is pretty simple, it has a unique auto-incrementing ID, a string name, a text description, and Eloquent's default timestamps. The most interesting part of the migration file is the `up()` method, where we define the table.

<pre>
// Class definitions
public function up()
{
    Schema::create('todolists', function(Blueprint $table) {
        $table->increments('id');

        $table->string('name');
        $table->text('description');

        $table->timestamps();
    });
}
// The rest of the class
</pre>

Besides the migration, we need to create our Todolist model.

<pre lang="shell">
php artisan make:model Todolist
</pre>

This gives us a basic model class, that we can use to create Todolist objects. The full class looks like this:

<pre>namespace App;

use Illuminate\Database\Eloquent\Model;

class Todolist extends Model
{
}
</pre>

That's pretty much as bare bones as it gets.

<h2 id="setters">Setting object properties</h2>

<pre>
$list = new App\Todolist();
$list->name = 'My new list';
$list->description 'A list of important tasks';
</pre>

This saves the model object with its fancy new name and description. But how does this happen? How does Laravel know which properties to save, when the properties isn't even defined on the object? Let's look at our object:

<pre>
Todolist {#144
  #connection: null
  #table: null
  #primaryKey: "id"
  #keyType: "int"
  #perPage: 15
  +incrementing: true
  +timestamps: true
  #attributes: array:2 [
    "name" => "My new list"
    "description" => "A list of important tasks"
  ]
  #original: []
  #relations: []
  #hidden: []
  #visible: []
  #appends: []
  #fillable: []
  #guarded: array:1 [
    0 => "*"
  ]
  #dates: []
  #dateFormat: null
  #casts: []
  #touches: []
  #observables: []
  #with: []
  #morphClass: null
  +exists: false
  +wasRecentlyCreated: false
}
</pre>

We see that our data is set in an array named `$properties`, and not as standard object properties. Let's look into this.

We start by looking at our Todolist model. This is just an empty class, so nothing happens here. The Todolist class extends the Eloquent <code>Model</code>, so let's look at that one. In a standard Laravel application, you'll find it in

<pre>
vendor/laravel/framework/src/Illuminate/Database/Eloquent/Model.php
</pre>

Obviously our model specific properties aren't defined here either, since Laravel can't predict what we need, so something else is going on.

We can see that Laravel uses <a href="http://php.net/manual/en/language.oop5.magic.php">magic methods</a>, in this case the <code><a href="http://php.net/manual/en/language.oop5.overloading.php#object.set">__set()</a></code> magic method.

In PHP the <code>__set()</code> magic method is used as a catch all, that is called when trying to set an inaccessible object property, which means a property that either isn't defined, or that is inaccessible due to being defined with a protected or private scope.

The method in the Eloquent <code>Model</code> class is defined as:

<pre>
/**
 * Dynamically set attributes on the model.
 *
 * @param  string  $key
 * @param  mixed  $value
 * @return void
 */
public function __set($key, $value)
{
    $this->setAttribute($key, $value);
}
</pre>

<code>__set()</code> is passed 2 arguments, <code>$key</code> is the name of the property to be accessed, and <code>$value</code> is the value we're trying to set on the property.

When calling the code:

<pre>
$list->name = 'My new list';
</pre>

<code>$key</code> will have the value 'name', and <code>$value</code> will have the value 'My new list'.

In this case, <code>__set()</code> is only used as a wrapper for the <code>setAttribute()</code>-method, so let's have a look at that one.
<pre>/**
 * Set a given attribute on the model.
 *
 * @param  string  $key
 * @param  mixed  $value
 * @return $this
 */
public function setAttribute($key, $value)
{
    // First we will check for the presence of a mutator for the set operation
    // which simply lets the developers tweak the attribute as it is set on
    // the model, such as "json_encoding" an listing of data for storage.
    if ($this->hasSetMutator($key)) {
        $method = 'set'.Str::studly($key).'Attribute';

        return $this->{$method}($value);
    }

    // If an attribute is listed as a "date", we'll convert it from a DateTime
    // instance into a form proper for storage on the database tables using
    // the connection grammar's date format. We will auto set the values.
    elseif ($value && (in_array($key, $this->getDates()) || $this->isDateCastable($key))) {
        $value = $this->fromDateTime($value);
    }

    if ($this->isJsonCastable($key) && ! is_null($value)) {
        $value = $this->asJson($value);
    }

    $this->attributes[$key] = $value;

    return $this;
}
</pre>
This is an important part of the Laravel setter magic, so lets go through it step by step.

<pre>
if ($this->hasSetMutator($key)) {
    $method = 'set'.Str::studly($key).'Attribute';

    return $this->{$method}($value);
}
</pre>

The first thing that happens is a check whether a set mutator method exists for the given property.

In an Eloquent context a set mutator is an object method on the form <code>set[Property]Attribute</code>, so for our name attribute, that would be <code>setNameAttribute()</code>. If a method with that name is defined, Laravel will call it with our value. This makes it possible to define our own setter methods, overriding the standard Laravel behavior.

<pre>
elseif ($value && (in_array($key, $this->getDates()) || $this->isDateCastable($key))) {
    $value = $this->fromDateTime($value);
}
</pre>

If no setter method is defined, Laravel checks whether the property name is listed in the class' <code>$dates</code> array, or if it should be cast to a <code>Date</code> or <code>DateTime</code> object, according to the class' <code>$casts</code> property. If Laravel determines that the value is a <code>datetime</code> type, it will convert the value into a time string, to make sure it is safe to save the value to the database.

<pre>
if ($this->isJsonCastable($key) && ! is_null($value)) {
    $value = $this->asJson($value);
}
</pre>

The last check determines whether the value should be encoded as a JSON string in which case it is converted to a json string, to make sure it's ready to be saved to the database.

<pre>
$this->attributes[$key] = $value;
</pre>

As the last thing, the method saves the value, which may or may not have been cast to a different type, into the object's <code>$attributes</code> properties. This is an array where the object's current state is saved.

<h2 id="getters">Getting object properties</h2>

Now that we have an idea what's happening when setting properties, let's look into getting the data back out.

<pre>
var_dump($list->name);
</pre>

Produces the output:

<pre>
string(11) "My new list"
</pre>

Just like <code>__set($name, $value)</code> is PHP's fallback when trying to set a property that doesn't exist, PHP has a magic get method, called <code>__get($name)</code>. This method is called when trying to read the value of a property that hasn't been defined.

Again, our Todolist class doesn't have a <code>$name</code> property. Trying to read it will call PHP's <code>__set()</code> method, which again is defined on the base Eloquent <code>Model</code> class.

<pre>
/**
 * Dynamically retrieve attributes on the model.
 *
 * @param  string  $key
 * @return mixed
 */
public function __get($key)
{
    return $this->getAttribute($key);
}
</pre>

<code>Illuminate\Eloquent\Model::__set()</code> itself is just a wrapper for the class' <code>getAttribute()</code> method.

<pre>
/**
 * Get an attribute from the model.
 *
 * @param  string  $key
 * @return mixed
 */
public function getAttribute($key)
{
    if (array_key_exists($key, $this->attributes) || $this->hasGetMutator($key)) {
        return $this->getAttributeValue($key);
    }

    return $this->getRelationValue($key);
}
</pre>

<pre>
return $this->getRelationValue($key);
</pre>

The last part of the method relates to Eloquent relationships. I might go into this in another post, but I will skip it for this post.
<pre>
if (array_key_exists($key, $this->attributes) || $this->hasGetMutator($key)) {
    return $this->getAttributeValue($key);
}
</pre>

The interesting part of the method is a check for whether the property name exists in the object's <code>$attributes</code> property. This is where you'll find it if it's been set with the default Eloquent property setter, or if it has been loaded from a database.

If the property doesn't exist in the <code>$attributes</code> array, a check is made to see if a get mutator exists. Like Laravel setters are in the form <code>set[Property]Attribute()</code>, getters are in the form <code>get[Property]Attribute()</code>. Ie. when trying to read our <code>$name</code> property, Laravel will check for the existense of a method called <code>getNameAttribute()</code>.

<pre>
/**
 * Get a plain attribute (not a relationship).
 *
 * @param  string  $key
 * @return mixed
 */
public function getAttributeValue($key)
{
    $value = $this->getAttributeFromArray($key);

    // If the attribute has a get mutator, we will call that then return what
    // it returns as the value, which is useful for transforming values on
    // retrieval from the model to a form that is more useful for usage.
    if ($this->hasGetMutator($key)) {
        return $this->mutateAttribute($key, $value);
    }

    // If the attribute exists within the cast array, we will convert it to
    // an appropriate native PHP type dependant upon the associated value
    // given with the key in the pair. Dayle made this comment line up.
    if ($this->hasCast($key)) {
        return $this->castAttribute($key, $value);
    }

    // If the attribute is listed as a date, we will convert it to a DateTime
    // instance on retrieval, which makes it quite convenient to work with
    // date fields without having to create a mutator for each property.
    if (in_array($key, $this->getDates()) && ! is_null($value)) {
        return $this->asDateTime($value);
    }

    return $value;
}
</pre>

The <code>getAttributeValue()</code> works like a reverse version of the <code>setAttributeValue()</code> discussed earlier. It's main purpose being to get a stored value, cast it to something useful, and return it. Let's go through it step by step.

<pre>
$value = $this->getAttributeFromArray($key);
</pre>

The first thing that happens is that the property's value is fetched, if the property exists in the <code>$attributes</code> array.

<pre>
if ($this->hasGetMutator($key)) {
    return $this->mutateAttribute($key, $value);
}
</pre>

If the class has a property mutator, the property value is run through it, and returned.
<pre>
if ($this->hasCast($key)) {
    return $this->castAttribute($key, $value);
}
</pre>

If the property name is specified in the <code>$casts</code> array, the property's value is cast to the specified type and returned.

<pre>
if (in_array($key, $this->getDates()) && ! is_null($value)) {
    return $this->asDateTime($value);
}
</pre>

If the property is listed as a date property in the <code>$dates</code> array, the value is converted to a DateTime object, and returned.

<pre>
return $value;
</pre>

And lastly, if the property shouldn't be changed in any way, the raw value is returned.

<h2 id="summary">Summary</h2>
Eloquent uses PHP's magic <code>__get($name)</code> and <code>__set($name, $value)</code> methods to save and get data on model objects. During this process it provides a couple of ways to manipulate the data.

So far we've identified 3 ways to manipulate property values set on and gotten from Eloquent model objects.

<ul>
<li>Accessor and mutator methods</li>
<li>The <code>$casts</code> array</li>
<li>The <code>$dates</code> array</li>
</ul>

<h3>Accessor and mutator methods</h3>
The most flexible way to manipulate Eloquent data on getting and setting is using accessor and mutator methods. These and named on the form <code>get[Property]Attribute()</code> and <code>set[Property]Attribute()</code>.

<h4>Examples</h4>

<pre>
public function getNameAttribute($value)
{
    return ucfirst($value);
}
</pre>
    
<pre>
public function setNameAttribute($value)
{
    $this->attributes['name'] = strtoupper($value);
}
</pre>

<h3>The <code>$casts</code> array</h3>
The casts array is an array property where casts are specified for object properties. If no accessor or mutator is defined for a property, and it's specified in the <code>$casts</code> array Eloquent will handle casting the value.

<h4>Example</h4>
<pre>
protected $casts = [
    'deadline' => 'DateTime',
];
</pre>

<h3>The <code>$dates</code> array</h3>
Since it's very common to work with dates and times, Eloquent provides a very easy way to specify which properties should be cast as date objects.

<h4>Example</h4>
<pre>
protected $dates = [
    'deadline',
];
</pre>

By default, the properties will be cast to <code>Carbon</code> objects when getting the property.

<pre>
echo $list->deadline->format('Y-m-d H:i:s');
</pre>

<h2 id="pitfalls">Common pitfalls</h2>
I've seen a couple of common errors developers make when working with Eloquent getters and setters, that cause issues.

<ul>
<li>Defining the object properties</li>
<li>Forgetting to set <code>$attributes</code></li>
</ul>

<h3>Defining the object properties</h3>
Many OO programmers prefer to define their object properties in their class files, both to make it instantly visible which properties are available on class objects, and to allow PHP to make various optimizations. But since Laravel's Eloquent ORM relies on the magic PHP getter and setter methods, defining the class properties will make Eloquent unable to mutate the data, as well as preventing the data from being set in the <code>$attributes</code> array, preventing it from being saved to the database.

<pre>
use Illuminate\Database\Eloquent\Model;

class Todolist extends Model
{
    public $name;
}
</pre>

Defining the object property like this prevents Eloquent from saving the name attribute to the database.

<pre>
$list = new Todolist;
$list->name = 'New list';
$list->save();
</pre>

In this example the 'name' key doesn't exist in the <code>$attributes</code> array, hence it doesn't exist in the database.

<pre>
Todolist {#144
  +name: "My new list"
  #connection: null
  #table: null
  #primaryKey: "id"
  #keyType: "int"
  #perPage: 15
  +incrementing: true
  +timestamps: true
  #attributes: array:4 [
    "description" => "A new list of important tasks"
    "updated_at" => "2016-08-15 08:30:11"
    "created_at" => "2016-08-15 08:30:11"
    "id" => 3
  ]
  #original: array:4 [
    "description" => "A new list of important tasks"
    "updated_at" => "2016-08-15 08:30:11"
    "created_at" => "2016-08-15 08:30:11"
    "id" => 3
  ]
  #relations: []
  #hidden: []
  #visible: []
  #appends: []
  #fillable: []
  #guarded: array:1 [
    0 => "*"
  ]
  #dates: []
  #dateFormat: null
  #casts: []
  #touches: []
  #observables: []
  #with: []
  #morphClass: null
  +exists: true
  +wasRecentlyCreated: true
}
</pre>

<h3>Forgetting so set <code>$attributes</code></h3>
Another common pitfall is to override a mutator method to manipulate the property value, but forgetting to add the data to the <code>$attributes</code> array.

<pre>
public function setNameAttribute($value)
{
    return strtoupper($value);
}
</pre>

In this example the value will never be saved to the database, and cannot be read using an accessor.

<pre>
$list->name = 'My new list';
echo $list->name;
</pre>

The example will echo an empty string.