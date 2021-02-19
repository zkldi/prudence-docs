Prudence Schema
====================

###############
Declaration
###############

A schema is just an object. You can create one as you would any other object.

.. code-block:: js

    let schema = {
        username: "string"
    };

The above schema declaration asserts that we expect an object with the key ``username``, and for that username
to also be a string.

###############
String Literal
###############

The first way of defining an expectation is to write a string. Strings are used by checking the ``typeof``
the given value equals the string.

The valid values for this can be found at `MDN <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/typeof#description>`_.

.. code-block:: js

    let schema = {
        username: "string",
        xp: "number",
        bigInt: "bigint"
    }

    // is valid
    let err = Prudence({ username: "zkldi", xp: 1234.567, bigInt: 123n }, schema);

    // is invalid: err2 is "[username] Expected typeof string. Received null."
    let err4 = Prudence({ username: null, xp: 1234.567, bigInt: 123n }, schema);

.. warning:: **Huge Exception**. For usefulness's sake, Prudence overasserts that typeof null === "null", and **NOT** "object".

----------------
Nullable Fields
----------------

You can prefix your strings with ``?``, which makes the field nullable. As an example,

.. code-block:: js

    let schema = {
        username: "?string"
    }

    // is valid.
    let err = Prudence({ username: "zkldi" }, schema);

    // is also valid.
    let err2 = Prudence({ username: null }, schema);

----------------
Optional Fields
----------------

You can also prefix your strings with ``*``, which makes the field optional. As an example,

.. code-block:: js

    let schema = {
        username: "*string"
    }

    // is valid.
    let err = Prudence({ username: "zkldi" }, schema);

    // is also valid.
    let err2 = Prudence({ }, schema);

    // is also valid (the two are essentially equivalent).
    let err3 = Prudence({ username: undefined }, schema);

You can use both of these by prefixing with ``*?``.

.. note:: The order must be ``*?``, ``?*`` will throw an error.

----------------
Error Messages
----------------

Prudence is able to automatically create meaningful error messages from this schema type.

The error message for this is as follows:

``[(location of error)]: Expected typeof (schemaValue) [or null, or no value]. Received (objectValue).``

#####################
Validation Functions
#####################

The other option inside a schema is to use a function. If a value in a schema is a function,
it is called with up to two arguments.

It is expected that this function returns a boolean value, whether the input is valid or not.

The first argument is the object's value at this same key.

.. code-block:: JS

    let schema = {
        age: (self) => Number.isSafeInteger(self) && self >= 18
    }

    // is valid!
    let err = Prudence({ age: 19 }, schema);

The second argument is the object the value was found inside. This is often not that useful,
but can be used for conditional validity, such as when one keys validity depends on another.

In the below example, we define a schema that checks if an objects ``end`` key is greater than
its ``start`` key.

.. code-block:: JS

    let schema = {
        start: "number",
        end: (self, parent) => typeof self === "number" && self > parent.start
    }

    // is valid.
    let err = Prudence({ start: 14, end: 190 }, schema);

    // is invalid, but we'll get to what err2 is in a second.
    let err2 = Prudence({ start: 14, end: 13 }, schema);

------------------------
Function Error Messages
------------------------

Prudence is **NOT** able to construct meaningful error messages from functions.

To enable Prudence to construct a meaningful error message, you attach the property ``errorMessage`` to the function.

As an example:

.. code-block:: JS

    let fn = (self) => self === "hello world!";

    fn.errorMessage = "Expected the string \"hello world!\"";

Given that this is rather cumbersome (and difficult in languages like TypeScript),
Prudence also provides a utility to create these functions.

.. code-block:: JS

    let fn = Prudence.createFn((self) => self === "hello world!", "Expected the string \"hello world!\"");

The two examples have identical results.

.. note:: These are referred to as Validation Functions in this documentation, and more information can be found here, at :ref:`validation_fn`.

The other way to pass an error message is to pass a third argument to Prudence - See :ref:`validator_errormessages`.

#####################
Nesting
#####################

Prudence is natively nestable, which means if you assign an object to a schema's value, it will simply work recursively.

.. code-block:: JS

    let schema = {
        a: {
            b: {
                c: {
                    "user-name": "string"
                }
            }
        }
    }

    // is valid!
    let err = Prudence({a:{b:{c:{"user-name": "zkldi"}}}}), schema);

    // Prudence also automatically provides meaningful information about where an error occured
    // even if nested!

    // "[a.b.c["user-name"]]: Expected typeof string. Received null."
    let err2 = Prudence({a:{b:{c:{"user-name": null}}}}, schema);

Prudence will throw an error if an object does not match the "shape" of the schema, as follows:

.. code-block:: JS

    let schema = {
        a: {
            b: {
                c: {
                    "user-name": "string"
                }
            }
        }
    }

    // "[a.b.c]: Object does not match structure of schema, expected this location to have an object."
    let err = Prudence({
        a: {
            b: {
                c: undefined
            }
        }
    }), schema);

.. note:: Prudence traverses the schema, not the object. You do not need to worry about deeply nested objects being passed.

#####################
Arrays
#####################

If a value in a schema is an array with a single value, Prudence will assume you want to validate an array
against that single value.

This is perhaps clearer with examples:

.. code-block:: JS

    let schema = {
        username: "string",
        aliases: ["string"]
    }

    // is valid
    let err = Prudence({ username: "zkldi", aliases: ["foo", "bar"] }, schema);

    let functionSchema = {
        username: "string",
        friendIDs: [(self) => Number.isSafeInteger(self)]
    }

    // is valid
    let err2 = Prudence({ username: "zkldi", friendIDs: [1,2,3,4] }, schema);

    let complexSchema = {
        username: "string",
        groupchats: [
            {
                name: "string",
                members: [Prudence.isInteger],
            },
        ]
    }

    // is valid
    let err3 = Prudence({
        username: "zkldi",
        groupchats: [
            {
                name: "the boys",
                members: [13, 14, 15],
            },
            {
                name: "the fellas",
                members: [13, 16, 17],
            }
        ]
    }, schema);

Prudence can also infer where an error occured inside an array, as follows:

.. code-block:: JS

    let schema = {
        username: "string",
        aliases: ["string"]
    }

    // [aliases[1]] Expected typeof string. Received null.
    let err = Prudence({ username: "zkldi", aliases: ["foo", null, "bar" ] }, schema);

################
Built-ins
################

Prudence comes with "built-in" functions that satisfy common use cases for validation.
You can see documentation for these at :ref:`builtins`.