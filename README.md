[![js-standard-style](https://img.shields.io/badge/code%20style-standard-brightgreen.svg)](http://standardjs.com)
[![Build Status](https://travis-ci.org/matrus2/dynamodb-stream-elasticsearch.svg?branch=master)](https://travis-ci.org/matrus2/dynamodb-stream-elasticsearch) 

```
             _                                   _  _     
          __| | _  _  _ _   __ _  _ __   ___  __| || |__  
         / _` || || || ' \ / _` || '  \ / _ \/ _` || '_ \
         \__,_| \_, ||_||_|\__,_||_|_|_|\___/\__,_||_.__/
                |__/  _                                                     
                  ___| |_  _ _  ___  __ _  _ __  
                 (_-<|  _|| '_|/ -_)/ _` || '  \ 
                 /__/ \__||_|  \___|\__,_||_|_|_|
           _            _    _                              _    
      ___ | | __ _  ___| |_ (_) __  ___ ___  __ _  _ _  __ | |_  
     / -_)| |/ _` |(_-<|  _|| |/ _|(_-</ -_)/ _` || '_|/ _|| ' \ 
     \___||_|\__,_|/__/ \__||_|\__|/__/\___|\__,_||_|  \__||_||_|
                                                             
                                                                            
```
# DynamoDB --> Stream --> ElasticSearch

The missing blueprint for AWS Lambda, which reads stream from AWS DynamoDB and writes it to ElasticSearch.
Compatible with node 8.10. (If for some reason you want to use it with node 6.10, then use 1.0.0 of this module)

Whenever data is changed (modified, removed or inserted) in DynamoDB one can use AWS Lambda function to capture this change and update ElasticSearch machine immediately. Further reading:

[Indexing Amazon DynamoDB Content with Amazon Elasticsearch Service Using AWS Lambda](https://aws.amazon.com/blogs/compute/indexing-amazon-dynamodb-content-with-amazon-elasticsearch-service-using-aws-lambda/) 
## Getting Started

Install:
```bash
npm i dynamodb-stream-elasticsearch 
```
Use it in your lambda:
```javascript
const { pushStream } = require('dynamodb-stream-elasticsearch');

const { ES_ENDPOINT, INDEX, TYPE } = process.env;

function myHandler(event, context, callback) {
  console.log('Received event:', JSON.stringify(event, null, 2));
  pushStream({ event, endpoint: ES_ENDPOINT, index: INDEX, type: TYPE })
    .then(() => {
      callback(null, `Successfully processed ${event.Records.length} records.`);
    })
    .catch((e) => {
      callback(`Error ${e}`, null);
    });
}

exports.handler = myHandler;
```
Upload Lambda to AWS and _star_ this repository if it works as expected!!

### Parameters

| Param  | Description | Required
| ------------- | ------------- | ------------- |
| event | Event object generated by the stream (pass it as it is and don't modify)  | required 
| endpoint  | Exact url of ElasticSearch instance (it works with AWS ES and standard ES) (string) | required
| index  | The name of ElasticSearch index (string). If not provided will set the same as DynamoDB table name | optional
| type  | The type of the ElasticSearch document (string). If not provided will set the same as DynamoDB table name | optional
| refresh  | Force ElasticSearch refresh its index immediately [more here](https://www.elastic.co/guide/en/elasticsearch/reference/current/docs-refresh.html) (boolean). Default: true | optional
| transformFunction  | A function/promise to transform each record before sending them to ES. Applies to INSERT and UPDATE operations. If transformFunction returns an empty object or false the row will be skipped. This function will receive `body` (NewImage), `oldBody` (OldImage) and (record) as the whole record as arguments. | optional
| elasticSearchOptions  | Additional set of arguments passed to elasticsearch Client see [here](https://www.elastic.co/guide/en/elasticsearch/client/javascript-api/16.x/configuration.html#config-options) | optional


## Running the tests

### Setup elastic node 
Docker can be used to host a node of elasticsearch

Docker by default tries to pull the ```elasticsearch:latest``` tag from the repository if no version is specifies. 
The tag ```latest`` does not exists, therefore a specific version needs to be specified ie: ```7.2.0```.
### Setup elastic node 
Docker can be used to host a node of elasticsearch

Docker by default tries to pull the ```elasticsearch:latest``` tag from the repository if no version is specifies. 
The tag ```latest`` does not exists, therefore a specific version needs to be specified ie: ```7.2.0```.
To run tests locally you need to have ElasticSearch docker container. Simply type:

```bash
docker run -i -p 9200:9200 --name my_elastic -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.2.0
```   
### Running tests

Tests are written in using mocha [https://mochajs.org/]. Tests can be launched using:

```bash
npm install -g mocha
```                 

Head in the test folder and launch

```bash
mocha index.js
docker run -i -p 9200:9200 --name my_elastic -p 9300:9300 -e "discovery.type=single-node" elasticsearch:7.2.0
```   
### Running tests

Tests are written in using mocha [https://mochajs.org/]. Tests can be launched using:

```bash
npm test
```

### Contributing

If you want to commit changes, make sure if follow these rules:
1. All code changes should go with a proper integration test;
2. Code should follow [Javascript Standard Guideline](https://standardjs.com/);
3. Commit messages should be set according to [this article](https://chris.beams.io/posts/git-commit/).

#### TODO
- Introduce Continuous Integration;
- Add elasticsearch bulk operation instead of index for multiple records

## Authors & Contributors

* [matrus2](https://github.com/matrus2)
* [cdelgadob](https://github.com/cdelgadob)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Donate

If you find this project to be useful and you would like to support the author for maintaining it, you might consider to make any donation under this link:

[Donate via Paypal](https://www.paypal.com/cgi-bin/webscr?cmd=_s-xclick&hosted_button_id=8DRSB8GWY24R8&source=url)

