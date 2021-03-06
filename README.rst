silver 
======

Django automated billing with an API

.. list-table::

   * - **Travis** (public)
     - .. image:: https://travis-ci.org/silverapp/silver.svg?branch=master
                  :target: https://travis-ci.org/silverapp/silver
   * - **Drone** (private)
     - .. image:: https://drone.presslabs.net/api/badges/silverapp/silver/status.svg?branch=master
                  :target: https://drone.presslabs.net/silverapp/silver


Installation
------------

To get the latest stable release from PyPi

.. code-block:: bash

    sudo apt-get build-dep python-imaging
    pip install django-silver

To get the latest commit from GitHub

.. code-block:: bash

    pip install -e git+git://github.com/kggoyani192/silver.git#egg=silver

Add ``silver`` to your ``INSTALLED_APPS``

.. code-block:: python

    INSTALLED_APPS = (
        ...,
        'silver',
    )

Add the ``silver`` URLs to your ``urls.py``

.. code-block:: python

    urlpatterns = patterns('',
        ...
        url(r'^silver/', include('silver.urls')),
    )

Don't forget to migrate your database

.. code-block:: bash

    ./manage.py migrate silver


Configuration
-----

For the complete API reference, check the project's wiki: <https://github.com/silverapp/silver/wiki>.

To run Silver automatically you have two choices, although we really recommend the first one. You can either:

* Use Celery (4.x) and setup a celery-beat for the following tasks (recommended):

  * silver.tasks.generate_documents
  * silver.tasks.generate_pdfs
  * silver.tasks.execute_transactions (if making use of silver transactions)
  * silver.tasks.fetch_transactions_status (if making use of silver transactions, for which the payment processor doesn't offer callbacks)

  Requirements:
  Celery-once is used to ensure that tasks are not queued more than once, so you can call them as often as you'd like.
  Redis is required by celery-once, so if you prefer not to use redis, you will have to write your own tasks.

* Setup CRONs which call the following Django commands (e.g. ``./manage.py generate_documents``):

  * generate_documents
  * generate_pdfs
  * execute_transactions (if making use of silver transactions)
  * fetch_transactions_status (if making use of silver transactions, for which the payment processor doesn't offer callbacks)

  You'll have to make sure that each of these commands is not run more than once at a time.


For creating the PDF templates, Silver uses the built-in templating engine of
Django <https://docs.djangoproject.com/en/1.8/topics/templates/#the-django-template-language>. 
The template variables that are available in the context of the template are:

    * ``name``
    * ``unit``
    * ``subscription``
    * ``plan``
    * ``provider``
    * ``customer``
    * ``product_code``
    * ``start_date``
    * ``end_date``
    * ``prorated``
    * ``proration_percentage``
    * ``metered_feature``
    * ``context``

For specifying the storage used add the ``SILVER_DOCUMENT_STORAGE`` setting to 
your settings file. Example for storing the PDFs on S3:

.. code-block:: python

    SILVER_DOCUMENT_STORAGE = (
        'storages.backends.s3boto.S3BotoStorage', [], {
            'bucket': 'THE-AWS-BUCKET',
            'access_key': 'YOUR-AWS-ACCESS-KEY',
            'secret_key': 'YOUR-AWS-SECRET-KEY',
            'acl': 'private',
        }
    )

Other available settings:

    * ``SILVER_DEFAULT_DUE_DAYS`` - the default number of days until an invoice is due for payment.
    * ``SILVER_DOCUMENT_PREFIX`` - it gets prepended to the path of the saved files.
      The default path of the documents is ``{prefix}{company}/{doc_type}/{date}/{filename}``


To add REST hooks to Silver you can install and configure the following packages:

    * <https://github.com/PressLabs/django-rest-hooks-ng>
    * <https://github.com/PressLabs/django-rest-hooks-delivery>


Getting Started
---------------

1. Create your profile as a service provider.
2. Add your pricing plans to the mix.
3. Import/add your customer list.
4. Create subscriptions for your customers.
5. Create your custom templates using HTML/CSS or use the ones already provided.
6. Setup cron job for generating the invoices automatically.
7. Enjoy. Silver will automatically generate the invoices or proforma invoices based on your providers' configuration.


Contribute
----------

Development of silver happens at http://github.com/silverapp/silver.

Issues are tracked at http://github.com/silverapp/silver/issues.

The Python package can be found at https://pypi.python.org/pypi/django-silver/.

You are highly encouraged to contribute with code, tests, documentation or just
sharing experience.

Please see CONTRIBUTING.md for a short guide on how to get started with Silver contributions.
