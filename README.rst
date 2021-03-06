|Gitter| Gitter 是个聊天工具.
|version status|
|travis status|
|appveyor status|
|Coverage Status|

Zipline is a Pythonic algorithmic trading library. It is an event-driven
system that supports both backtesting and live-trading. Zipline is currently used in production as the backtesting and live-trading
engine powering `Quantopian <https://www.quantopian.com>`_ -- a free,
community-centered, hosted platform for building and executing trading
strategies.

- `Join our community! <https://groups.google.com/forum/#!forum/zipline>`_
- `Documentation <http://www.zipline.io>`_
- Want to contribute? See our `development guidelines <http://zipline.io/development-guidelines.html>`_

Features
========

- Ease of use: Zipline tries to get out of your way so that you can
  focus on algorithm development. See below for a code example.
- Zipline comes "batteries included" as many common statistics like
  moving average and linear regression can be readily accessed from
  within a user-written algorithm.
- Input of historical data and output of performance statistics are
  based on Pandas DataFrames to integrate nicely into the existing
  PyData eco-system.
- Statistic and machine learning libraries like matplotlib, scipy,
  statsmodels, and sklearn support development, analysis, and
  visualization of state-of-the-art trading systems.

Installation
============

Installing With ``pip``
-----------------------

Assuming you have all required (see note below) non-Python dependencies, you
can install Zipline with ``pip`` via:

.. code-block:: bash

    $ pip install zipline

**Note:** Installing Zipline via ``pip`` is slightly more involved than the
average Python package.  Simply running ``pip install zipline`` will likely
fail if you've never installed any scientific Python packages before.

There are two reasons for the additional complexity:

1. Zipline 封装了一些 C extensions that require access to the CPython C API.
   In order to build the C extensions, ``pip`` needs access to the CPython
   header files for your Python installation.

2. Zipline depends on `numpy <http://www.numpy.org/>`_, the core library for
   numerical array computing in Python.  Numpy depends on having the `LAPACK
   <http://www.netlib.org/lapack>`_ linear algebra routines available.

Because LAPACK and the CPython headers are binary dependencies, the correct way
to install them varies from platform to platform.  On Linux, users generally
acquire these dependencies via a package manager like ``apt``, ``yum``, or
``pacman``.  On OSX, `Homebrew <http://www.brew.sh>`_ is a popular choice
providing similar functionality.

See the full `Zipline Install Documentation`_ for more information on acquiring
binary dependencies for your specific platform.

conda
-----

Another way to install Zipline is 通过 the ``conda`` package manager.

Once set up, you can install Zipline from our ``Quantopian`` channel:

.. code-block:: bash

    $ conda install -c Quantopian zipline

Currently supported platforms include:

-  GNU/Linux 64-bit
-  OSX 64-bit
-  Windows 64-bit


Quickstart
==========

See our `getting started tutorial <http://www.zipline.io/beginner-tutorial.html>`_.

The following code implements a simple dual moving average algorithm.

.. code:: python

    from zipline.api import order_target, record, symbol

    def initialize(context):
        context.i = 0
        context.asset = symbol('AAPL')


    def handle_data(context, data):
        # Skip first 300 days to get full windows
        context.i += 1
        if context.i < 300:
            return

        # Compute averages
        # data.history() has to be called with the same params
        # from above and returns a pandas dataframe.
        short_mavg = data.history(context.asset, 'price', bar_count=100, frequency="1d").mean()
        long_mavg = data.history(context.asset, 'price', bar_count=300, frequency="1d").mean()

        # Trading logic
        if short_mavg > long_mavg:
            # order_target orders as many shares as needed to
            # achieve the desired number of shares.
            order_target(context.asset, 100)
        elif short_mavg < long_mavg:
            order_target(context.asset, 0)

        # Save values for later inspection
        record(AAPL=data.current(context.asset, 'price'),
               short_mavg=short_mavg,
               long_mavg=long_mavg)


You can then run this algorithm using the Zipline CLI（命令行界面）. From the command
line, run:

.. code:: bash

    $ zipline ingest // 吸收; 咽下; 获取（某事物）; 接待; ？？？？？？？？
    $ zipline run -f dual_moving_average.py --start 2011-1-1 --end 2012-1-1 -o dma.pickle

This will download the AAPL price data from `quantopian-quandl` in the
specified time range and stream it through the algorithm and save the
resulting performance dataframe to dma.pickle which you can then load
and analyze from within Python.

Contributing
============

 `development guidelines <http://zipline.io/development-guidelines.html>`_.

If you are looking to start working with the Zipline codebase, navigate to the GitHub `issues` tab and start looking through interesting issues. Sometimes there are issues labeled as `Beginner Friendly <https://github.com/quantopian/zipline/issues?q=is%3Aissue+is%3Aopen+label%3A%22Beginner+Friendly%22>`_ or `Help Wanted <https://github.com/quantopian/zipline/issues?q=is%3Aissue+is%3Aopen+label%3A%22Help+Wanted%22>`_.
