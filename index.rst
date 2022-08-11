.. Ora documentation master file, created by
   sphinx-quickstart on Thu May  3 17:28:12 2018.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to OpenRaster.org
=========================

This is the home of the Open Raster spec

OpenRaster is file format specification for the exchange of layered raster images 
between image editors. OpenRaster was `proposed in 2006 by Boudewijn Rempt and
Cyrille Berger <https://valdyas.org/fading/software/openraster/>`__. 

OpenRaster is an intentionally simple file format, similar to the Open Document
file format: a zip file with an xml document describing the layer structure and a 
png image for each raster layer or an svg image for each vector layer.

Applications that support OpenRaster are Krita, GIMP, MyPaint and Scribus.


.. toctree::
   :maxdepth: 2
   :caption: Contents:
   
   baseline/baseline
   archival/archival
   extensions/extensions
   proposals/proposals



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
