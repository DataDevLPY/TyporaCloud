# Parsing Raw Data

## CSV: Comma Separated Values

TSV: Tab Separated Values Software: Microsoft Excel, Open Office Calc, and Google Spreadsheets

## CSV: Tools

- Pandas functions for reading tabular data
  - read_csv(): Read delimited data from a file, URL, or file-like object. Use comma as default delimiter.
  - read_table(): Read delimited data from a file, URL, or file-like object. Use tab as default delimiter.
  - read_fwf(): Read data in fixed-width column format, no delimiters.
  - read_clipboard(): Version of read_table that reads data from clipboard. Useful for converting tables from web pages.

## JSON: JavaScript Object Notation

- JSON: one of the most commonly used formats for transferring data between web services and other applications via HTTP
- JSON is completely language independent but uses conventions that are familiar to programmers of the C-family of languages.
- JSON is built on two structures:
  - A collection of name/ values pairs. In various languages, this is realised as an object, record, struct, dictionary, hash table, keyed list, or asscociative array.
  - An ordered list of values. In most languages, this is realised as an array, vector, list, or sequence.

## JSON: tools

- json: a built-in Python library used to parse JSON files.
- pandas json functions:
  - read_json(): Convert a JSON string to pandas object
  - json_normalize(): "Normalise" semi-structured JSON data into a flat table.

## XML: Extensible Markup Language

- XML is a software- and hardware-independent tool for storing and transporting data.
  - It simplifies data sharing and platform changes -- no need to worry about issues of exchanging data between incompatible systems
  - It simplifies data transport -- XML stores data in plan text format
  - It simplifies data availability -- With XML, data can be available to all kinds of "reading machines".
- XML was designed to be both human- and machine-readable.

## XML: DOM tree

- According to the DOM (Document Object Model), everything in an XML document is a node.
- The DOM says:
  - The entire document is a document node
  - Every XML element is an element node
  - The text in the XML elements are text nodes
  - Every attribute is an attribute node
  - Comments are comment nodes

## XML: tools

- Element Tree
  - https://docs.python.org/2/library/xml.etree.elementtree.html
  - Python's built-in XML parser.
- lxml
  - https://lxml.de/
  - Strong performance in parsing very large files
- Beautifulsoup
  - https://www.crummy.com/sofware/BeautifulSoup/bs4/doc/
  - A Python library for pulling dataout of HTML and XML files
  - Works with your favourite parser, html.parser and lxml-xml

## PDF: Portable Document Format

- A file format used to present and exchange documents
  - "looks really do matter" from Adobe
    - PDF can contains text, image, link, button, form field, audio and video.
    - PDF file encapsulates a complete description of the layout information, fonts, graphics, and other meta information of the document

## PDF: Parsing Tools

- pdfminer: A tool for extracting text, images, object coordinates,metadata from PDF documents.
- pdftable: A tool for extracting tables from PDF files, it uses pdfminer to get information on the locations of text elements.
- slate: A small Python module that wraps pdfminer's API.
- Tabula: A simple tool for extracting data tables out of PDF files
- 