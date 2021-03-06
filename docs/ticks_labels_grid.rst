==================================
Ticks, tick labels, and grid lines
==================================

For the example in the following page we start from the example introduced in
:doc:`getting_started`.

.. plot::
   :context: reset
   :nofigs:

    from astropy.wcs import WCS
    from astropy.io import fits
    hdu = fits.open('msx.fits')[0]
    wcs = WCS(hdu.header)

    import matplotlib.pyplot as plt
    fig = plt.figure()

    from wcsaxes import WCSAxes

    ax = WCSAxes(fig, [0.1, 0.1, 0.8, 0.8], wcs=wcs)
    fig.add_axes(ax)  # note that the axes have to be added to the figure

    ax.imshow(hdu.data, vmin=-2.e-5, vmax=2.e-4, cmap=plt.cm.gist_heat,
              origin='lower')


Coordinate objects
==================

While for many images, the coordinate axes are aligned with the pixel axes,
this is not always the case, especially if there is any rotation in the world
coordinate system, or in coordinate systems with high curvature, where the
coupling between x- and y-axis to actual coordinates become less well-defined.

Therefore rather than referring to ``x`` and ``y`` ticks as Matplotlib does,
we use specialized objects to access the coordinates. The coordinates used in
the plot can be accessed using the ``coords`` attribute. The coordinates can
either be accessed by index::

    lon = ax.coords[0]
    lat = ax.coords[1]

or, in the case of common coordinate systems, by their name:

.. plot::
   :context:
   :include-source:
   :nofigs:

    lon = ax.coords['glon']
    lat = ax.coords['glat']

In this example, the image is in Galactic coordinates, so the coordinates are
called ``glon`` and ``glat``. For an image in equatorial coordinates, you
would use ``ra`` and ``dec``. The names are only available for specific celestial coordinate systems - for all other systems, you should use the index of the coordinate (``0`` or ``1``).

Each coordinate is an instance of the :class:`~wcsaxes.coordinate_helpers.CoordinateHelper`
class, which can be used to control the appearance of the ticks, tick labels,
grid lines, and axis labels associated with that coordinate.

Axis labels
===========

Axis labels can be added using the :meth:`~wcsaxes.coordinate_helpers.CoordinateHelper.set_axislabel` method:

.. plot::
   :context:
   :include-source:
   :align: center

    lon.set_axislabel('Galactic Longitude')
    lat.set_axislabel('Galactic Latitude')

Tick label format
=================

The format of the tick labels can be specified with the
:meth:`~wcsaxes.coordinate_helpers.CoordinateHelper.set_major_formatter` method. This method accepts either a standard Matplotlib ``Formatter`` object, or a
string describing the format:

.. plot::
   :context:
   :include-source:
   :align: center

    lon.set_major_formatter('dd:mm')
    lat.set_major_formatter('dd:mm:ss.s')

The syntax for the format string is the following:

==================== ====================
       format              result
==================== ====================
``'dd'``              ``'15d'``
``'dd:mm'``           ``'15d24m'``
``'dd:mm:ss'``        ``'15d23m32s'``
``'dd:mm:ss.s'``      ``'15d23m32.0s'``
``'dd:mm:ss.ssss'``   ``'15d23m32.0316s'``
``'hh'``              ``'1h'``
``'hh:mm'``           ``'1h02m'``
``'hh:mm:ss'``        ``'1h01m34s'``
``'hh:mm:ss.s'``      ``'1h01m34.1s'``
``'hh:mm:ss.ssss'``   ``'1h01m34.1354s'``
``'d'``               ``'15'``
``'d.d'``             ``'15.4'``
``'d.dd'``            ``'15.39'``
``'d.ddd'``           ``'15.392'``
``'x.xxxx'``          ``'15.3922'``
==================== ====================

All the ``d...`` and ``h...`` formats can be used for angular coordinate axes,
while the ``x...`` formats should be used for non-angular coordinate axes.

Tick/label spacing and properties
=================================

The spacing of ticks/tick labels should have a sensible default, but you may
want to be able to manually specify the spacing. This can be done using the
:meth:`~wcsaxes.coordinate_helpers.CoordinateHelper.set_ticks` method. There are different
options that can be used:

* Set the tick positions manually::

      lon.set_ticks([242.2, 242.3, 242.4])

* Set the spacing between ticks::

      lon.set_ticks(spacing=0.1)

* Set the approximate number of ticks::

      lon.set_ticks(number=4)

In the case of angular axes, you should specify the spacing as an Astropy
:class:`~astropy.units.quantity.Quantity`::

      from astropy import units as u
      lon.set_ticks(spacing=5. * u.arcmin)

This is to avoid roundoff errors. The
:meth:`~wcsaxes.coordinate_helpers.CoordinateHelper.set_ticks` method can also be used to set the
appearance (color and size) of the ticks, using the ``color=`` and ``size=``
options.

We can apply this to the previous example:

.. plot::
   :context:
   :include-source:
   :align: center

    from astropy import units as u
    lon.set_ticks(spacing=10 * u.arcmin, color='white')
    lat.set_ticks(spacing=10 * u.arcmin, color='white')

Tick, tick label, and axis label position
=========================================

By default, the tick and axis labels for the first coordinate are shown on the
x-axis, and the tick and axis labels for the second coordinate are shown on
the y-axis. In addition, the ticks for both coordintes are shown on all axes.
This can be customized using the
:meth:`~wcsaxes.coordinate_helpers.CoordinateHelper.set_ticks_position` and
:meth:`~wcsaxes.coordinate_helpers.CoordinateHelper.set_ticklabel_position` methods, which each
take a string that can contain any or several of ``l``, ``b``, ``r``, or ``t``
(indicating the ticks or tick labels should be shown on the left, bottom,
right, or top axes respectively):

.. plot::
   :context:
   :include-source:
   :align: center

    lon.set_ticks_position('bt')
    lon.set_ticklabel_position('bt')
    lon.set_axislabel_position('bt')
    lat.set_ticks_position('lr')
    lat.set_ticklabel_position('lr')
    lat.set_axislabel_position('lr')

we can set the defaults back using:

.. plot::
   :context:
   :include-source:
   :align: center

    lon.set_ticks_position('all')
    lon.set_ticklabel_position('b')
    lon.set_axislabel_position('b')
    lat.set_ticks_position('all')
    lat.set_ticklabel_position('l')
    lat.set_axislabel_position('l')


Coordinate grid
===============

Since the properties of a coordinate grid are linked to the properties of the
ticks and labels, grid lines 'belong' to the coordinate objects described
above. For example, you can show a grid with red lines for RA and blue lines
for declination with:

.. plot::
   :context:
   :include-source:
   :align: center

    lon.grid(color='yellow', alpha=0.5)
    lat.grid(color='orange', alpha=0.5)

For convenience, you can also simply draw a grid for all the coordinates in
one command:

.. plot::
   :context:
   :include-source:
   :align: center

    ax.coords.grid(color='white', alpha=0.3)