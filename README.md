# logstash-config-templates

Config templates for using logstash to load log data into an elasticsearch cluster

Log data source is designed to come in from an S3 repository upon ingestion it is moved to a processed area of the bucket after which lifecycle rules should delete the log files after a period (current thinking is 6 months)

Imports should happen as regularly as possible but currently envisioned to run on a monthly basis until a more automated process can be created, elasticsearch server containing logs would be hosted in AWS and powered down when not in use (manual install rather than AWS elasticsearch services due to cost considerations as this is not a live query service, only post-hoc log analysis).

Requires `geoip` plugin and a geoip database

## Log formats [current list to be supported, may have more or less]

- IIS
  - W3C
- AWS
  - S3
  - ELB
- Apache
  - default
  - extra [Internal JNCC format includes IO metrics] - "%h %l %u %t \"%r\" %>s %D %I %O \"%{Referer}i\" \"%{User-Agent}i\"
- Nginx
- Tomcat

## Elasticsearch output format

```json
{
  "request": {
    "verb": "GET|POST|DELETE|HEAD|... HTTP Verb of request",
    "user_agent": "... User agent supplied by user",
    "path": "... Path requested by user",
    "ip": "x.x.x.x Originating IP of user [cleared when moved to long term format]",
    "querystring": "... Original querystring requested by user",
    "geoip": {
      "region_code": "...",
      "postal_code": "...",
      "country_code3": "...",
      "city_name": "...",
      "longitude": 0,
      "country_code2": "...",
      "region_name": "...",
      "ip": "x.x.x.x Originating IP of user [cleared when moved to long term format]",
      "latitude": 0,
      "timezone": "...",
      "country_name": "...",
      "location": {
        "lat": 0,
        "lon": 0
      },
      "coordinates": [
        0,
        0
      ],
      "continent_code": "...",
      "dma_code": 0
    },
    "service_specific": {
      "service_speicfic_response": "... Service Specific request values"
    }
  },
  "response": {
    "status": "... HTTP Status code",
    "time": "... Response time (preferably in milliseconds)",
    "service_specific": {
      "service_speicfic_response": "... Service Specific response values i.e. IIS->sub_status/win32_status"
    }
  },
  "...": {}
}
```