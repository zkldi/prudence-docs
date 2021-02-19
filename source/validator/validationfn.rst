.. _validation_fn:

Validation Function
====================

The function expected by Prudence for validation.

.. function:: ([self, [parent]])

    :param any self: The object's value for this key. This can be anything.
    :param object parent: The object the above value came from.

    :attribute string errorMessage: An error message for Prudence to display if this function returns false.

    :returns: boolean

.. note:: This function is allowed to return truthy/falsy values, but it is not prefered.
