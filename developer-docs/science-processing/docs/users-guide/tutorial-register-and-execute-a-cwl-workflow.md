# Tutorial: Register and Execute a CWL Workflow

## Pre-Requisite

This tutorial assumes that the CWL workflow already exists at some public location on the internet, as does a YAML file containing specific values for all the parameters needed by the workflow. Example:

* CWL workflow file: https://raw.githubusercontent.com/unity-sds/sbg-workflows/main/L1-to-L2-e2e.cwl
* YAML parameters file: https://raw.githubusercontent.com/unity-sds/sbg-workflows/main/L1-to-L2-e2e.dev.yml

## Step 1: Register the generic CWL DAG

The Unity SPS comes bundled with a DAG that allows to execute a generic CWL workflow. When SPS is first installed, this DAG does not show up in the Airflow UI because it is not registered yet. To register it, we must execute an HTTP request to the OGC "register" method exposed by the SPS. The id of the DAG (which is part of the request) is "cwl\_dag\_new".

First define the SPS OGC endpoint, for example: export OGC\_API = http://k8s-airflow-ogcproce-abcdefgh1j-123456789.us-west-2.elb.amazonaws.com:5001

Then execute the HTTP request:

<details>

<summary>Request</summary>

```sh
curl -s -0 -X POST "${OGC_API}/processes" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF | jq
{
   "id": "cwl_dag_new",
   "title": "DAG to execute a generic CWL workflow",
   "description": "placeholder description",
   "version": "1.0.0",
   "jobControlOptions": [
       "async-execute",
       "sync-execute"
   ],
   "inputs": [{
       "stringInput": {
       "title": "String Literal Input Example",
       "description": "This is an example of a STRING literal input.",
       "schema": {
           "type": "string",
           "enum": [
           "Value1",
           "Value2",
           "Value3"
           ]
       }
       },
       "measureInput": {
       "title": "Numerical Value with UOM Example",
       "description": "This is an example of a NUMERIC literal with an associated unit of measure.",
       "schema": {
           "type": "object",
           "required": [
           "measurement",
           "uom"
           ],
           "properties": {
           "measurement": {
               "type": "number"
           },
           "uom": {
               "type": "string"
           },
           "reference": {
               "type": "string",
               "format": "uri"
           }
           }
       }
       },
       "dateInput": {
       "title": "Date Literal Input Example",
       "description": "This is an example of a DATE literal input.",
       "schema": {
           "type": "string",
           "format": "date-time"
       }
       },
       "doubleInput": {
       "title": "Bounded Double Literal Input Example",
       "description": "This is an example of a DOUBLE literal input that is bounded between a value greater than 0 and 10.  The default value is 5.",
       "schema": {
           "type": "number",
           "format": "double",
           "minimum": 0,
           "maximum": 10,
           "default": 5,
           "exclusiveMinimum": true
       }
       },
       "arrayInput": {
       "title": "Array Input Example",
       "description": "This is an example of a single process input that is an array of values.  In this case, the input array would be interpreted as a single value and not as individual inputs.",
       "schema": {
           "type": "array",
           "minItems": 2,
           "maxItems": 10,
           "items": {
           "type": "integer"
           }
       }
       },
       "complexObjectInput": {
       "title": "Complex Object Input Example",
       "description": "This is an example of a complex object input.",
       "schema": {
           "type": "object",
           "required": [
           "property1",
           "property5"
           ],
           "properties": {
           "property1": {
               "type": "string"
           },
           "property2": {
               "type": "string",
               "format": "uri"
           },
           "property3": {
               "type": "number"
           },
           "property4": {
               "type": "string",
               "format": "date-time"
           },
           "property5": {
               "type": "boolean"
           }
           }
       }
       },
       "geometryInput": {
       "title": "Geometry input",
       "description": "This is an example of a geometry input.  In this case the geometry can be expressed as a GML of GeoJSON geometry.",
       "minOccurs": 2,
       "maxOccurs": 5,
       "schema": {
           "oneOf": [
           {
               "type": "string",
               "contentMediaType": "application/gml+xml; version=3.2",
               "contentSchema": "http://schemas.opengis.net/gml/3.2.1/geometryBasic2d.xsd"
           },
           {
               "format": "geojson-geometry"
           }
           ]
       }
       },
       "boundingBoxInput": {
       "title": "Bounding Box Input Example",
       "description": "This is an example of a BBOX literal input.",
       "schema": {
           "allOf": [
           {
               "format": "ogc-bbox"
           },
           {
               "$ref": "../../openapi/schemas/bbox.yaml"
           }
           ]
       }
       },
       "imagesInput": {
       "title": "Inline Images Value Input",
       "description": "This is an example of an image input.  In this case, the input is an array of up to 150 images that might, for example, be a set of tiles.  The oneOf[] conditional is used to indicate the acceptable image content types; GeoTIFF and JPEG 2000 in this case.  Each input image in the input array can be included inline in the execute request as a base64-encoded string or referenced using the link.yaml schema.  The use of a base64-encoded string is implied by the specification and does not need to be specified in the definition of the input.",
       "minOccurs": 1,
       "maxOccurs": 150,
       "schema": {
           "oneOf": [
           {
               "type": "string",
               "contentEncoding": "binary",
               "contentMediaType": "image/tiff; application=geotiff"
           },
           {
               "type": "string",
               "contentEncoding": "binary",
               "contentMediaType": "image/jp2"
           }
           ]
       }
       },
       "featureCollectionInput": {
       "title": "Feature Collection Input Example.",
       "description": "This is an example of an input that is a feature collection that can be encoded in one of three ways: as a GeoJSON feature collection, as a GML feature collection retrieved from a WFS or as a KML document.",
       "schema": {
           "oneOf": [
           {
               "type": "string",
               "contentMediaType": "application/gml+xml; version=3.2"
           },
           {
               "type": "string",
               "contentSchema": "https://schemas.opengis.net/kml/2.3/ogckml23.xsd",
               "contentMediaType": "application/vnd.google-earth.kml+xml"
           },
           {
               "allOf": [
               {
                   "format": "geojson-feature-collection"
               },
               {
                   "$ref": "https://geojson.org/schema/FeatureCollection.json"
               }
               ]
           }
           ]
       }
       }
   }],
   "outputs": [{
       "stringOutput": {
       "schema": {
           "type": "string",
           "enum": [
           "Value1",
           "Value2",
           "Value3"
           ]
       }
       },
       "measureOutput": {
       "schema": {
           "type": "object",
           "required": [
           "measurement",
           "uom"
           ],
           "properties": {
           "measurement": {
               "type": "number"
           },
           "uom": {
               "type": "string"
           },
           "reference": {
               "type": "string",
               "format": "uri"
           }
           }
       }
       },
       "dateOutput": {
       "schema": {
           "type": "string",
           "format": "date-time"
       }
       },
       "doubleOutput": {
       "schema": {
           "type": "number",
           "format": "double",
           "minimum": 0,
           "maximum": 10,
           "default": 5,
           "exclusiveMinimum": true
       }
       },
       "arrayOutput": {
       "schema": {
           "type": "array",
           "minItems": 2,
           "maxItems": 10,
           "items": {
           "type": "integer"
           }
       }
       },
       "complexObjectOutput": {
       "schema": {
           "type": "object",
           "required": [
           "property1",
           "property5"
           ],
           "properties": {
           "property1": {
               "type": "string"
           },
           "property2": {
               "type": "string",
               "format": "uri"
           },
           "property3": {
               "type": "number"
           },
           "property4": {
               "type": "string",
               "format": "date-time"
           },
           "property5": {
               "type": "boolean"
           }
           }
       }
       },
       "geometryOutput": {
       "schema": {
           "oneOf": [
           {
               "type": "string",
               "contentMediaType": "application/gml+xml",
               "contentSchema": "http://schemas.opengis.net/gml/3.2.1/geometryBasic2d.xsd"
           },
           {
               "allOf": [
               {
                   "format": "geojson-geometry"
               },
               {
                   "$ref": "http://schemas.opengis.net/ogcapi/features/part1/1.0/openapi/schemas/geometryGeoJSON.yaml"
               }
               ]
           }
           ]
       }
       },
       "boundingBoxOutput": {
       "schema": {
           "allOf": [
           {
               "format": "ogc-bbox"
           },
           {
               "$ref": "../../openapi/schemas/bbox.yaml"
           }
           ]
       }
       },
       "imagesOutput": {
       "schema": {
           "oneOf": [
           {
               "type": "string",
               "contentEncoding": "binary",
               "contentMediaType": "image/tiff; application=geotiff"
           },
           {
               "type": "string",
               "contentEncoding": "binary",
               "contentMediaType": "image/jp2"
           }
           ]
       }
       },
       "featureCollectionOutput": {
       "schema": {
           "oneOf": [
           {
               "type": "string",
               "contentMediaType": "application/gml+xml; version=3.2"
           },
           {
               "type": "string",
               "contentMediaType": "application/vnd.google-earth.kml+xml",
               "contentSchema": "https://schemas.opengis.net/kml/2.3/ogckml23.xsd"
           },
           {
               "allOf": [
               {
                   "format": "geojson-feature-collection"
               },
               {
                   "$ref": "https://geojson.org/schema/FeatureCollection.json"
               }
               ]
           }
           ]
       }
       }
   }],
   "links": [
       {
       "href": "https://processing.example.org/oapi-p/processes/EchoProcess/execution",
       "rel": "http://www.opengis.net/def/rel/ogc/1.0/execute",
       "title": "Execute endpoint"
       }
   ]
}
EOF
```

</details>

<details>

<summary>Expected Response</summary>

```
{
    "title": "SBG L1 to L2 end-to-end CWL step by step dag",
    "description": "placeholder description",
    "metadata": null,
    "id": "sbg_L1_to_L2_e2e_cwl_step_by_step_dag",
    "version": "1.0.0",
    "jobControlOptions": [
        "async-execute",
        "sync-execute"
    ],
    "links": [
        {
            "href": "https://processing.example.org/oapi-p/processes/EchoProcess/execution",
            "rel": "http://www.opengis.net/def/rel/ogc/1.0/execute",
            "type": null,
            "hreflang": null,
            "title": "Execute endpoint"
        }
    ],
    "inputs": [
        {
            "stringInput": {
                "title": "String Literal Input Example",
                "description": "This is an example of a STRING literal input.",
                "schema": {
                    "type": "string",
                    "enum": [
                        "Value1",
                        "Value2",
                        "Value3"
                    ]
                }
            },
            "measureInput": {
                "title": "Numerical Value with UOM Example",
                "description": "This is an example of a NUMERIC literal with an associated unit of measure.",
                "schema": {
                    "type": "object",
                    "required": [
                        "measurement",
                        "uom"
                    ],
                    "properties": {
                        "measurement": {
                            "type": "number"
                        },
                        "uom": {
                            "type": "string"
                        },
                        "reference": {
                            "type": "string",
                            "format": "uri"
                        }
                    }
                }
            },
            "dateInput": {
                "title": "Date Literal Input Example",
                "description": "This is an example of a DATE literal input.",
                "schema": {
                    "type": "string",
                    "format": "date-time"
                }
            },
            "doubleInput": {
                "title": "Bounded Double Literal Input Example",
                "description": "This is an example of a DOUBLE literal input that is bounded between a value greater than 0 and 10.  The default value is 5.",
                "schema": {
                    "type": "number",
                    "format": "double",
                    "minimum": 0,
                    "maximum": 10,
                    "default": 5,
                    "exclusiveMinimum": true
                }
            },
            "arrayInput": {
                "title": "Array Input Example",
                "description": "This is an example of a single process input that is an array of values.  In this case, the input array would be interpreted as a single value and not as individual inputs.",
                "schema": {
                    "type": "array",
                    "minItems": 2,
                    "maxItems": 10,
                    "items": {
                        "type": "integer"
                    }
                }
            },
            "complexObjectInput": {
                "title": "Complex Object Input Example",
                "description": "This is an example of a complex object input.",
                "schema": {
                    "type": "object",
                    "required": [
                        "property1",
                        "property5"
                    ],
                    "properties": {
                        "property1": {
                            "type": "string"
                        },
                        "property2": {
                            "type": "string",
                            "format": "uri"
                        },
                        "property3": {
                            "type": "number"
                        },
                        "property4": {
                            "type": "string",
                            "format": "date-time"
                        },
                        "property5": {
                            "type": "boolean"
                        }
                    }
                }
            },
            "geometryInput": {
                "title": "Geometry input",
                "description": "This is an example of a geometry input.  In this case the geometry can be expressed as a GML of GeoJSON geometry.",
                "minOccurs": 2,
                "maxOccurs": 5,
                "schema": {
                    "oneOf": [
                        {
                            "type": "string",
                            "contentMediaType": "application/gml+xml; version=3.2",
                            "contentSchema": "http://schemas.opengis.net/gml/3.2.1/geometryBasic2d.xsd"
                        },
                        {
                            "format": "geojson-geometry"
                        }
                    ]
                }
            },
            "boundingBoxInput": {
                "title": "Bounding Box Input Example",
                "description": "This is an example of a BBOX literal input.",
                "schema": {
                    "allOf": [
                        {
                            "format": "ogc-bbox"
                        },
                        {
                            "$ref": "../../openapi/schemas/bbox.yaml"
                        }
                    ]
                }
            },
            "imagesInput": {
                "title": "Inline Images Value Input",
                "description": "This is an example of an image input.  In this case, the input is an array of up to 150 images that might, for example, be a set of tiles.  The oneOf[] conditional is used to indicate the acceptable image content types; GeoTIFF and JPEG 2000 in this case.  Each input image in the input array can be included inline in the execute request as a base64-encoded string or referenced using the link.yaml schema.  The use of a base64-encoded string is implied by the specification and does not need to be specified in the definition of the input.",
                "minOccurs": 1,
                "maxOccurs": 150,
                "schema": {
                    "oneOf": [
                        {
                            "type": "string",
                            "contentEncoding": "binary",
                            "contentMediaType": "image/tiff; application=geotiff"
                        },
                        {
                            "type": "string",
                            "contentEncoding": "binary",
                            "contentMediaType": "image/jp2"
                        }
                    ]
                }
            },
            "featureCollectionInput": {
                "title": "Feature Collection Input Example.",
                "description": "This is an example of an input that is a feature collection that can be encoded in one of three ways: as a GeoJSON feature collection, as a GML feature collection retrieved from a WFS or as a KML document.",
                "schema": {
                    "oneOf": [
                        {
                            "type": "string",
                            "contentMediaType": "application/gml+xml; version=3.2"
                        },
                        {
                            "type": "string",
                            "contentSchema": "https://schemas.opengis.net/kml/2.3/ogckml23.xsd",
                            "contentMediaType": "application/vnd.google-earth.kml+xml"
                        },
                        {
                            "allOf": [
                                {
                                    "format": "geojson-feature-collection"
                                },
                                {
                                    "$ref": "https://geojson.org/schema/FeatureCollection.json"
                                }
                            ]
                        }
                    ]
                }
            }
        }
    ],
    "outputs": [
        {
            "stringOutput": {
                "schema": {
                    "type": "string",
                    "enum": [
                        "Value1",
                        "Value2",
                        "Value3"
                    ]
                }
            },
            "measureOutput": {
                "schema": {
                    "type": "object",
                    "required": [
                        "measurement",
                        "uom"
                    ],
                    "properties": {
                        "measurement": {
                            "type": "number"
                        },
                        "uom": {
                            "type": "string"
                        },
                        "reference": {
                            "type": "string",
                            "format": "uri"
                        }
                    }
                }
            },
            "dateOutput": {
                "schema": {
                    "type": "string",
                    "format": "date-time"
                }
            },
            "doubleOutput": {
                "schema": {
                    "type": "number",
                    "format": "double",
                    "minimum": 0,
                    "maximum": 10,
                    "default": 5,
                    "exclusiveMinimum": true
                }
            },
            "arrayOutput": {
                "schema": {
                    "type": "array",
                    "minItems": 2,
                    "maxItems": 10,
                    "items": {
                        "type": "integer"
                    }
                }
            },
            "complexObjectOutput": {
                "schema": {
                    "type": "object",
                    "required": [
                        "property1",
                        "property5"
                    ],
                    "properties": {
                        "property1": {
                            "type": "string"
                        },
                        "property2": {
                            "type": "string",
                            "format": "uri"
                        },
                        "property3": {
                            "type": "number"
                        },
                        "property4": {
                            "type": "string",
                            "format": "date-time"
                        },
                        "property5": {
                            "type": "boolean"
                        }
                    }
                }
            },
            "geometryOutput": {
                "schema": {
                    "oneOf": [
                        {
                            "type": "string",
                            "contentMediaType": "application/gml+xml",
                            "contentSchema": "http://schemas.opengis.net/gml/3.2.1/geometryBasic2d.xsd"
                        },
                        {
                            "allOf": [
                                {
                                    "format": "geojson-geometry"
                                },
                                {
                                    "$ref": "http://schemas.opengis.net/ogcapi/features/part1/1.0/openapi/schemas/geometryGeoJSON.yaml"
                                }
                            ]
                        }
                    ]
                }
            },
            "boundingBoxOutput": {
                "schema": {
                    "allOf": [
                        {
                            "format": "ogc-bbox"
                        },
                        {
                            "$ref": "../../openapi/schemas/bbox.yaml"
                        }
                    ]
                }
            },
            "imagesOutput": {
                "schema": {
                    "oneOf": [
                        {
                            "type": "string",
                            "contentEncoding": "binary",
                            "contentMediaType": "image/tiff; application=geotiff"
                        },
                        {
                            "type": "string",
                            "contentEncoding": "binary",
                            "contentMediaType": "image/jp2"
                        }
                    ]
                }
            },
            "featureCollectionOutput": {
                "schema": {
                    "oneOf": [
                        {
                            "type": "string",
                            "contentMediaType": "application/gml+xml; version=3.2"
                        },
                        {
                            "type": "string",
                            "contentMediaType": "application/vnd.google-earth.kml+xml",
                            "contentSchema": "https://schemas.opengis.net/kml/2.3/ogckml23.xsd"
                        },
                        {
                            "allOf": [
                                {
                                    "format": "geojson-feature-collection"
                                },
                                {
                                    "$ref": "https://geojson.org/schema/FeatureCollection.json"
                                }
                            ]
                        }
                    ]
                }
            }
        }
    ]
}
```

</details>

If the register request was successful, the CWL DAG will now appear after logging into the Airflow User Interface (for example: http://k8s-airflow-airflowi-abcdefghij-123456789.us-west-2.elb.amazonaws.com:5000)

## Step 2: Trigger execution of the CWL worklow

The easiest way to trigger execution of the CWL workflow is to use the Airflow User Interface:

* On the Airflow User Interface, locate the CWL DAG and click on the arrow button on the right
* Add the desired values for the "CWL workflow" and "CWL workflow parameters fields"
* Click the "Trigger" button

You can follow the execution of the DAG run by going back to the main Airflow home and clicking on the "Last Run" value.

Alternatively, you can execute the CWL DAG by making an HTTP request to the OGC "process" method exposed by the SPS.
