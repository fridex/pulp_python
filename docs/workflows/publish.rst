.. _host:

Publish and Host
================

This section assumes that you have a repository with content in it. To do this, see the
:doc:`sync` or :doc:`upload` documentation.

Create a Publication
--------------------

Kick off a publish task by creating a new publication. The publish task will generate all the
metadata that ``pip`` needs to install packages (although it will need to be hosted through a
Distribution before it is consumable).

.. literalinclude:: ../_scripts/publication.sh
    :language: bash

Publication Create Response::

    {
      "pulp_href": "/pulp/api/v3/publications/python/pypi/cad6007d-7172-41d1-8c22-0ec95e1d242a/",
      "pulp_created": "2021-03-09T04:30:16.686784Z",
      "repository_version": "/pulp/api/v3/repositories/python/python/8fbb24ee-dc91-44f4-a6ee-beec60aa542d/versions/1/",
      "repository": "/pulp/api/v3/repositories/python/python/8fbb24ee-dc91-44f4-a6ee-beec60aa542d/",
      "distributions": []
    }

Host a Publication (Create a Distribution)
--------------------------------------------

To host a publication, (which makes it consumable by ``pip``), users create a distribution which
will serve the associated publication at ``/pulp/content/<distribution.base_path>``

.. literalinclude:: ../_scripts/distribution.sh
    :language: bash

Response::

   {
      "pulp_href": "/pulp/api/v3/distributions/python/pypi/4839c056-6f2b-46b9-ac5f-88eb8a7739a5/",
      "pulp_created": "2021-03-09T04:36:48.289737Z",
      "base_path": "foo",
      "base_url": "https://pulp3-source-fedora33.localhost.example.com/pulp/content/foo/",
      "content_guard": null,
      "pulp_labels": {},
      "name": "foo",
      "publication": "/pulp/api/v3/publications/python/pypi/a09111b1-6bce-43ac-aed7-2e8441c22704/"
    }

.. _using-distributions:

Use the newly created distribution
-----------------------------------

The metadata and packages can now be retrieved from the distribution::

$ http $CONTENT_ADDR/pulp/content/foo/simple/
$ http $CONTENT_ADDR/pulp/content/foo/simple/shelf-reader/

The content is also pip installable::

$ pip install --trusted-host localhost -i $CONTENT_ADDR/pulp/content/foo/simple/ shelf-reader

If you don't want to specify the distribution path every time, you can modify your ``pip.conf``
file. See the `pip docs <https://pip.pypa.io/en/stable/user_guide/#configuration>`_ for more
detail.::

$ cat pip.conf

.. code::

  [global]
  index-url = http://localhost:24816/pulp/content/foo/simple/

The above configuration informs ``pip`` to install from ``pulp``::

$ pip install --trusted-host localhost shelf-reader
