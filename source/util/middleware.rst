.. _util_middleware:

Express Middleware
===========================

This provides utilities for using Prudence in `Express <https://expressjs.com>`_.

.. module:: Prudence

.. function:: Middleware(schema, [errorHandler, [errorMessages, [options]]])

    Creates an Express Middleware that validates req.query (method "GET") or req.body (all other methods) using Prudence.

    On success, this 

    :param object schema: The Prudence schema to validate req.query or req.body against.
    :param function errorHandler: If prudence hits an error, this function determines what to do with it. See :ref:`util_middleware_errorhandler`, optional.
    :param object errorMessages: Error message overrides for Prudence, optional.
    :param object options: The options for Prudence, optional.

    :returns: Express Middleware Function

.. note::
    Passing an errorHandler is optional. If one is not passed, the middleware will return 400 and the error message in JSON.

Example usage:

.. code-block:: JS

    let schema = {
        username: Prudence.isBoundedString(3, 20),
        password: Prudence.isBoundedString(3, 20),
        confirmPassword: (self, parent) => self === parent.password
    };

    let errorHandler = (req, res, next, errMsg) => res.send(400).send(`You messed up! ${errMsg}`);

    router.post("/register", Prudence.Middleware(schema, errorHandler), (req, res) => {
        // assign cookies or something
        return res.redirect("/"); // send users back to homepage
    });

.. _util_middleware_errorhandler:

Error Handler
###########################

A function which is passed 4 parameters. In order; ``req``, ``res``, ``next``, ``errMsg``.

The first three are identical to their Express middleware counterparts. The errMsg is the string Prudence returned.

Curry Middleware
============================

It's common that you'll want to re-use error handlers. To support this, you can use the following function.

.. function:: CurryMiddleware(errorHandler)

    Returns a function that takes schema, [errorMessages, [options]] to create express middleware.
    The initially passed errorHandler is always used.

    :param function errorHandler: If prudence hits an error, this function determines what to do with it. See :ref:`util_middleware_errorhandler`, optional.

    :returns: See above.

Example usage:

.. code-block:: JS

    let schema = {
        username: Prudence.isBoundedString(3, 20),
        password: Prudence.isBoundedString(3, 20),
        confirmPassword: (self, parent) => self === parent.password
    };

    let errorHandler = (req, res, next, errMsg) => res.send(400).send(`You messed up! ${errMsg}`);

    let MiddlewareGenerator = Prudence.CurryMiddleware(errorHandler);

    router.post("/register", MiddlewareGenerator(schema), (req, res) => {
        // assign cookies or something
        return res.redirect("/"); // send users back to homepage
    });

Example usage:

.. code-block:: JS

    let schema = {
        username: Prudence.isBoundedString(3, 20),
        password: Prudence.isBoundedString(3, 20),
        confirmPassword: (self, parent) => self === parent.password
    };

    let errorHandler = (req, res, next, errMsg) => res.send(400).send(`You messed up! ${errMsg}`);

    router.post("/register", Prudence.Middleware(schema, errorHandler), (req, res) => {
        // assign cookies or something
        return res.redirect("/"); // send users back to homepage
    });
