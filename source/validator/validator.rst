Default Validator
====================

The Prudence validator can be accessed by simply calling ``Prudence()`` with its arguments.

.. function:: Prudence(object, schema, [errorMessages, [options]])

    Validated the provided object against the provided schema.

    :param object object: The object to validate.
    :param object schema: The schema to validate against.
    :param object errorMessages: Error messages to use on error, defaults to ``{}``.
    :param object options: Options for the Prudence validator. See :ref:`validator_options`.

    :returns: A ``string`` error message on failure, and ``null`` on success.

.. warning:: Prudence returns ``null`` on success. To keep this clear, you **should** use Prudence
    like this:

    .. code-block:: JS

        let err = Prudence(/* ... */);

        if (err) { /* do error handling */ }

====================
Instanced Validator
====================

In the case that you want to always use a specific set of options while parsing, you can instantiate
your own validator with the following command.

.. code-block:: JS

    let myPrudenceValidator = new Prudence.Validator(myPreferedOptions);

    // Note: you can still pass an options param, all this accomplishes is changing the validator defaults.
    let err = myPrudenceValidator.validate(object, schema, errorMessages, options);

.. _validator_errormessages:

========================
Error Message Overrides
========================

The third argument to Prudence is ``errorMessages``, and it expects an object with identical structure to
the schema, containing error messages.

This can be used to override existing error messages, or to provide them for functions
(Although the alternative of Validation Functions is prefered).

.. code-block:: JS

    let schema = {
        username: "string"
    }

    let errMsgs = {
        username: "Custom Error Message Here"
    }

    // err is "[username] Custom Error Message Here. Received null." - Note the automatic prefixing and suffixing of information.
    let err = Prudence({ username: null }, schema, errMsgs);


.. _validator_options:

=================
Options
=================

The below table indicates the options available for Prudence. The defaults are in brackets.

.. list-table::
    :widths: 25 25 50
    :header-rows: 1

    *   - Key
        - Type (Default)
        - Description
    *   - ``allowExcessKeys``
        - ``boolean (false)``
        - Whether or whether not to allow excess keys on the provided object, such as { foo, bar } being validated by schema { foo }.
    *   - ``throwOnNonObject``
        - ``boolean (true)``
        - Whether or whether not to throw an error if a non-object is provided as the object parameter (such as accidentally sending ``undefined``).

====================
Default Options
====================

The default options for Prudence can be accessed at ``Prudence.Validator.defaultOptions``.

