.. _builtins:

Built-In Functions
===================

All built in functions have built in error messages.

If you think this list should be expanded, feel free to submit a `feature request! <https://github.com/zkldi/Prudence/issues>`_

#####################
Validation Functions
#####################

.. module:: Prudence

The below functions should be used like:

.. code-block:: js

    let schema = {
        key: Prudence.isInteger
    }

.. attribute:: isInteger

    Checks whether a value is a safe integer or not.

    .. note:: This is an alias for Number.isSafeInteger.

.. attribute:: isPositiveInteger

    Checks whether a value is a positive integer or not. 0 is regarded as positive.

.. attribute:: isPositiveNonZeroInteger

    Checks whether a value is a positive, non-zero integer or not.


###############################
Validation Function Generators
###############################

The below functions should be used as follows:

.. code-block:: js

    let schema = {
        key: Prudence.isBoundedInteger(1, 10)
    }

This is because these are functions that return validation functions, rather than
simply being validation functions.

The arguments they expect are detailed in their below definitions.

.. function:: isIn(...values)

    Returns a function that checks whether the value given is inside the list of arguments.

    :param ...values: Rest Parameter: the values to check against.
    :returns: :ref:`validation_fn`

    Example usage:

    .. code-block:: js
    
        let schema = {
            fruit: Prudence.isIn("apple", "banana"),
            alternatively: Prudence.isIn(["apple", "banana"])
        }

    .. note:: You can also pass an array as a first and only argument, and it will automatically be expanded.

.. function:: isBoundedInteger(lower, upper)

    Returns a function that checks whether a value is an integer and between the two arguments.

    This is inclusive on both ends.

    :param integer lower: The number the object's value must be greater than or equal to.
    :param integer upper: The number the object's value must be less than or equal to.

    :returns: :ref:`validation_fn`

.. function:: isBoundedString(lower, upper)

    Returns a function that checks whether a value is a string and its length is between the two arguments.

    This is inclusive on both ends.

    :param integer lower: The number the string's length must be greater than or equal to.
    :param integer upper: The number the string's length must be less than or equal to.

    :returns: :ref:`validation_fn`

.. function:: regex(regex)

    Returns a function that checks whether the input is a string and matches the given regex.

    This exists because regex.test coerces input to a string, even when thats not what we want.

    :param regex regex: The number the string's length must be greater than or equal to.

    :returns: :ref:`validation_fn`

.. warning:: The below functions exist because the ``<``, ``<=``, ``>`` and ``>=`` operators in JS are not strict (think ``==``)
    and convert non-numeric input into numbers, when that's almost certainly not what you'd want.

.. function:: gt(num)

    Returns a function that checks whether a value is a number and greater than the argument.

    :param number num: The number the object's value must be greater than.
    :returns: :ref:`validation_fn`

.. function:: gte(num)

    Returns a function that checks whether a value is a number and greater than or equal to the argument.

    :param number num: The number the object's value must be less than or equal to.
    :returns: :ref:`validation_fn`

.. function:: lt(num)

    Returns a function that checks whether a value is a number and less than the argument.

    :param number num: The number the object's value must be greater than.
    :returns: :ref:`validation_fn`

.. function:: lte(num)

    Returns a function that checks whether a value is a number and less than or equal to the argument.

    :param number num: The number the object's value must be less than or equal to.
    :returns: :ref:`validation_fn`

.. function:: gtInt(num)

    Returns a function that checks whether a value is an integer and greater than the argument.

    :param number num: The number the object's value must be greater than.
    :returns: :ref:`validation_fn`

.. function:: gteInt(num)

    Returns a function that checks whether a value is an integer and greater than or equal to the argument.

    :param number num: The number the object's value must be less than or equal to.
    :returns: :ref:`validation_fn`

.. function:: ltInt(num)

    Returns a function that checks whether a value is an integer and less than the argument.

    :param number num: The number the object's value must be greater than.
    :returns: :ref:`validation_fn`

.. function:: lteInt(num)

    Returns a function that checks whether a value is an integer and less than or equal to the argument.

    :param number num: The number the object's value must be less than or equal to.
    :returns: :ref:`validation_fn`