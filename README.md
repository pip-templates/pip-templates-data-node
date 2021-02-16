# <img src="https://github.com/pip-services/pip-services/raw/master/design/Logo.png" alt="Pip.Services Logo" style="max-width:30%"> <br/> Beacons microservice

This microservice stores information about BLE beacons that emit unique device IDs (UDIs) over bluetooth protocol.
And then it uses that information to triangulate position of users based on UDIs their devices detect around them.

Supported functionality:
* Deployment platforms: Standalone Process, Docker, AWS Lambda
* External APIs: HTTP (REST and Commandable), GRPC (Custom and Commandable)
* Persistence: Memory, Flat Files, MongoDB, PosgreSQL (Relational and NoSQL), SQLServer (Relational and NoSQL), MySql (Relational and NoSQL), Couchbase
* Health checks: Heartbeat, Status
* Consolidated logging: ElasticSearch, CloudWatch, DataDog
* Consolidated metrics: Prometheus, CloudWatch, DataDog

This microservice does not depend on other microservices.

<a name="links"></a> Quick links:

* Communication Protocols:
  - [gRPC Version 1](src/protos/beacons_v1.proto)
  - [HTTP Version 1](src/swagger/beacons_v1.yaml)
* Client SDKs:
  - [Node.js SDK](https://github.com/pip-templates/pip-clients-beacons-node)
  - [.NET SDK](https://github.com/pip-templates/pip-clients-beacons-dotnet)
  - [Golang SDK](https://github.com/pip-templates/pip-clients-beacons-go)
* [API Reference](https://pip-templates.github.io/pip-services-beacons-node/globals.html)
* [Change Log](CHANGELOG.md)


## Contract

The contract of the microservice is presented below. 

```javascript
export class BeaconV1 implements IStringIdentifiable {
    public id: string;          // Beacon ID
    public site_id: string;     // ID of a work site (field installation)
    public type?: string;       // Beacon type: iBeacon, AltBeacon or Eddystone
    public udi: string;         // Unique device identifier emitted by the beacon
    public label?: string;      // Human readable label
    public center?: any;        // Position of the beacon in GeoJson
    public radius?: number;     // Detection radius
}

// GeoJson Example:
// {
//     "type": "Point", 
//     "coordinates": [30, 10]
// }

export interface IBeaconsClientV1 {
    getBeacons(correlationId: string, filter: FilterParams, paging: PagingParams,
        callback: (err: any, page: DataPage<BeaconV1>) => void): void;

    getBeaconById(correlationId: string, beaconId: string,
        callback: (err: any, beacon: BeaconV1) => void): void;

    getBeaconByUdi(correlationId: string, udi: string,
        callback: (err: any, beacon: BeaconV1) => void): void;

    calculatePosition(correlationId: string, siteId: string, udis: string[], 
        callback: (err: any, position: any) => void): void;

    createBeacon(correlationId: string, beacon: BeaconV1,
        callback: (err: any, beacon: BeaconV1) => void): void;

    updateBeacon(correlationId: string, beacon: BeaconV1,
        callback: (err: any, beacon: BeaconV1) => void): void;

    deleteBeaconById(correlationId: string, beaconId: string,
        callback: (err: any, beacon: BeaconV1) => void): void;            
}

```

## Get

Get the microservice source from GitHub:
```bash
git clone git@github.com:pip-templates/pip-services-beacons-node.git
```

Install the microservice as a binary dependency:
```bash
npm install pip-services-beacons-node
```

Get docker image for the microservice:
```bash
docker pull pipdevs/pip-services-beacons-node:latest
```
## Run

The microservice can be configured using the environment variables:
* AWS_LAMDBA_ARN - a unique Amazon Resource Name
* AWS_ACCESS_ID - AWS access/client id
* AWS_ACCESS_KEY - AWS access/client id
* CLOUD_WATCH_ENABLED -  turn on CloudWatch loggers and metrics
* CLOUD_WATCH_STREAM - Cloud Watch Log stream (default: context name)
* CLOUD_WATCH_GROUP - Cloud Watch Log group (default: context instance ID or hostname)
* DATADOG_ENABLED - turn on DataDog loggers and metrics
* DTAT_DOG_PROTOCOL - (optional) connection protocol: http or https (default: https)
* DATADOG_URI - (optional) resource URI or connection string with all parameters in it
* DATADOG_HOST - (optional) host name or IP address (default: api.datadoghq.com)
* DATADOG_PORT - (optional) port number (default: 443)
* DATADOG_ACCRSS_KEY - DataDog client api key
* ELASTICSEARCH_LOGGING_ENABLED - turn on Elasticsearch logs and metrics
* ELASTICSEARCH_PROTOCOL - connection protocol: http or https
* ELASTICSEARCH_SERVICE_URI - resource URI or connection string with all parameters in it
* ELASTICSEARCH_SERVICE_HOST - host name or IP address
* ELASTICSEARCH_SERVICE_PORT - port number
* FILE_ENABLED - turn on file persistence. Keep it undefined to turn it off
* FILE_PATH - file path where persistent data shall be stored (default: ../data/id_records.json) 
* MEMORY_ENABLED - turn on in-memory persistence. Keep it undefined to turn it off
* MONGO_ENABLED - turn on MongoDB persistence. Keep it undefined to turn it off
* MONGO_SERVICE_URI - URI to connect to MongoDB. When it's defined other database parameters are ignored
* MONGO_SERVICE_HOST - MongoDB hostname or server address
* MONGO_SERVICE_PORT - MongoDB port number (default: 3360)
* MONGO_DB - MongoDB database name (default: app)
* MONGO_COLLECTION - MongoDB collection (default: id_records)
* MONGO_USER - MongoDB user login
* MONGO_PASS - MongoDB user password
* MYSQL_ENABLED - turn on MySql persistence. Keep it undefined to turn it off
* MYSQL_JSON_ENABLED - turn on JSON MySql persistence. Keep it undefined to turn it off
* MYSQL_URI - URI to connect to MySql. When it's defined other database parameters are ignored
* MYSQL_HOST - MySql hostname or server address
* MYSQL_PORT - MySql port number (default: 3306)
* MYSQL_DB - MySql database name (default: test)
* MYSQL_USER - MySql user login
* MYSQL_PASSWORD - MySql user password
* POSTGRES_ENABLED - turn on PostgreSQL persistence. Keep it undefined to turn it off
* POSTGRES_JSON_ENABLED - turn on JSON PostgreSQL persistence. Keep it undefined to turn it off
* POSTGRES_SERVICE_URI - URI to connect to PostgreSQL. When it's defined other database parameters are ignored
* POSTGRES_SERVICE_HOST - PostgreSQL hostname or server address
* POSTGRES_SERVICE_PORT - PostgreSQL port number (default: 5432)
* POSTGRES_DB - PostgreSQL database name (default: app)
* POSTGRES_TABLE - PostgreSQL table (default: id_records)
* POSTGRES_USER - PostgreSQL user login
* POSTGRES_PASS - PostgreSQL user password
* PUSHGATEWAY_METRICS_ENABLED - turn on pushgetway for prometheus
* PUSHGATEWAY_PROTOCOL - connection protocol: http or https
* PUSHGATEWAY_METRICS_SERVICE_URI - resource URI or connection string with all parameters in it
* PUSHGATEWAY_METRICS_SERVICE_HOST - host name or IP address
* PUSHGATEWAY_METRICS_SERVICE_PORT - port number
* SQLSERVER_ENABLED - turn on SQL Server persistence. Keep it undefined to turn it off
* SQLSERVER_JSON_ENABLED - turn on JSON SQL Server persistence. Keep it undefined to turn it off
* SQLSERVER_SERVICE_URI - URI to connect to SQL Server. When it's defined other database parameters are ignored
* SQLSERVER_SERVICE_HOST - SQL Server hostname or server address
* SQLSERVER_SERVICE_PORT - SQL Server port number (default: 1433)
* SQLSERVER_DB - SQL Server database name (default: app)
* SQLSERVER_TABLE - SQL Server table (default: id_records)
* SQLSERVER_USER - SQL Server user login
* SQLSERVER_PASS - SQL Server user password
* HTTP_ENABLED - turn on HTTP endpoint
* HTTP_PORT - HTTP port number (default: 8080)
* GRPC_ENABLED - turn on GRPC endpoint
* GRPC_PORT - GRPC port number (default: 8090)


Start the microservice as process:
```bash
node ./bin/main.js
```

Run the microservice in docker:
Then use the following command:
```bash
./run.ps1
```

Launch the microservice with all infrastructure services using docker-compose:
```bash
docker-compose -f ./docker/docker-compose.yml up
```

## Use

Install the client NPM package as
```bash
npm install pip-clients-beacons-node
```

Inside your code get the reference to the client SDK
```javascript
import { ConfigParams } from 'pip-services3-commons-node';
import { FilterParams } from 'pip-services3-commons-node';
import { PagingParams } from 'pip-services3-commons-node';

import { BeaconV1 } from 'pip-services-beacons-node';
import { BeaconTypeV1 } from 'pip-services-beacons-node';
import { BeaconsCommandableHttpClientV1 } from 'pip-services-beacons-node';
```

Instantiate the client
```javascript
// Create the client instance
let client = new BeaconsCommandableHttpClientV1();
```

Define client connection parameters
```javascript
// Client configuration
var httpConfig = ConfigParams.fromTuples(
	"connection.protocol", "http",
	"connection.host", "localhost",
	"connection.port", 8080
);
// Configure the client
client.configure(httpConfig);
```

Connect to the microservice
```javascript
// Connect to the microservice
client.open(null, (err) => {
  if (err) {
    // Error handling...
  }
});
    
// Work with the microservice
...
```

Call the microservice using the client API
```javascript
// Define a beacon
let beacon: BeaconV1 = {
    id: '1',
    udi: '00001',
    type: BeaconTypeV1.AltBeacon,
    site_id: '1',
    label: 'TestBeacon',
    center: { type: 'Point', coordinates: [ 0, 0 ] },
    radius: 50
};

async.series([
    // Create the beacon
    (callback) => {
        this.client.createBeacon(
            null,
            BEACON1,
            (err, beacon) => {
                if(err){
                  // Error handling...
                }
                
                // Do something with the returned beacon...

                callback();
            }
        );
    },
    // Get a list of beacons
    (callback) => {
        this.client.getBeacons(
            null,
            FilterParams.fromTuples(
              "label", "TestBeacon",
            ),
            new PagingParams(0, 10),
            (err, page) => {
                if(err){
                  // Error handling...
                }

                // Do something with the returned page...
                // E.g. beacon = page.data[0];

                callback();
            }
        )
    }
], (err, results) => {
    if (err) {
        // Error handling...
    }
});
```

## Develop

For development you shall install the following prerequisites:
* Node.js
* Visual Studio Code or another IDE of your choice
* Docker
* Typescript

Install dependencies:
```bash
npm install
```

Compile the microservice:
```bash
tsc
```

Before running tests launch infrastructure services and required microservices:
```bash
docker-compose -f ./docker-compose.dev.yml up
```

Run automated tests:
```bash
npm test
```

Run automated benchmarks:
```bash
npm run benchmark
```

Generate GRPC protobuf stubs:
```bash
./protogen.ps1
```

Generate API documentation:
```bash
./docgen.ps1
```

Before committing changes run dockerized build and test as:
```bash
./build.ps1
./test.ps1
./package.ps1
./run.ps1
./clear.ps1
```

## Contacts

This microservice was created and currently maintained by *Sergey Seroukhov*.
