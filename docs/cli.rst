Command line interface
======================

.. code-block:: console

    $ mercantile
    Usage: mercantile [OPTIONS] COMMAND [ARGS]...

      Command line interface for the Mercantile Python package.

    Options:
      -v, --verbose  Increase verbosity.
      -q, --quiet    Decrease verbosity.
      --help         Show this message and exit.

    Commands:
      children  Write the children of the tile.
      parent    Write the parent tile.
      shapes    Write the shapes of tiles as GeoJSON.
      tiles     List tiles overlapped or contained by a lng/lat bounding box.

Examples
--------

``mercantile shapes`` generates GeoJSON from tiles and ``mercantile tiles``
does the reverse operation.

.. code-block:: console

    $ mercantile shapes "[2331, 1185, 12]" | mercantile tiles 12
    [2331, 1185, 12]

``mercantile parent`` and ``mercantile children`` traverse the hierarchy
of Web Mercator tiles.

.. code-block:: console

    $ mercantile parent "[2331,1185,12]" | mercantile children
    [2330, 1184, 12]
    [2331, 1184, 12]
    [2331, 1185, 12]
    [2330, 1185, 12]


shapes
------

The shapes command writes Mercator tile shapes to several forms of GeoJSON.

.. code-block:: console

    $ echo "[106, 193, 9]" | mercantile shapes --indent 2 --precision 6
    {
      "features": [
        {
          "geometry": {
            "coordinates": [
              [
                [
                  -105.46875,
                  39.909736
                ],
                [
                  -105.46875,
                  40.446947
                ],
                [
                  -104.765625,
                  40.446947
                ],
                [
                  -104.765625,
                  39.909736
                ],
                [
                  -105.46875,
                  39.909736
                ]
              ]
            ],
            "type": "Polygon"
          },
          "id": "(106, 193, 9)",
          "properties": {
            "title": "XYZ tile (106, 193, 9)"
          },
          "type": "Feature"
        }
      ],
      "type": "FeatureCollection"
    }

tiles
-----

The tiles command writes descriptions of tiles intersecting with a geographic
point, bounding box, or GeoJSON object.

.. code-block:: console

    $ echo "[-104.99, 39.99, -105, 40]" | mercantile tiles 14
    [3413, 6202, 14]
    [3413, 6203, 14]

The commands can be piped together to do this:

.. code-block:: console

    $ echo "[-104.99, 39.99, -105, 40]" \
    > | mercantile tiles 14 \
    > | mercantile shapes --indent 2 --precision 6
    {
      "features": [
        {
          "geometry": {
            "coordinates": [
              [
                [
                  -105.007324,
                  39.993956
                ],
                [
                  -105.007324,
                  40.010787
                ],
                [
                  -104.985352,
                  40.010787
                ],
                [
                  -104.985352,
                  39.993956
                ],
                [
                  -105.007324,
                  39.993956
                ]
              ]
            ],
            "type": "Polygon"
          },
          "id": "(3413, 6202, 14)",
          "properties": {
            "title": "XYZ tile (3413, 6202, 14)"
          },
          "type": "Feature"
        },
        {
          "geometry": {
            "coordinates": [
              [
                [
                  -105.007324,
                  39.97712
                ],
                [
                  -105.007324,
                  39.993956
                ],
                [
                  -104.985352,
                  39.993956
                ],
                [
                  -104.985352,
                  39.97712
                ],
                [
                  -105.007324,
                  39.97712
                ]
              ]
            ],
            "type": "Polygon"
          },
          "id": "(3413, 6203, 14)",
          "properties": {
            "title": "XYZ tile (3413, 6203, 14)"
          },
          "type": "Feature"
        }
      ],
      "type": "FeatureCollection"
    }

or do a round trip like this

.. code-block:: console

    $  echo "[106, 193, 9]" | mercantile shapes | mercantile tiles 9
    [106, 193, 9]

If you have `geojsonio-cli <https://github.com/mapbox/geojsonio-cli>`__
installed, you can shoot this GeoJSON straight to `geojson.io
<http://geojson.io/>`__ for lightning-fast visualization and editing.

.. code-block:: console

    $ echo "[-104.99, 39.99, -105, 40]" \
    > | mercantile tiles 14 \
    > | mercantile shapes --compact \
    > | geojsonio