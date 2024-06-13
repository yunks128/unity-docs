# Tutorial: Register, Execute, and Unregister a Process using the OGC API - Processes

#### Step 1 (optional) - Deploy a U-SPS Cluster in the MCP Dev Account

* **Note:** You can skip this step if you're using the OGC API - Processes of a cluster that has already been deployed. You'll just need the URL of the API.

```sh
local-machine) $ cd unity-sps/terraform-unity
(local-machine) $ terraform apply -var-file=MCP_DEV.tfvars -no-color 2>&1 --auto-approve | tee apply_output.txt
# Get the hostnames of the load balancers
(local-machine) $ terraform output
resources = {
  "buckets" = {
    "airflow_logs" = {
      "bucket" = "unity-dev-sps-airflowlogs-drew-1"
      "ssm_param_id" = "/unity/dev/sps/drew/1/processing/airflow/logs"
    }
  }
  "endpoints" = {
    "airflow" = {
      "rest_api" = {
        "ssm_param_id" = "/unity/dev/sps/drew/1/processing/airflow/api_url"
        "url" = "http://k8s-airflow-airflowi-7d38808288-1998282987.us-west-2.elb.amazonaws.com:5000/api/v1"
      }
      "ui" = {
        "ssm_param_id" = "/unity/dev/sps/drew/1/processing/airflow/ui_url"
        "url" = "http://k8s-airflow-airflowi-7d38808288-1998282987.us-west-2.elb.amazonaws.com:5000"
      }
    }
    "ogc_processes" = {
      "rest_api" = {
        "ssm_param_id" = "/unity/dev/sps/drew/1/processing/ogc_processes/api_url"
        "url" = "http://k8s-airflow-ogcproce-0a48b6d5b4-206583772.us-west-2.elb.amazonaws.com:5001"
      }
      "ui" = {
        "ssm_param_id" = "/unity/dev/sps/drew/1/processing/ogc_processes/ui_url"
        "url" = "http://k8s-airflow-ogcproce-0a48b6d5b4-206583772.us-west-2.elb.amazonaws.com:5001/redoc"
      }
    }
  }
}
# Set the the value of OGC_API for future API requests
(local-machine) OGC_API=http://k8s-airflow-ogcproce-0a48b6d5b4-206583772.us-west-2.elb.amazonaws.com:5001
```

#### Step 2 - Deploy a Process Named `sbg_L1_to_L2_e2e_cwl_step_by_step_dag` with the OGC API

<details>

<summary>Request</summary>

```sh
curl -s -0 -X POST "${OGC_API}/processes" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF | jq
{
   "id": "sbg_L1_to_L2_e2e_cwl_step_by_step_dag",
   "title": "SBG L1 to L2 end-to-end CWL step by step dag",
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

#### Step 3 - Trigger Execution of the `sbg_L1_to_L2_e2e_cwl_step_by_step_dag` Process Through the OGC API

<details>

<summary>Request</summary>

```
curl -0 -s -D - -o /dev/null "${OGC_API}/processes/sbg_L1_to_L2_e2e_cwl_step_by_step_dag/execution" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF
{
  "inputs": {
    "arrayInput": [
      1,
      2,
      3,
      4,
      5,
      6
    ],
    "boundingBoxInput": {
      "bbox": [
        51.9,
        7,
        52,
        7.1
      ],
      "crs": "http://www.opengis.net/def/crs/OGC/1.3/CRS84"
    },
    "complexObjectInput": {
      "value": {
        "property1": "value1",
        "property2": "value2",
        "property5": true
      }
    },
    "dateInput": "2021-03-06T07:21:00",
    "doubleInput": 3.14159,
    "featureCollectionInput": {
      "mediaType": "application/gml+xml; version=3.2",
      "value": "<?xml version=\"1.0\" encoding=\"UTF-8\"?><FeatureCollection xmlns=\"http://schemas.myserver.com/namespaces/null\" xmlns:gml=\"http://www.opengis.net/gml/3.2\" xmlns:xsi=\"http://www.w3.org/2001/XMLSchema-instance\" xsi:schemaLocation=\"http://schemas.myserver.com/namespaces/null https://www.pvretano.com/myserver/ogcapi/daraa/schema?f=GML32&amp;collectionids=TransportationGroundCrv http://www.opengis.net/gml/3.2 http://schemas.opengis.net/schemas/gml/3.2.1/gml.xsd\">..."
    },
    "geometryInput": [
      {
        "mediaType": "application/gml+xml; version=3.2",
        "value": "<gml:Polygon gml:id=\"GID1\" srsName=\"urn:ogc:def:crs:OGC::CRS84\"><gml:exterior><gml:LinearRing><gml:posList>-77.024519 38.810529 -77.024635 38.810973 -77.024704 38.810962 -77.024776 38.811239 -77.024957 38.81121 -77.024905 38.811012 -77.024905 38.811012 -77.024865 38.810857 -77.025024 38.810832 -77.025071 38.811012 -77.025203 38.810992 -77.02506 38.810444 -77.024519 38.810529</gml:posList></gml:LinearRing></gml:exterior></gml:Polygon>"
      },
      {
        "value": {
          "coordinates": [
            [
              [
                -176.5814819,
                -44.10896301
              ],
              [
                -176.5818024,
                -44.10964584
              ],
              [
                -176.5844116,
                -44.11236572
              ],
              [
                -176.5935974,
                -44.11021805
              ],
              [
                -176.5973511,
                -44.10743332
              ],
              [
                -176.5950928,
                -44.10562134
              ],
              [
                -176.5858459,
                -44.1043396
              ],
              [
                -176.5811157,
                -44.10667801
              ],
              [
                -176.5814819,
                -44.10896301
              ]
            ]
          ],
          "type": "Polygon"
        }
      }
    ],
    "imagesInput": [
      {
        "href": "https://www.someserver.com/ogcapi/Daraa/collections/Daraa_DTED/styles/Topographic/coverage?...",
        "type": "image/tiff; application=geotiff"
      },
      {
        "encoding": "base64",
        "mediaType": "image/jp2",
        "value": "VBORw0KGgoAAAANSUhEUgAABvwAAAa4CAYAAABMB35kAAABhGlDQ1BJQ0MgcHJvZmlsZQAA\nKJF9kT1Iw0AcxV9TpSL1A+xQxCFDdbIgKuKoVShChVArtOpgcumH0KQhSXFxFFwLDn4sVh1c\nnHV1cBUEwQ8QNzcnRRcp8X9JoUWMB8f9eHfvcfcOEOplplkdY4Cm22Y6mRCzuRUx9IogouhH\n ... \nj3Z5mX7/PCPVRJV92rpHK24xcJrzk20+tkeYlCPqcZNO3Lpni1OJWatPCcmgGDEqx7Om6lfa\nppM4k4BTe9+bsn3L9/9/yWhA0PwQGW8ipCZsnZt9lsdrYEM8z/M8z/M8z/M8z/M8z/MzLWY1\nAAAACUlEQVQ871H6P6JI+TxS5Wn2AAAAAElFTkSuQmCC"
      }
    ],
    "measureInput": {
      "value": {
        "measurement": 10.3,
        "reference": "https://ucum.org/ucum-essence.xml",
        "uom": "m"
      }
    },
    "stringInput": "Value2"
  },
  "outputs": {
    "arrayOutput": {},
    "boundingBoxOutput": {},
    "complexObjectOutput": {},
    "dateOutput": {},
    "doubleOutput": {},
    "featureCollectionOutput": {},
    "geometryOutput": {},
    "imageOutput": {
      "format": {
        "mediaType": "image/tiff; application=geotiff"
      }
    },
    "measureOutput": {},
    "stringOutput": {}
  }
}

EOF
```

</details>

<details>

<summary>Expected Response</summary>

```
{
    "processID": "sbg_L1_to_L2_e2e_cwl_step_by_step_dag",
    "type": "process",
    "jobID": "2a8a0ab9-e39a-4fd6-9e64-4b1db6a7257f",
    "status": "accepted",
    "message": null,
    "exception": null,
    "created": "2024-06-12T22:34:42.228354Z",
    "started": null,
    "finished": null,
    "updated": null,
    "progress": null,
    "links": null
}
```

</details>

#### Step 5a - Check Job Status through the OGC API

* Check the status of the job by it's ID:

```shell
JOB_ID={Find in the `jobID` field in above response header}
watch -n 5 "curl -s "${OGC_API}:5001/processes/sbg_L1_to_L2_e2e_cwl_step_by_step_dag/jobs/${JOB_ID}" | jq"
```

#### Step 5b - Check Job Status through the Airflow UI

* Verify the execution of the process started a `sbg_L1_to_L2_e2e_cwl_step_by_step_dag` DAG run.

#### Step 6 - After the Job Completes, Unregister the Process

