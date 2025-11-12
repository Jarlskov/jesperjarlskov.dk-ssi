---
title: 'OOP Cheatsheet'
date: 2016-09-15
---

This is a small OOP (Object oriented programming) cheatsheet, to give a quick introduction to some of the commonly used OOP terminology.

<pre language="php">
function returnSomething() {
    $something = 'something';

    return $something;
}

echo returnSomething();

class Something {
    protected $somethingElse = 'something else';

    public function returnSomethingElse() {
        return $this->somethingElse;
    }
}

$something = new Something();

echo $something->returnSomethingElse();
</pre>

<table>
<thead><tr><th>Line</th><th>Concept</th><th>Definition</th></tr></thead>
<tbody>
<tr>
<td>1</td><td>Function</td><td>A function is defined in the global namespace and can be called from anywhere.</td>
</tr>
<tr>
<td>2</td><td>Variable</td><td>The variable is enclosed in the function definition, so it can only be used by that function.</td>
</tr>
<tr>
<td>4</td><td>Variable access</td><td>Here the value contained in the variable is accessed.</td>
</tr>
<tr>
<td>7</td><td>Function call</td><td>This executes the function.</td>
</tr>
<tr>
<td>9</td><td>Class definition</td><td>A class is a blueprint that you can generate objects from. All new objects based on a class will start out with everything that has been defined in the class definition.</td>
</tr>
<tr>
<td>10</td><td>Property</td><td>A property is like a variable, but is accessible from the entire object it is defined in. Objects can have different <a href="http://php.net/manual/en/language.oop5.visibility.php">visibilities</a>.</td>
</tr>
<tr>
<td>12</td><td>Method</td><td>A method is a function defined inside a class. It is always accessible to all objects of the class, and depending on it's <a href="http://php.net/manual/en/language.oop5.visibility.php">visibility</a> it might, or might not, be accessible from outside the class.</td>
</tr>
<tr>
<td>13</td><td>Property usage</td><td>An object can reach it's own properties using the `$this-><PROPERTYNAME>` syntax.</td>
</tr>
<tr>
<td>17</td><td>Object instantiation</td><td>This is an object is created based on a class definition.</td>
</tr>
<tr>
<td>19</td><td>Method call</td><td>The method `Something::returnSomethingElse()` is called on the newly created object. The method has it's visibility set to "public", hence it can be called from outside the object itself.</td>
</tr>
<tr>
<td>21</td><td>Property access</td><td>This is how the property `Something::$somethingElse` is accessed from outside the object. But in this case the property has the visibility protected which means it can't be accessed from outside the object itself, hence this will cause PHP to fail.</td>
</tr>
</tbody>
</table>
