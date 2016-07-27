# Get

### Basic case

Now that you've used `set()` to set some values, let's use `get()` to retrieve them!

```javascript
client
    .get('firstName')
    .then(function(firstName) {
        console.log('Successfully got the key firstName: ', firstName);
        // Would print: "Successfully got the key firstName: Victor"
    });
```

Get takes 1 argument, the key, and it returns a Promise. It has an optional
second argument which is an object to specify options for this retrieval.

The key must be a [string](misc.md). The value you get back from the resolution
of this promise will also be a string, it is up to you to convert it into any
specific type you need.

For example, if you had previously `set()` with an object, you'll need to
deserialize it to get your object back.

```javascript
client
    .get('user')
    .then(function(u) {
        var user = JSON.parse(u);
        console.log('Successfully got the object: ', user);
        // Would print: "Successfully got the object: { firstName: 'Victor', lastName: 'Quinn' }"
    });
```

### Callbacks

Memcache Plus will always return a [Promise](https://www.promisejs.org), but it
can also take a traditional callback for any of its methods so it can work just
like most of the other Memcache modules out there. For example:

```javascript
client.get('firstName', function(firstName) {
    console.log('Successfully gott the value for key firstName: ', firstName);
});
```

### No value

When there is no value set for a key, Memcache Plus will simply return `null` as
the value.

For example:

```javascript
client
    .get('keyThatDoesNotExist')
    .then(function(value) {
        console.log('The value is: ', value);
        // Would print: "The value is: null"
    });
```

### Compression

If an item was written with `set()` with compression enabled, you must specify
that fact when retrieving the object or it will not be decompressed by Memcache
Plus:

```javascript
client.get('firstName', { compressed: true })
    .then(function(firstName) {
        console.log('Successfully got the key firstName as compressed data: ', firstName);
        // Would print: "Successfully got the key firstName as compressed data: Victor"
    });
```
By enabling this option, every value will be compressed with Node's
[zlib](https://nodejs.org/api/zlib.html) library after being retrieved.

Notes:

1. If you store a value compressed with `set()` you have to `get()` it and
specify that it was compressed. There is no automatic inspection of values to
determine whether they were set with compression and decompress automatically
(as this would incur significant performance penalty)
1. Enabling compression will reduce the size of the objects stored but it will
also add a non-negligent performance hit to each `set()` and `get()` so use it
judiciously!
