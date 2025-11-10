
Table Properties
================


Alignment
---------

Word allows a table to be aligned between the page margins either left,
right, or center.

The read/write :attr:`Table.alignment` property specifies the alignment for
a table::

    >>> table = document.add_table(rows=2, cols=2)
    >>> table.alignment
    None
    >>> table.alignment = WD_TABLE_ALIGNMENT.RIGHT
    >>> table.alignment
    RIGHT (2)


Autofit
-------

Word has two algorithms for laying out a table, *fixed-width* or `autofit`.
The default is autofit. Word will adjust column widths in an autofit table
based on cell contents. A fixed-width table retains its column widths
regardless of the contents. Either algorithm will adjust column widths
proportionately when total table width exceeds page width.

The read/write :attr:`Table.allow_autofit` property specifies which algorithm
is used::

    >>> table = document.add_table(rows=2, cols=2)
    >>> table.allow_autofit
    True
    >>> table.allow_autofit = False
    >>> table.allow_autofit
    False


Preferred Width
---------------

Word allows a table to have a preferred width, which corresponds to checking
the "Preferred width" checkbox in the Table Properties dialog. When set, the
table maintains its width regardless of window size, providing true fixed-width
behavior.

The read/write :attr:`Table.width` property specifies the preferred width for
a table::

    >>> from docx.shared import Inches, Cm
    >>> table = document.add_table(rows=2, cols=2)
    >>> table.width
    None
    >>> table.width = Inches(6)
    >>> table.width
    5486400
    >>> table.width = Cm(15)
    >>> table.width
    5400040
    >>> table.width = None  # Remove preferred width
    >>> table.width
    None

When :attr:`Table.width` is set to a |Length| value, Word sets the table's
``w:tblW`` element with ``w:type="dxa"`` and the width in twips. When set to
|None|, the ``w:tblW`` element is removed (or remains with ``w:type="auto"``),
allowing the table to use automatic width.

This is distinct from the :attr:`Table.allow_autofit` property, which controls
whether column widths adjust based on content. A table can have a fixed
preferred width (``table.width = Inches(6)``) while still allowing autofit
layout (``table.allow_autofit = True``), or vice versa.


Text Wrapping and Table Positioning
------------------------------------

Word allows tables to have text wrapping enabled, where surrounding text flows
"around" the table instead of above and below it. This is controlled through
table positioning properties (``w:tblpPr``) and the overlap setting
(``w:tblOverlap``).

The :attr:`Table.positioning` property provides access to a
:class:`_TablePositioning` object that controls these settings::

    >>> from docx.enum.table import WD_TABLE_OVERLAP, WD_TABLE_VERTICAL_ANCHOR
    >>> from docx.shared import Twips
    >>> table = document.add_table(rows=3, cols=3)
    >>> 
    >>> # Enable "around" text wrapping
    >>> table.positioning.left_from_text = Twips(180)
    >>> table.positioning.right_from_text = Twips(180)
    >>> table.positioning.vertical_anchor = WD_TABLE_VERTICAL_ANCHOR.TEXT
    >>> table.positioning.y = Twips(1)
    >>> table.overlap = WD_TABLE_OVERLAP.NEVER
    >>> 
    >>> # Read the properties back
    >>> table.positioning.left_from_text
    114300
    >>> table.positioning.vertical_anchor
    TEXT (0)
    >>> table.overlap
    'never'

The :class:`_TablePositioning` object provides the following properties:

* :attr:`left_from_text` - Distance from left edge of table to text (|Length|)
* :attr:`right_from_text` - Distance from right edge of table to text (|Length|)
* :attr:`top_from_text` - Distance from top edge of table to text (|Length|)
* :attr:`bottom_from_text` - Distance from bottom edge of table to text (|Length|)
* :attr:`vertical_anchor` - Vertical anchor point (TEXT, MARGIN, or PAGE)
* :attr:`horizontal_anchor` - Horizontal anchor point (TEXT, MARGIN, or PAGE)
* :attr:`x` - Horizontal position offset from anchor (|Length|, can be negative)
* :attr:`y` - Vertical position offset from anchor (|Length|, can be negative)

The :attr:`Table.overlap` property controls whether the table allows other
floating tables to overlap with it::

    >>> table.overlap = WD_TABLE_OVERLAP.NEVER  # Don't allow overlap
    >>> table.overlap = WD_TABLE_OVERLAP.OVERLAP  # Allow overlap
    >>> table.overlap = None  # Remove overlap setting (inherit)

When positioning properties are set, Word generates ``w:tblpPr`` and
``w:tblOverlap`` elements within ``w:tblPr``. These elements must appear in a
specific order according to the schema: ``w:tblStyle``, ``w:tblpPr``,
``w:tblOverlap``, ``w:bidiVisual``, etc.


Specimen XML
------------

.. highlight:: xml

The following XML represents a 2x2 table::

    <w:tbl>
      <w:tblPr>
        <w:tblStyle w:val="TableGrid"/>
        <w:tblW w:type="auto" w:w="0"/>
        <w:jc w:val="right"/>
        <w:tblLook w:firstColumn="1" w:firstRow="1" w:lastColumn="0"
                   w:lastRow="0" w:noHBand="0" w:noVBand="1" w:val="04A0"/>
      </w:tblPr>
      <w:tblGrid>
        <w:gridCol w:w="4788"/>
        <w:gridCol w:w="4788"/>
      </w:tblGrid>
      <w:tr>
        <w:tc/>
          <w:tcPr>
            <w:tcW w:type="dxa" w:w="4788"/>
          </w:tcPr>
          <w:p/>
        </w:tc>
        <w:tc>
          <w:tcPr>
            <w:tcW w:type="dxa" w:w="4788"/>
          </w:tcPr>
          <w:p/>
        </w:tc>
      </w:tr>
      <w:tr>
        <w:tc>
          <w:tcPr>
            <w:tcW w:type="dxa" w:w="4788"/>
          </w:tcPr>
          <w:p/>
        </w:tc>
        <w:tc>
          <w:tcPr>
            <w:tcW w:type="dxa" w:w="4788"/>
          </w:tcPr>
          <w:p/>
        </w:tc>
      </w:tr>
    </w:tbl>

A table with "around" text wrapping enabled::

    <w:tbl>
      <w:tblPr>
        <w:tblStyle w:val="TableGrid"/>
        <w:tblpPr w:leftFromText="180" w:rightFromText="180" 
                  w:vertAnchor="text" w:tblpY="1"/>
        <w:tblOverlap w:val="never"/>
        <w:tblW w:type="auto" w:w="0"/>
        <w:tblLook w:firstColumn="1" w:firstRow="1" w:lastColumn="0"
                   w:lastRow="0" w:noHBand="0" w:noVBand="1" w:val="04A0"/>
      </w:tblPr>
      <w:tblGrid>
        <w:gridCol w:w="3005"/>
        <w:gridCol w:w="3005"/>
        <w:gridCol w:w="3006"/>
      </w:tblGrid>
      <!-- table rows... -->
    </w:tbl>

The ``w:tblpPr`` element controls table positioning and text wrapping. Key
attributes include:

* ``w:leftFromText`` / ``w:rightFromText`` - Distance from text (in twips)
* ``w:topFromText`` / ``w:bottomFromText`` - Distance from text (in twips)
* ``w:vertAnchor`` - Vertical anchor: "text", "margin", or "page"
* ``w:horzAnchor`` - Horizontal anchor: "text", "margin", or "page"
* ``w:tblpX`` / ``w:tblpY`` - Position offsets (in twips, can be negative)

The ``w:tblOverlap`` element controls whether the table can overlap with other
floating tables. Valid values are "never" or "overlap".


Layout behavior
---------------

Auto-layout causes actual column widths to be both unpredictable and
unstable. Changes to the content can make the table layout shift.


Semantics of CT_TblWidth element
--------------------------------

e.g. ``tcW``::

    <w:tcW w:w="42.4mm"/>

    <w:tcW w:w="1800" w:type="dxa"/>

    <w:tcW w:w="20%" w:type="pct"/>

    <w:tcW w:w="0" w:type="auto"/>

    <w:tcW w:type="nil"/>


    ST_MeasurementOrPercent
    |
    +-- ST_DecimalNumberOrPercent
    |   |
    |   +-- ST_UnqualifiedPercentage
    |   |   |
    |   |   +-- XsdInteger e.g. '1440'
    |   |
    |   +-- ST_Percentage e.g. '-07.43%'
    |
    +-- ST_UniversalMeasure  e.g. '-04.34mm'


Schema Definitions
------------------

.. highlight:: xml

::

  <xsd:complexType name="CT_Tbl">  <!-- denormalized -->
    <xsd:sequence>
      <xsd:group    ref="EG_RangeMarkupElements"        minOccurs="0" maxOccurs="unbounded"/>
      <xsd:element name="tblPr"       type="CT_TblPr"/>
      <xsd:element name="tblGrid"     type="CT_TblGrid"/>
      <xsd:choice                                       minOccurs="0" maxOccurs="unbounded">
        <xsd:element name="tr"        type="CT_Row"/>
        <xsd:element name="customXml" type="CT_CustomXmlRow"/>
        <xsd:element name="sdt"       type="CT_SdtRow"/>
        <xsd:group    ref="EG_RunLevelElts"             minOccurs="0" maxOccurs="unbounded"/>
      </xsd:choice>
    </xsd:sequence>
  </xsd:complexType>

  <xsd:complexType name="CT_TblPr">  <!-- denormalized -->
    <xsd:sequence>
      <xsd:element name="tblStyle"            type="CT_String"        minOccurs="0"/>
      <xsd:element name="tblpPr"              type="CT_TblPPr"        minOccurs="0"/>
      <xsd:element name="tblOverlap"          type="CT_TblOverlap"    minOccurs="0"/>
      <xsd:element name="bidiVisual"          type="CT_OnOff"         minOccurs="0"/>
      <xsd:element name="tblStyleRowBandSize" type="CT_DecimalNumber" minOccurs="0"/>
      <xsd:element name="tblStyleColBandSize" type="CT_DecimalNumber" minOccurs="0"/>
      <xsd:element name="tblW"                type="CT_TblWidth"      minOccurs="0"/>
      <xsd:element name="jc"                  type="CT_JcTable"       minOccurs="0"/>
      <xsd:element name="tblCellSpacing"      type="CT_TblWidth"      minOccurs="0"/>
      <xsd:element name="tblInd"              type="CT_TblWidth"      minOccurs="0"/>
      <xsd:element name="tblBorders"          type="CT_TblBorders"    minOccurs="0"/>
      <xsd:element name="shd"                 type="CT_Shd"           minOccurs="0"/>
      <xsd:element name="tblLayout"           type="CT_TblLayoutType" minOccurs="0"/>
      <xsd:element name="tblCellMar"          type="CT_TblCellMar"    minOccurs="0"/>
      <xsd:element name="tblLook"             type="CT_TblLook"       minOccurs="0"/>
      <xsd:element name="tblCaption"          type="CT_String"        minOccurs="0"/>
      <xsd:element name="tblDescription"      type="CT_String"        minOccurs="0"/>
      <xsd:element name="tblPrChange"         type="CT_TblPrChange"   minOccurs="0"/>
    </xsd:sequence>

  <!-- table alignment --------------------------------- -->

  <xsd:complexType name="CT_JcTable">
    <xsd:attribute name="val" type="ST_JcTable" use="required"/>
  </xsd:complexType>

  <xsd:simpleType name="ST_JcTable">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="center"/>
      <xsd:enumeration value="end"/>
      <xsd:enumeration value="left"/>
      <xsd:enumeration value="right"/>
      <xsd:enumeration value="start"/>
    </xsd:restriction>
  </xsd:simpleType>

  <!-- table width ------------------------------------- -->

  <xsd:complexType name="CT_TblWidth">
    <xsd:attribute name="w"    type="ST_MeasurementOrPercent"/>
    <xsd:attribute name="type" type="ST_TblWidth"/>
  </xsd:complexType>

  <xsd:simpleType name="ST_MeasurementOrPercent">
    <xsd:union memberTypes="ST_DecimalNumberOrPercent s:ST_UniversalMeasure"/>
  </xsd:simpleType>

  <xsd:simpleType name="ST_DecimalNumberOrPercent">
    <xsd:union memberTypes="ST_UnqualifiedPercentage s:ST_Percentage"/>
  </xsd:simpleType>

  <xsd:simpleType name="ST_UniversalMeasure">
    <xsd:restriction base="xsd:string">
      <xsd:pattern value="-?[0-9]+(\.[0-9]+)?(mm|cm|in|pt|pc|pi)"/>
    </xsd:restriction>
  </xsd:simpleType>

  <xsd:simpleType name="ST_UnqualifiedPercentage">
    <xsd:restriction base="xsd:integer"/>
  </xsd:simpleType>

  <xsd:simpleType name="ST_Percentage">
    <xsd:restriction base="xsd:string">
      <xsd:pattern value="-?[0-9]+(\.[0-9]+)?%"/>
    </xsd:restriction>
  </xsd:simpleType>

  <xsd:simpleType name="ST_TblWidth">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="nil"/>
      <xsd:enumeration value="pct"/>
      <xsd:enumeration value="dxa"/>
      <xsd:enumeration value="auto"/>
    </xsd:restriction>
  </xsd:simpleType>

  <!-- table layout ------------------------------------ -->

  <xsd:complexType name="CT_TblLayoutType">
    <xsd:attribute name="type" type="ST_TblLayoutType"/>
  </xsd:complexType>

  <xsd:simpleType name="ST_TblLayoutType">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="fixed"/>
      <xsd:enumeration value="autofit"/>
    </xsd:restriction>
  </xsd:simpleType>

  <!-- table look -------------------------------------- -->

  <xsd:complexType name="CT_TblLook">
    <xsd:attribute name="firstRow"    type="s:ST_OnOff"/>
    <xsd:attribute name="lastRow"     type="s:ST_OnOff"/>
    <xsd:attribute name="firstColumn" type="s:ST_OnOff"/>
    <xsd:attribute name="lastColumn"  type="s:ST_OnOff"/>
    <xsd:attribute name="noHBand"     type="s:ST_OnOff"/>
    <xsd:attribute name="noVBand"     type="s:ST_OnOff"/>
    <xsd:attribute name="val"         type="ST_ShortHexNumber"/>
  </xsd:complexType>

  <!-- table positioning -------------------------------- -->

  <xsd:complexType name="CT_TblPPr">
    <xsd:attribute name="leftFromText"   type="s:ST_TwipsMeasure"/>
    <xsd:attribute name="rightFromText"  type="s:ST_TwipsMeasure"/>
    <xsd:attribute name="topFromText"    type="s:ST_TwipsMeasure"/>
    <xsd:attribute name="bottomFromText" type="s:ST_TwipsMeasure"/>
    <xsd:attribute name="vertAnchor"     type="ST_VAnchor"/>
    <xsd:attribute name="horzAnchor"     type="ST_HAnchor"/>
    <xsd:attribute name="tblpXSpec"      type="s:ST_XAlign"/>
    <xsd:attribute name="tblpX"          type="ST_SignedTwipsMeasure"/>
    <xsd:attribute name="tblpYSpec"      type="s:ST_YAlign"/>
    <xsd:attribute name="tblpY"          type="ST_SignedTwipsMeasure"/>
  </xsd:complexType>

  <xsd:simpleType name="ST_VAnchor">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="text"/>
      <xsd:enumeration value="margin"/>
      <xsd:enumeration value="page"/>
    </xsd:restriction>
  </xsd:simpleType>

  <xsd:simpleType name="ST_HAnchor">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="text"/>
      <xsd:enumeration value="margin"/>
      <xsd:enumeration value="page"/>
    </xsd:restriction>
  </xsd:simpleType>

  <!-- table overlap ------------------------------------ -->

  <xsd:complexType name="CT_TblOverlap">
    <xsd:attribute name="val" type="ST_TblOverlap" use="required"/>
  </xsd:complexType>

  <xsd:simpleType name="ST_TblOverlap">
    <xsd:restriction base="xsd:string">
      <xsd:enumeration value="never"/>
      <xsd:enumeration value="overlap"/>
    </xsd:restriction>
  </xsd:simpleType>
