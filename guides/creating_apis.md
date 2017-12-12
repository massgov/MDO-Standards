# Creating APIs

This document will serve as a central how-to for creating APIs for the Data Office (or Digital Services as a whole). This guide is meant to be an opinionated guide on creating APIs in AWS, it is not meant to be an intro to APIs or a guide on any particular API flavor, although we will follow relevant standards in place in digital services, like [JSON API](http://jsonapi.org/) where appropriate. 

This guide currently leverages only Python for code examples, though examples from other languages currently being used by Digital Services are welcome as pull requests!
This guide also will assume the reader is on a mac with [`brew` installed](brew.sh) examples for systems such as linux are welcome as PRs! 

## Contents

[Quickstart Guide](#quickstart-guide)

[Serverless](#serverless)

[Creating Derivatives for AWS Lambda](#creating-derivatives-for-aws-lambda)

[Server-based with Docker and ELB](#server-based-with-docker-and-elb)



## Quickstart Guide
This guide will show you how to create a simple 1 lambda api which uses `sklearn` to produce a 2 class sample and serve it from an API stood up via the API Gateway.

### Prereqs
Install the following or ensure these are installed on your local system:
Python 2.7

[`pip`](https://pip.pypa.io/en/stable/installing/)

Then run the following:

```bash
# python modules
pip install aws-cli
pip install flask
pip install boto3

# install apex
curl https://raw.githubusercontent.com/apex/apex/master/install.sh | sudo sh
```

Then run:
```
aws configure
```

Note: you will need a valid aws profile with your client and secret tokens handy.

Now let's create a new api `sample_producer` which will use `sklearn` to produce a sample of data.

```bash
mkdir ml_api
cd ml_api
apex init
```

1. Supply the name `sample_producer` for `Project name`

2. (Optional) Enter a short description of the api

```bash
cd functions/
rm -r hello
mkdir sample_producer  # note you want to make meaningful function names in the case of many lambdas in one project
```

Get the module derivatives we will need to run our lambda. See more on this below

```bash
cd sample_producer
aws s3 cp s3://mdo-artifacts/lambda/python_2.7/sklearn_numpy_scipy.zip ./
unzip sklearn_numpy_scipy.zip
rm sklearn_numpy_scipy.zip
rm -r __MACOSX  # not sure why this is here
```

Now create a `function.json` configuration file describing the function for apex:

```json
{
    "description": "function which produces a sample from sklearn",
    "runtime": "python2.7",
    "memory": 128,
    "timeout": 30,
    "handler": "sample.handle"    
}
```

now create `sample.py`:
```python
from sklearn.datasets import make_classification
import logging
import json
import os

# logs automatically go to cloudwatch
logger = logging.getLogger()
logger.setLevel(logging.INFO)

# you can use env vars like so
N_CLASSES = os.environ.get('N_CLASSES')


def create_payload(n_classes=N_CLASSES):
    '''
    create json payload of sample feature vectors
    '''
    logger.info('drawing sample')
    X, y = make_classification(n_features=2, n_redundant=0, n_informative=2,
            random_state=0, n_clusters_per_class=1, n_classes=int(n_classes))
    logger.info('creating blank payload')
    payload = {}
    for index, column in enumerate(X.T):
        payload['vec_{}'.format(index)] = list(column)
    payload['target'] = list(y)
    logger.info('created payload {}'.format(json.dumps(payload, indent=2)))
    return payload


def handle(event, context):
    '''
    main function for sample producer lambda
    '''
    event_string = json.dumps(event, indent=2)
    logger.info('received event {}'.format(event_string))
    payload = create_payload()
    return json.dumps(payload)
```
finally `cd` back up to the root project dir and run `apex deploy`.

Log in to the AWS console go to lambda and find the function named `ml_api_sample_producer` and create a random test, the body does no matter. Test the lambda and notice the output. 

Now we need to place the api gateway in front of this lambda so we can expose it over http and put proper controls in place.

1. Log in to the AWS console and navigate to the lambda dashboard and select Functions

![](img/lambda_funcs.png)

2. Select your function and under "Add Triggers" click API Gateway

![](img/add_triggers.png)

3. Click the "Configuration Required" link and fill out the form. Of interest is the stage and security. Stage should be one of prod, stage, dev for official api stages or some personal name for testing.

![](img/configure_apig.png)
