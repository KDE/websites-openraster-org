Layer Stack Specification
=========================

Introduction
------------

This document specifies the
`OpenRaster <https://en.wikipedia.org/wiki/OpenRaster>`__ Layer Stack XML
format. The main two goals of the specification are interoperability
between applications and allowing files to be saved without destructive
pixel operations.

Note that this document does not define how the data is stored, and
especially doesn't address the issue of how to store pixels nor how
OpenRaster files can be stored on disk. This document doesn't address
the mathematics behind layer compositing operations: it's simpler to
describe that using other standard bodies' documents at this stage.

The first part of the document explains the concept of a baseline Layer
Stack, with examples. Each attribute and element defined by the baseline
profile is explained individually.

Baseline documents
------------------

A baseline OpenRaster document is a document that can be opened and look
similar on all applications and platforms. The baseline layer stack
format defines the minimum requirement to achieve interoperability and
what is unsafe for interoperability. Layer Stack format permits
extensions, which should be individually specified in detail, and work
safely with the baseline profile.

Some users will require full interoperability, to create files which can
be edited on every application supporting OpenRaster. Other users will
only need to view OpenRaster files. This specification defines two
classes of baseline support:

-  "Full baseline", which allows reading and editing
-  "Viewing baseline" which guarantees visualization of the file, but
   editing might damage the result

Syntax
------

The formal syntax of the layer stack is given following the `RelaxNG Schema
<https://relaxng.org/>`__:

-  https://invent.kde.org/documentation/openraster-org/-/blob/master/openraster-standard/schema.rnc
   (compact format, canonical schema)
-  https://invent.kde.org/documentation/openraster-org/-/blob/master/openraster-standard/schema.rng
   (xml format)

Example
-------

::

    <?xml version='1.0' encoding='UTF-8'?>
    <image version="0.0.3" w="300" h="177" xres="600" yres="600">
      <stack>
        <stack>
            <layer name="OpenRaster Logo" src="data/hw.svg" x="15" y="5" />
        </stack>
        <!-- filters are syntactically permissible, but not valid for baseline -->
        <layer name="layer1" src="data/image1.png" />
      </stack>
    </image>

Elements and Attributes
-----------------------

``image`` element
~~~~~~~~~~~~~~~~~

This is the root element of the file. The logical size of the image is
given by the mandatory ``w`` and ``h`` attributes, which are
positive integers. The content expressed within ``image`` may have
extents which can be smaller or larger, and so the image should be
cropped to (0,0,w,h) when displaying, printing, or otherwise exporting
to a context which requires a rectangular image.

The mandatory ``version`` attribute specifies the version of the
OpenRaster specification to which the OpenRaster file as a whole
conforms. Values are
`xsd:string <http://www.w3.org/TR/xmlschema-2/#string>`__\ s which
conform to `SemVer 2.0.0 <http://semver.org/spec/v2.0.0.html>`__.

The optional ``xres`` and ``yres`` attributes specify the nominal
resolution of the document as a whole in the horizontal and vertical
dimensions, in pixels per inch. Values are
`xsd:int <http://www.w3.org/TR/xmlschema-2/#int>`__\ s with a minimum
value of 1. The default value, if unspecified, is 72. If either is
specified, the other must also be specified. Applications should
preserve resolution information specified in the document in both
directions unless it is adjusted by the user, even if they make the
simplifying assumption that ``xres`` is equal to ``yres``.

.. versionadded:: 0.0.3
   The ``xres`` and ``yres`` attributes.
.. versionadded:: 0.0.1
   The ``version`` attribute.

Common attributes for layer and non-root stack elements
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``name`` attribute
^^^^^^^^^^^^^^^^^^

The name of the object represented by an element. A Unicode string,
encoded in the XML document's own encoding.

``opacity`` attribute
^^^^^^^^^^^^^^^^^^^^^

The object's opacity, expressed as a simple floating-point number. Values range from 0.0 (fully transparent) to 1.0 (fully opaque). Default value is 1.0.

If the layer/stack has an alpha channel, it gets multiplied by this opacity before blending.

``visibility`` attribute
^^^^^^^^^^^^^^^^^^^^^^^^

The visibility of the object.

Valid values
  ``visible``
    the layer/stack is displayed
  ``hidden``
    the layer/stack is not displayed 

Default value
  ``visible``.

``composite-op`` attribute
^^^^^^^^^^^^^^^^^^^^^^^^^^

The operation to use when rendering this stack or layer over its
backdrop. It is also called the blending mode.

The effect is to be calculated as described in `Compositing-1:
General Formula for Compositing and
Blending <http://www.w3.org/TR/compositing-1/#generalformula>`__, namely
as combinations of a colour *blending function* and a Porter-Duff alpha
compositing operator. This attribute's name is perhaps a misnomer now,
but it reflects the history of the OpenRaster specification and the
evolution of the attribute from its inspirations in the earlier `SVG 1.2
Working
Draft <http://dev.w3.org/SVG/modules/compositing/master/SVGCompositing.html#comp-op-property>`__
and in `GEGL's named processing
"operations" <http://www.gegl.org/operations>`__, which also
provide the seeds of the naming scheme for values below.

========================= ========================= =========================
          Value               Blending function       Compositing Operator
========================= ========================= =========================
``svg:src-over``          Normal_                   `Source Over`_
``svg:multiply``          Multiply__                `Source Over`_
``svg:screen``            Screen__                  `Source Over`_
``svg:overlay``           Overlay__                 `Source Over`_
``svg:darken``            Darken__                  `Source Over`_
``svg:lighten``           Lighten__                 `Source Over`_
``svg:color-dodge``       `Color Dodge`__           `Source Over`_
``svg:color-burn``        `Color Burn`__            `Source Over`_
``svg:hard-light``        `Hard Light`__            `Source Over`_
``svg:soft-light``        `Soft Light`__            `Source Over`_
``svg:difference``        Difference__              `Source Over`_
``svg:color``             Color__                   `Source Over`_
``svg:luminosity``        Luminosity__              `Source Over`_
``svg:hue``               Hue__                     `Source Over`_
``svg:saturation``        Saturation__              `Source Over`_
``svg:plus``              Normal_                   Lighter__
``svg:dst-in``            Normal_                   `Destination In`__
``svg:dst-out``           Normal_                   `Destination Out`__
``svg:src-atop``          Normal_                   `Source Atop`__
``svg:dst-atop``          Normal_                   `Destination Atop`__
========================= ========================= =========================

.. _Normal: http://www.w3.org/TR/compositing-1/#blendingnormal
.. _Source Over: http://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_srcover
__ http://www.w3.org/TR/compositing-1/#blendingmultiply
__ http://www.w3.org/TR/compositing-1/#blendingscreen
__ http://www.w3.org/TR/compositing-1/#blendingoverlay
__ http://www.w3.org/TR/compositing-1/#blendingdarken
__ http://www.w3.org/TR/compositing-1/#blendinglighten
__ http://www.w3.org/TR/compositing-1/#blendingcolordodge
__ http://www.w3.org/TR/compositing-1/#blendingcolorburn
__ http://www.w3.org/TR/compositing-1/#blendinghardlight
__ http://www.w3.org/TR/compositing-1/#blendingsoftlight
__ http://www.w3.org/TR/compositing-1/#blendingdifference
__ http://www.w3.org/TR/compositing-1/#blendingcolor
__ http://www.w3.org/TR/compositing-1/#blendingluminosity
__ http://www.w3.org/TR/compositing-1/#blendinghue
__ http://www.w3.org/TR/compositing-1/#blendingsaturation
__ http://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_plus
__ http://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstin
__ http://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstout
__ http://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_srcatop
__ http://www.w3.org/TR/compositing-1/#porterduffcompositingoperators_dstatop

The default value is ``svg:src-over``, which represents simple alpha
compositing.

In the future other compositing modes might be added, and a way for
applications to define new modes will be specified.

.. versionadded:: 0.0.5
   The ``svg:src-atop`` and ``svg:dst-atop`` values.
.. versionadded:: 0.0.4
   The ``svg:dst-in`` and ``svg:dst-out`` values.

``stack`` element
^^^^^^^^^^^^^^^^^

The ``stack`` element describes a group of layers. They may contain
sub-\ ``stack``\ s, ``layer``\ s, or ``text`` elements. The first
element in a stack is the uppermost.

The following attributes are optional on non-root ``stack``\ s, but must
be omitted on the root stack.

-  ``name``
-  ``opacity``
-  ``visibility``
-  ``composite-op`` (only relevant when the stack is rendered as ``isolate``, see |isolation attribute|_)
-  ``isolation``

.. |isolation attribute| replace:: ``isolation`` attribute
.. deprecated:: 0.0.6
   ``x`` and ``y`` attributes on ``stack`` elements are no longer allowed.

``isolation`` attribute
^^^^^^^^^^^^^^^^^^^^^^^

.. versionadded:: 0.0.4

If a stack is isolated, it means the rendering is done in a separate image, starting with a fully transparent backdrop.

Valid values
  ``isolate``
    the stack is always rendered as isolate
  ``auto``
    the layers of the stack are rendered directly on the underlying backdrop, unless specified otherwise.

Default value
  ``isolate``

``layer`` element
^^^^^^^^^^^^^^^^^

The ``layer`` element defines a graphical layer within a layer stack,
stored in a separate file within the OpenRaster file. The following
attribute is required:

``src``
   the path to the stored data file for this layer. See the
   File Layout Specification for an explanation of the values which can
   go here.

The following attributes are optional on ``layer`` elements:

-  ``name``
-  ``x`` and ``y``
-  ``opacity``
-  ``visibility``
-  ``composite-op`` (not relevant if the layer is at the bottom of an isolate stack)

``x`` and ``y`` attributes
^^^^^^^^^^^^^^^^^^^^^^^^^^

These attributes are used with layers only. They are the horizontal and vertical offset in pixels to be applied. They are signed integers with a default value of 0. When both are zero, the layer source is drawn at the top-left corner.

For a non-root stack, x and y are ignored. They do not add an offset to the layers contained in the stack. Readers can thus discard them and writers can omit them. The offset of the contained layers is solely defined by their own x and y attributes.

Compositing the image
~~~~~~~~~~~~~~~~~~~~~

.. versionadded:: 0.0.4

Layer stacks should be composited in a manner conforming to the W3C's
`Compositing and Blending Level 1 Candidate
Recommendation <http://www.w3.org/TR/compositing-1/>`__. In terms of
this specification's rendering model, some OpenRaster layer stacks or
nested sub-stacks are *isolated* groups, but some sub-stacks may be
non-isolated.

`Isolated groups <http://www.w3.org/TR/compositing-1/#isolatedgroups>`__
are always rendered independently at first, starting with a
fully-transparent 'black' backdrop (rgba={0,0,0,0}). The results of this
independent composite are then rendered on top of the group's own
backdrop using the group's opacity and composite mode settings.
Conversely non-isolated groups are rendered by rendering each child
layer or sub-stack in turn to the group's backdrop, just as if there
were no stacked group.

The root stack has a fixed, implicit rendering in OpenRaster: it is
to composite as an isolated group over a background of the
application's choice.

Non-root stacks should be rendered as isolated groups if their
``isolation`` property is ``isolate`` (and not explicitly set to ``auto``)

When a stack is not isolated, its ``composite-op`` attribute is ignored and the
``composite-op`` of the layers are used instead. The ``visibility`` and ``opacity``
attributes should be combined to the corresponding values of the layer, for
example by multiplication. Thus if the stack has an opacity of 0.5 and contains
a layer of opacity 0.5, it amounts to having a layer of opacity 0.25.

Applications may assume that all stacks are isolated groups if that is
all they support. If they do so, they should declare when writing
OpenRaster files that their layer groups are isolated
(``isolation='isolate'``).
