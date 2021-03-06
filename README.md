# node-lambda

### Custom fork

This is a fork of the original project to enable support for dependencies defined using the 'linklocal' npm package.
I may consider creating a pull request to the main repo, but I'm not sure if it is just behaviour that is specific to our project.

![node-lambda](../master/node-lambda.png?raw=true)

Command line tool to locally run and deploy your node.js application to [Amazon Lambda](http://aws.amazon.com/lambda/).

[![BuildStatus](https://travis-ci.org/motdotla/node-lambda.png?branch=master)](https://travis-ci.org/motdotla/node-lambda)
[![NPM version](https://badge.fury.io/js/node-lambda.png)](http://badge.fury.io/js/node-lambda)

```
node-lambda run
```

## Installation

```
npm install -g node-lambda
```

## Example App

The [node-lambda-template](https://github.com/RebelMail/node-lambda-template) example app makes it easy to get up and running.

## Usage

There are 4 available commands.

```
node-lambda setup
node-lambda run
node-lambda package
node-lambda deploy
```

### Commands

#### setup

Initializes the `event.json`, `context.json`, `.env` files, and `deploy.env` files. `event.json` is where you mock your event. `context.json` is where you can add additional mock data to the context passed to your lambda function. `.env` is where you place your deployment configuration. `deploy.env` has the same format as `.env`, but is used for holding any environment/config variables that you need to be deployed with your code to Lambda but you don't want in version control (e.g. DB connection info).

```
$ node-lambda setup --help

  Usage: run [options]

  Options:

    -h, --help                     output usage information
```

After running setup, it's a good idea to gitignore the generated `event.json` and `.env` files.

```
echo -e ".env\ndeploy.env\nevent.json" >> .gitignore
```

#### run

Runs your Amazon Lambda index.js file locally. Passes `event.json` data to the Amazon Lambda event object.

```
$ node-lambda run --help

  Usage: run [options]

  Options:

    -h, --help                          Output usage information
    --handler [index.handler]           Lambda Handler {index.handler}
    -j, --eventFile [event.json]        Event JSON File
    -u, --runtime [nodejs4.3]           Lambda Runtime {nodejs4.3, nodejs} - "nodejs4.3" is the current standard, "nodejs" is v0.10.36 
    -x, --contextFile [context.json]    Context JSON file
```

#### package

Bundles your application into a local zip file.

```
$ node-lambda package --help

  Usage: package [options]

  Options:

    -h, --help                          output usage information
    -p, --packageDirectory [build]      Local Package Directory
    -n, --functionName [node-lambda]    Lambda FunctionName
    -e, --environment [staging]         Choose environment {development, staging, production}
    -f, --configFile []                 Path to file holding secret environment variables (e.g. "deploy.env")
    -x, --excludeGlobs []               Add a comma separated list of file(type)s to ignore (e.g. "*.json")
```

#### deploy

Bundles and deploys your application up to Amazon Lambda.

```
$ node-lambda deploy --help

  Usage: deploy [options]

  Options:

    -h, --help                        output usage information
    -e, --environment [staging]       Choose environment {development, staging, production}
    -a, --accessKey [your_key]        AWS Access Key
    -s, --secretKey [your_secret]     AWS Secret Key
    -k, --sessionToken [your_token]   AWS Session Token
    -r, --region [us-east-1]          AWS Region(s)
    -n, --functionName [node-lambda]  Lambda FunctionName
    --handler [index.handler]         Lambda Handler {index.handler}
    -o, --role [your_role]            Amazon Role ARN
    -m, --memorySize [128]            Lambda Memory Size
    -t, --timeout [3]                 Lambda Timeout
    -d, --description [missing]       Lambda Description
    -u, --runtime [nodejs4.3]         Lambda Runtime {nodejs4.3, nodejs} - "nodejs4.3" is the current standard, "nodejs" is v0.10.36 
    -p, --publish [false]             This boolean parameter can be used to request AWS Lambda to create the Lambda function and publish a version as an atomic operation
    -v, --version [custom-version]    Lambda Version
    -f, --configFile []               Path to file holding secret environment variables (e.g. "deploy.env")
    -b, --vpcSubnets []               VPC Subnet ID(s, comma separated list) for your Lambda Function, when using this, the below param is also required
    -g, --vpcSecurityGroups []        VPC Security Group ID(s, comma separated list) for your Lambda Function, when using this, the above param is also required
    -x, --excludeGlobs []             Add a comma separated list of file(type)s to ignore (e.g. "*.json")
```

## Custom Environment Variables

AWS Lambda doesn't let you set environment variables for your function, but in many cases you will need to configure your function with secure values that you don't want to check into version control, for example a DB connection string or encryption key. Use the sample `deploy.env` file in combination with the `--configFile` flag to set values which will be prepended to your compiled Lambda function as `process.env` environment variables before it gets uploaded to S3. 

## Node.js Runtime Configuration

AWS Lambda now supports Node.js v4.3.2, and there have been some [API changes](http://docs.aws.amazon.com/lambda/latest/dg/nodejs-prog-model-using-old-runtime.html) for the new version.  Most notably, 
`context.done()`, `context.succeed()`, and `context.fail()` are deprecated in favor of the Node convention of passing in
a callback function.  These will still work for now for backward compatibility, but are no longer recommended.

v0.10.36 is still supported, and can be targeted by changing the `AWS_RUNTIME` value to `nodejs` in the `.env` file.

## Post install script
When running `node-lambda deploy` if you need to do some action after `npm install --production` and before deploying to AWS Lambda (i.e. replace some modules with precompiled ones or download some libraries) you can create `post_install.sh` script. If the file exists the script will be executed (and output shown after execution) if not it is skipped. Make sure that the script is executable.

## Other AWS Lambda Tools Projects

+ [lambdaws](https://github.com/mentum/lambdaws)
+ [lambdaws-instant-api](https://github.com/mentum/lambdaws-instant-api)

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Added some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request

## Running tests

```
npm install
npm test
```
