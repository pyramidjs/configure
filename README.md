# Configure

[![Master Build][travis-master]][travis-url]

Smart configuration control

[travis-master]: https://img.shields.io/travis/pyramidjs/configure/master.svg?label=master
[travis-url]: https://travis-ci.org/pyramidjs/configure

## Install

```bash
npm i @pyramid/configure
```

## Basic Usage

This package by default read a the *config* folder in your working directory:

- config
    - app.json
    - database.json
    - language.json

```javascript
const config = require('@pyramid/configure');
console.log(config.app.name)
console.log(config.database.mysql.host)
console.log(config.language.supported[0])
```

You can override the config file path with an environment variable:

```javascript
// read a folder
process.env.CONFIG_FILE=src/config
// read a file
process.env.CONFIG_FILE=src/config.json
// with absolute path
process.env.CONFIG_FILE=/path/to/src/config.json
```

If you specified a single file, the config object will be the file itself rather than an nested object with the filename as its key(like folder reading does).

*src/config.json*
```json
{
    "app": {
        "name": "myapp"
    }
}
```

```javascript
process.env.CONFIG_FILE=src/config.json
const config = require('@pyramid/configure');
console.log(config.app.name)
```

It won't load again once loaded. To reload configs, you may call:

```javascript
// delete cached config
config.__.desolve()
// reload
config = require('@pyramid/configure');
```

## Env File

By default, This package read a *.env* file in your working directory and set environment variables from this file.

```plain
NODE_ENV=production
CONFIG_FILE=src/config.json
```

You may override this behaviour by passing the following environment variable:

```javascript
process.env.ENV_FILE=src/.env
const config = require('@pyramid/configure');
```

**Existing environment variables won't be overrided.**

```bash
NODE_ENV=development npm start
```

```plain
# NODE_ENV won't be set because it already exists
NODE_ENV=production
CONFIG_FILE=src/config.json
```

## Smart Config File

A bunch of **keywords** can be used in config files

```json
{
    "name": {
        "$env": "APP_NAME"
    },
    "database": {
        "port": {
            "$env": ["DB_PORT", 3306]
        },
        "host": {
            "$env": ["DB_HOST", "localhost"]
        },
        "username": {
            "$env": "DB_USER"
        },
        "password": {
            "$env": ["DB_PASS", {"$env": "DB_USER"}]
        }
    }
}
```

The `$env` object reads environment varaibles with an optional fallback value.  With the following env set:

```plain
APP_NAME=myapp
DB_HOST=10.10.10.100
DB_USER=admin
```

The loaded config will be

```json
{
    "name": "myapp",
    "database": {
        "port": 3306,
        "host": "10.10.10.100",
        "username": "admin",
        "password": "admin"
    }
}
```

Here is a list of available **keywords**:

**$env**

Get value from environment variables with an optional fallback value

- params: `string`|`[string, any]`
- returns `any`

**$path**

Resolve the path to absolute from current working directory

- params: `string`
- returns `string`

**$file**

Resolve the path and read the file with an optional encoding

- params: `string`|`[string, string]`
- returns `string`|`Buffer`

**$json**

Parse the given string into object

- params: `string`
- returns `any`

**$number**

Parse the given string into a number

- params: `string`
- returns `number`

**$concat**

Concat strings or arrays

- params: `string[]`|`[][]`
- returns `string`|`any[]`

**$max**

Return the max number in the array

- params: `number[]`
- returns `number`

**$min**

Return the min number in the array

- params: `number[]`
- returns `number`

**$sum**

Sum the numbers in the array

- params: `number[]`
- returns `number`

**$avg**

Return the average value of the array

- params: `number[]`
- returns `number`

**$first**

Return the first element in the array

- params: `any[]`
- returns `any`

**$last**

Return the last element in the array

- params: `any[]`
- returns `any`

**$at**

Return element in the array at the given index

- params: `[any[], number]`
- returns `any`

**$asce**

Sort the numbers in ascending order

- params: `number[]`
- returns `number[]`

**$asce**

Sort the numbers in descending order

- params: `number[]`
- returns `number[]`

**$rand**

Return an element at random index

- params: `any[]`
- returns `any`

**$rands**

Return the given amount of elements at random indices

- params: `[any[], number]`
- returns `any[]`

**$reverse**

Reverse an array

- params: `any[]`
- returns `any[]`

**$slice**

Slice an array from the given index to an optional end index

- params: `[any[], number]`|`[any[], number, number]`
- returns `any[]`

**$count**

Return the length of an array

- params: `any[]`
- returns `number`

**$merge**

Return the merge of two objects

- params: `[any, any]`
- returns `any`

**$keys**

Return the keys of an object

- params: `any`
- returns `string[]`

**$vals**

Return the values of an object

- params: `any`
- returns `any[]`

**$zip**

Merge a series of key-value pairs into an object

- params: `[any, any][]`
- returns `any`

**$zap**

Split an object into key-value pairs

- params: `any`
- returns `[any, any][]`

**$cond**

Return the second or third element based on the boolean value of the first element

- params: `[boolean, any, any]`
- returns `any`

**$and**

Return true only if both two elements' boolean values are true

- params: `[boolean, boolean]`
- returns `boolean`

**$or**

Return true if any of the two elements' boolean value is true

- params: `[boolean, boolean]`
- returns `boolean`

**$not**

Return true only if the given value is false

- params: `boolean`
- returns `boolean`

### Other Keywords

**Operators** 

$abs, $add, $sub, $mul, $div, $mod, $ceil, $floor, $round, $trunc, $sign

**Comparers**

$gt, $gte, $lt, $lte, $eq, $ne, $in, $ni

**String Morph**

$upper, $lower, $snake, $pascal, $camel, $dash, $plural, $singular

## Test

```bash
npm test
```

