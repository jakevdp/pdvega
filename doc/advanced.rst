.. _advanced-plotting:

Advanced Plotting: Using Vega-Lite Directly
===========================================

The ``pdvega`` API is rather simplistic; it's main purpose is to mimic the
pandas plotting API, using Vega stack; ``pdvega`` is tied to (and based upon) the `Altair`_ project, which allows any resulting plot to be adjusted flexibly from within a Python API.


For example, consider this plot:

.. pdvega-setup::

   import pdvega
   import pandas

.. pdvega-plot::

   from vega_datasets import data
   iris = data.iris()

   iris.vgplot(kind='scatter', x='sepalLength', y='petalLength', c='species')

Vega-Lite's default behavior is to include the zero-value in the scale, unless
the user explicitly turns that requirement off in the JSON spec.

``pdvega`` is not designed to give easy access to every option available in the Altair or, more broadly, the Vega-Lite schema, but it is possible to modify the specification manually. Any chart object ``pdvega`` produces is a complete Altair Chart object, that can be modified directly. In particular, if you look at the options for `chart.encoding`_, you can see that there is a ``"scale"`` property of the "x" encoding which allows turning off the zero behavior.

.. pdvega-setup::
    
    import altair as alt
    from vega_datasets import data
    iris = data.iris()
    plot = iris.vgplot(kind='scatter', x='sepalLength', y='petalLength', c='species')

.. pdvega-plot::

    plot.spec['encoding']['x']['scale'] = alt.Scale(zero=False) # or simply {zero:False}
    plot

Same way Altair also allows us to add interactivity:

.. pdvega-plot::
    plot.interactive()

Or combine charts:

.. pdvega-setup::

    from vega_datasets import data
    iris = data.iris()
    plot1 = iris.vgplot(kind='scatter', x='sepalLength', y='petalLength', c='species')
    plot2 = iris.vgplot(kind='scatter', x='sepalWidth', y='petalWidth', c='species')

.. pdvega-plot::

    plot1 | plot2

Using this type of approach, you can customize your plots in any way Altair allows.

Getting Vega-Lite specification
-------------------------------

In addition, we can always return raw `Vega-Lite`_ specification from any plot using the ``spec`` attribute. For convenience, there is also a ``spec_no_data`` attribute that returns the spec without the the embedded data:

.. code-block:: python

   >>> plot = iris.vgplot(kind='scatter', x='sepalLength', y='petalLength', c='species')
   >>> plot.spec_no_data
   {'$schema': 'https://vega.github.io/schema/vega-lite/v2.json',
   'encoding': {'color': {'field': 'species', 'type': 'nominal'},
    'x': {'field': 'sepalLength', 'type': 'quantitative'},
    'y': {'field': 'petalLength', 'type': 'quantitative'}},
   'height': 300,
   'mark': 'circle',
   'selection': {'grid': {'bind': 'scales', 'type': 'interval'}},
   'width': 450}

This dictionary contains the specification that tells the vega-lite renderer
how to map data to visual components in the plot. You can read more details on
the `Vega-Lite`_ website. 

.. note:: Altair can also dump data as a ``json`` file and link specs automatically, via ``alt.data_transformers.enable('json')``.

.. note:: Alternatively, Altair can convert chart directly to Vega specs.


Skipping ``vgplot`` entirely
----------------------------

``pdvega`` is merely a convenience library; If you would like to skip pdvega's vgplot API entirely and build your Vega-Lite plot from scratch, you can use pandas with Altair directly (see more in `Altair`_ documentation)

For ideas on what sort of visualizations you can create in this way,
check out the specifications on `Altair examples`_ and `Vega-Lite examples`_ pages.
The `Vega online editor`_ is also a useful resource for developing visualizations
directly in Vega or Vega-Lite.

.. _Vega-Lite: http://vega.github.io/vega-lite/
.. _Altair: http://altair-viz.github.io/
.. _Vega-Lite scales: https://vega.github.io/vega-lite/docs/scale.html
.. _Altair examples: https://altair-viz.github.io/gallery/index.html
.. _Vega-Lite examples: https://vega.github.io/vega-lite/examples/
.. _Vega online editor: https://vega.github.io/editor/#/custom/vega-lite
