Starting contributing code to Weblate
=====================================

Understand the Weblate source code by going through :doc:`code`,
:doc:`frontend` and :doc:`internals`.

Starting with the codebase
--------------------------

Familiarize yourself with the Weblate codebase, by having a go at the
bugs labelled `good first issue <https://github.com/WeblateOrg/weblate/labels/good%20first%20issue>`_.

You are welcome to start working on these issues without asking. Just announce
that in the issue, so that it's clear that somebody is working on that issue.

Running Weblate locally
-----------------------

The most comfortable approach to get started with Weblate development is to
follow :doc:`../admin/install/source`. It will get you a virtualenv with editable Weblate
sources.

1. Clone the Weblate source code:

   .. code-block:: sh

      git clone https://github.com/WeblateOrg/weblate.git
      cd weblate

2. Create a virtualenv:

   .. code-block:: sh

      uv venv .venv
      source .venv/bin/activate

3. Install Weblate (for this you need some system dependencies, see :doc:`../admin/install/source`) and all dependencies useful for development:

   .. code-block:: sh

      uv pip install -e '.[dev]'

3. Start a development server:

   .. code-block:: sh

      weblate runserver

4. Depending on your configuration, you might also want to start Celery workers:

   .. code-block:: sh

      ./weblate/examples/celery start

5. To run a test (see :ref:`local-tests` for more details):

   .. code-block:: sh

      . scripts/test-database.sh
      pytest

.. seealso::

   :doc:`../admin/install/source`

.. _dev-docker:

Running Weblate locally in Docker
---------------------------------

If you have Docker and the docker-compose-plugin installed, you need an additional tool
called ``jq`` which you can install through your favorite package manager. Then, you can
spin up the development environment by simply running:

.. code-block:: sh

   ./rundev.sh

It will create a development Docker image and start it. Weblate is running on
<http://127.0.0.1:8080/> and you can sign in as the user ``admin`` using ``admin``
as the password. The new installation is empty, so you might want to continue with
:ref:`adding-projects`.

Weblate is configured to use :program:`maildev` container as a e-mail server.
The delivered e-mails can be seen at <http://127.0.0.1:1080/>.

The :file:`Dockerfile` and :file:`docker-compose.yml` for this are located in the
:file:`dev-docker` directory. For easier access to the database during development,
the container running PostgreSQL is exposed on port ``5433``.

The script also accepts some parameters, to execute tests, run it with the
``test`` parameter and then specify any :djadmin:`django:test` parameters,
for example running only tests in the ``weblate.machine`` module:

.. code-block:: sh

   ./rundev.sh test --exitfirst weblate/machine

.. note::

   Be careful that your Docker containers are up and running before running the
   tests. You can check that by running the ``docker ps`` command.

To display the logs:

.. code-block:: sh

   ./rundev.sh logs

To stop the background containers, run:

.. code-block:: sh

   ./rundev.sh stop

Running the script without arguments will re-create the Docker container and restart it.

.. warning::

   This container is not suitable for production use. Security is sacrificed to
   make the development easier.

Bootstrapping your devel instance
---------------------------------

You might want to use :wladmin:`import_demo` to create demo translations and
:wladmin:`createadmin` to make an admin user.

Coding Weblate with PyCharm
---------------------------

PyCharm is a known IDE for Python, here are some guidelines to help you set up
your Weblate project in it.

Considering you have just cloned the GitHub repository to a folder, just open it with
PyCharm. Once the IDE is open, the first step is to specify the interpreter you want
to use:

.. image:: /images/pycharm-1.png

You can either choose to let PyCharm create the virtualenv for you, or select an already
existing one:

.. image:: /images/pycharm-2.png

Don't forget to install the dependencies once the interpreter is set:
Either through the console (the console from the IDE will directly use your
virtualenv by default), or through the interface when you get a warning about missing
dependencies.

The second step is to set the right info to use Django natively inside PyCharm:
The idea is to be able to immediately trigger the unit tests in the IDE.
For that you need to specify the root path of the Django project and the path to its settings:

.. image:: /images/pycharm-3.png

Be careful, the `Django project root` is the actual root of the repository, not the Weblate
sub-directory. About the settings, you could use the :file:`weblate/settings_test.py` from the
repository, but you could create your own setting and set it there.

The last step is to run the server and to put breakpoints in the code to be able
to debug it. This is done by creating a new `Django Server` configuration:

.. image:: /images/pycharm-4.png
.. image:: /images/pycharm-5.png


.. hint::

   Be careful with the property called :guilabel:`No reload`: It prevents
   the server from being reloaded live if you modify files. This allows the
   existing debugger breakpoints to persist, when they normally would be
   discarded upon reloading the server.
