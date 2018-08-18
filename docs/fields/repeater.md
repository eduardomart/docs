---
title: Repeater field
---
Repeater field
==============

A special field type available as a field definition is the `repeater` field
type which allows you to compose an array-like structure of sub-fields within a
single field name.

## Basic Configuration:

The configuration of a repeating field set comprises the main field set name,
along with the definition of the sub fields.

```yaml
        features:
            type: repeater
            fields:
                repeattitle:
                    type: text
                repeatimage:
                    type: image
                    extensions: [ gif, jpg, png ]
                repeatcontent:
                    type: html
```

As you can see the field is configured with a type of `repeater` and then the sub-fields
are configured under the `fields` attribute. In general you can include any valid fields
within a repeater, there are a few that are not supported.

<p class="note"><strong>Note:</strong> Repeaters are useable with most of the
available field types, except for a few where it would get too complex, or where
it simply does not make sense to have more than one of. In short, do
<strong>not</strong> use <code>type: slug</code>, <code>type: block</code>,
<code>type: repeater</code> or <code>type: templateselect</code> as fields in
your repeater. These fields will not work as expected. </p>

You can drag-and-sort repeaters, but don't forget to save your record after sorting its repeaters.

If your repeater has one or more sub-fields with `type: text`, the first one will be used as the header of each individual repeater set. This is particularly useful when you need to sort your repeater sets and collapse them all prior to sorting them.

## Example usage in templates:

When you want to use repeaters in your templates you will need to iterate over
a set before you can access the individual fields.

The approach you use may vary slightly on whether you know the names of the
fields within each set or if you want to allow your template to iterate over
all the sub-fields without necessarily knowing the field names.

For instance if you just want to iterate over all sets and then all fields
within the set then the template code will look like this:

```twig
{% for feature in record.features %}
    {% for field in feature %}
        {{ field.fieldtype }}:
        {{ field }}
    {% endfor %}
{% endfor %}
```

In the example above using `{{ field }}` will just output the value of the
sub-field, if it is a text field such as like `text`, `html`, `textarea` or
`markdown`. Since these are sometimes more complex fields you can use
`{{ field|showimage }}` for images or `{{ dump(field.value) }}` to dump the
value regardless of type. In practice, you'll often want to use the techniques
described in the section below for individual fields to output the specific type
of field in the layout you require.

If you know the names of the fields you want to render then you can fetch a
field from the collection by name. For instance using the same example as above
but knowing that our repeat set comprises the individual sub-fields
`companyname`, `telephone` and `email` we can output the fields like this:

```twig
{% for company in record.companies %}
    <h2>{{ company.companyname }}</h2>
    <h4>Tel: {{ company.telephone }}</h4>
    <h4>Email: <a href="mailto:{{ company.email }}">{{ company.email }}</a></h4>
    <hr>
{% endfor %}
```

## Images and thumbnails

You can use the image- and thumbnail filters for images inside a repeater
contenttype, but the notation is slightly different. For example, if we have a
contenttype with a `sections` repeater, acces them like this:

```twig
{% for section in record.sections %}
    <img src="{{ section.repeatimage|image(1200, 800, "r") }}"
        alt="{{ section.repeatimage.title }}">
{% endfor %}
```

## Options

The field has three specific options:

* `limit`: Limit how many sets an editor is able to create. If you omit this
  setting, then an unlimited number of sets can be created. The configuration
  for that option looks like this:
* `initial`: How many empty sets are created initially. This defaults to 1
  but when a sub-field is marked as required you may want to set it to `0` to
  ensure that a user must manually add a set first.
* `collapsible`: Whether the separate sets of this Repeater are collapsible in
  the backend. This allows the editor to get a better overview, if there are
  multiple sets. The default is `true`, you can set it to `false` to disable
  this feature.

```yaml
        features:
            type: repeater
            limit: 3
            initial: 0
            collapsible: false
            fields:
                repeattitle:
                    type: text
                repeatimage:
                    type: image
                    extensions: [ gif, jpg, png ]
                repeatcontent:
                    type: html
```
