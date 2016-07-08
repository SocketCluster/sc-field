# sc-field
Field model component for Polymer and SocketCluster

## Setup

```bash
bower install sc-field --save
```

You can include it inside your Polymer components like this (example):
```html
<link rel="import" href="/bower_components/sc-field/sc-field.html">
```

## Usage

See https://github.com/socketcluster/sc-sample-inventory for sample app which demonstrates this component in action.

### Using as a standalone component

In your DOM, you can add an sc-field tag anywhere inside your main Polymer <template></template> tags (example):

```html
<sc-field id="product-qty" resource-type="Product" resource-id="{{productId}}" resource-field="qty" resource-value="{{productQty}}"></sc-field>
```

To save the current value to the server, you can setup a separate input field with a handler like this
(in this case we will save the value when the input field's 'change' event triggers):

```html
<input type="text" value="{{productQty}}" on-change="saveValue" data-save-to="product-qty">
```

Then you need to add a save handler to your Polymer component instance:

```js
saveValue: function (event) {
  // This is the value from the data-save-to attribute from our input tag.
  // This is just to get a reference to our sc-field model instance.
  var targetModelFieldName = event.target.dataset.saveTo;
  var targetModelField = this.$[targetModelFieldName];

  // This will save the sc-field tag's current data to the server.
  targetModelField.update(event.target.value);
}
```

### Using as part of a collection:

This is what an sc-collection might look like:

```html
<sc-collection id="category-products" realtime="{{realtime}}" resource-type="Product" resource-value="{{categoryProducts}}" resource-view="categoryView" resource-view-params="{{paramsObject}}" resource-page-offset="{{pageOffsetStart}}" resource-page-size="{{pageSize}}" resource-count="{{itemCount}}"></sc-collection>

```

```html
<template is="dom-repeat" items="{{categoryProducts}}" filter="hasIdFilter" observe="id">
  <sc-field resource-type="Product" resource-id="{{item.id}}" resource-field="qty" resource-value="{{item.qty}}"></sc-field>
  <sc-field resource-type="Product" resource-id="{{item.id}}" resource-field="price" resource-value="{{item.price}}"></sc-field>
  <sc-field resource-type="Product" resource-id="{{item.id}}" resource-field="name" resource-value="{{item.name}}"></sc-field>
</template>
```

Here we are iterating over an array of ```categoryProducts``` which are in the form ```{id: '644e1dd7-2a7f-18fb-b8ed-ed78c3f92c2b'}``` (the ```categoryProducts``` array comes from the sc-collection component's ```resource-value``` attribute) and we are extending them with additional fields/properties ```qty```, ```price``` and ```name``` - This is how we can bind fields to specific items within a collection.
Note that the ```item``` object above represents the current element in the iteration over the ```categoryProducts``` array.
Also, note that there is no rendering logic here - These tags are just there to setup our realtime modeling layer.

Using the ```resource-value="{{item.qty}}"``` attribute, we are binding a RethinkDB document's qty property to each product's ```qty``` property on our frontend (in realtime).

## Supported attributes

The sc-field tag supports the following attributes:

- ```resource-type```: This is the model/table name in RethinkDB.
- ```resoure-id```: The ID of the document inside RethinkDB which this model will be bound to.
- ```resource-field```: The specific property/field of the document which this sc-field tag is bound to for read/update operations.
- ```resource-value```: The binding for the value from RethinkDB (updated in realtime) - This is the output of the component.

See https://github.com/SocketCluster/sc-crud-rethink for more details about schemas and views.

For details about sc-collection attributes, see https://github.com/SocketCluster/sc-collection
