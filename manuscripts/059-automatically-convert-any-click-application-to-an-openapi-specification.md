# Automatically convert any Chart CLI into an OpenAPI specification

## Introduction

**TL;DR**

The Chart python program is a command-line interface ["CLI"] app that creates a price chart of any SYMBOL.

**Version 0.1**

     Usage: chart.py [OPTIONS] SYMBOL
       This script prints a price chart
     Options:
       -m, --main [bb|cci|ichi|kc|sma|ema|wma]
                                       Main indicator
       --maininp TEXT                  Main indicator inputs
       -s, --sub [macd|roc|rsi|sr|srsi|wr]
                                       Sub window indicator
       --subinp TEXT                   Sub window indicator inputs
       -o, --out [gif|jpg|png]         Output type, default=png
       -h, --help                      Show this message and exit.

**Version 0.2**

     Usage: chart.py [OPTIONS] SYMBOL
       This script prints a price chart
     Options:
       -m, --main [bb|ema|sma]         Main indicator
       -s, --subwin [atr|cci|macd|rsi|stoch]
                                       Sub window indicator
       -o, --out [gif|jpg|png]         Output type, default=png
       -h, --help                      Show this message and exit.

## Prerequisites

* [Click](https://palletprojects.com/p/click) is a Python package for creating beautiful CLIs



## Step 1 - Basic Prototype

The design of an OpenAPI specification is rather tedious, where we need to create schemas. Our idea is to design a prototype of the specification in a Python CLI app and automatically converting it to an OpenAPI specification.

**Chart CLI**

The chart CLI accepts several optional parameters and ONE (1) argument, i.e. SYMBOL. It returns a chart as a PNG file, which contains a price chart of SYMBOL.

The optional parameters are to include indicators on the main chart or subwindow. To prevent the chart from overcrowding, we limit the number of indicators to THREE (3) each for both the main chart and subwindow.

**Basic Prototype**

At the basic level, we output one chart per SYMBOL with a default period of ONE (1) year. For example:

     $ python3 chart.py TSLA

In this example, the CLI app creates a chart, i.e. _ChartC_0.1_.png, within the same folder.

![][1]

[1]: images/059-automatically-convert-any-click-application-to-an-openapi-specification/step-1---basic-prototype.png

## Step 2 - Add Date Parameter

In this step, we want to add optional parameters to specify a user period, using either ONE (1) relative period, or TWO (2) dates for a range.

**Relative Period**

The default time period is ONE (1) year, however, we allow the user to override this default period, by selecting ONE (1) of multiple options.

In Click, we can define this as choice options, where you have a parameter be a choice of a list of values. For example:

     @click.option('--date-rel', type=click.Choice( 
                   ['M','Q','Y','MTD','QTD','YTD'], case_sensitive=False))

**Specific Dates**

We also allow the user to override the default period by specifing a user date length, i.e. both start and end dates.

In Click, we can define this as a multi value option, where you have a fixed number of parameters, in our case (TWO) date strings.

     @click.option('--date-len', type=str, nargs=2)

## Step 3a - Add Main Indicator

This step is probably the most complex design for our app, we need to add optional parameters to specify ONE or more main indicators, where EACH indicator has ONE or more inputs.

One of the properties of a main indicator is that it shares the same Y-axis (price unit) as SYMBOL. Unlike the subwindow indicator, which has a different Y-axis, and may have its own levels.

**Basic Indicator**

Let's start with a basic indicator that has only ONE (1) input:

* EMA(period)
* SMA(period)

**Indicator with Two Inputs**

These are some indicators that has TWO (2) inputs:

* BB(period, deviation)

**Indicator with Three Inputs**

These are some indicators that has THREE (3) inputs:

* None

## Step 3b - Add Subwindow Indicator

A subwindow indicator has a different Y-axis than SYMBOL, hence it has to be displayed separately. Additionally, it may have ONE (1) or more horizontal lines (levels).

As the screen estate space for a chart is finite, we specify a HARD limit of THREE (3) to the number of subwindows. This means that any chart may include up to a maximum of THREE (3) subwindow indicators.

However, there is NO maximum limit on the number of main indicators.

**Basic Indicator**

* ATR(period) with NO level
* CCI(period) with Level(upper, lower)
* RSI(period) with Level(upper, lower)

**Indicator with Two Inputs**

* None

**Indicator with Three Inputs**

* MACD(slow, fast, signal) with Level(neutral)
* Stochastic(kperiod, dperiod, slowing) with Level(upper, lower)

## Step 4 - Understand OpenAPI Parameters

Before we can convert our CLI app into an OpenAPI spec, we need to know what are the available parameter types in OpenAPI. Unfortunately, there are fewer paremeter types in OpenAPI than in CLI.

**Types of OpenAPI Parameters**

There are FOUR (4) types of parameters:

* Path parameter
* Query string parameter
* Header parameter
* Body request parameter

Header parameters usually includes authorization parameters that are common across all endpoints. As a result, the header parameters aren't usually documented with each endpoint. Instead, the authorization details in header parameters are documented in the authorization requirements section.

Body request are usually only used with CREATE or PUT methods, and often includes a JSON object in the body of the request. Hence, we will not be using this type of parameter.

**Viable Types of Parameters**

As a result, there remains TWO (2) viable types of parameters for each endpoint:

* Path parameter
* Query string parameter

Regardless of the parameter type, define the following with each parameter:

* Data type: string, integer, boolean, key-value object, array

You may optional specify default values and restrict values for each data type:

* Default: values for each type
* Restrictions: e.g., max and min values for integer, enums for strings, length for strings

The standard way to pass a list of values (array) is to repeat them:

     /service/myresource/product?id=1&id=2

## Step 5 - Add Path Parameter

Path parameters are part of the endpoint itself and are not optional. For example, in the following endpoint, {user} and {bicycleId} are required path parameters:

     /service/myresource/user/{user}/bicycles/{bicycleId}

In our CLI app, the parameter SYMBOL is a required. Hence, we can use the path parameter to model our CLI app.

     /fastchart/0.1/{symbol}

Also, we can model all subcommands as a path parameter, under the {symbol} parameter

     /fastchart/0.1/{symbol}/subcommand/{id}

## Step 6a - Add Query Parameter

Query string parameters appear after a question mark (?) in the endpoint. In the query string, each parameter is listed one right after the other with an ampersand (&) separating them. The order of the query string parameters does not matter.

In our CLI app, we can use the query parameter to model our optional MAIN parameter.

     /fastchart/0.1/{symbol}?main=bb&main=sma&maininp=14&maininp=2.0&maininp=200

Also, for our optional SUBWINDOW parameter.

     /fastchart/0.1/{symbol}?sub=macd&sub=atr&subinp=21&subinp=13&subinp=9&subinp=14&sublvl=0

Warning: The main inputs, sub inputs and sub levels are either FULLY optional or required. For example, if you need to customize any sub indicator levels, you will need to specify levels for ALL sub indicators. Conversely, if you don't need to customize any main indicator, you can use the default values for ALL main indicators.

However, the main inputs and sub inputs, sub levels are mutually exclusive. This means that if we customize any sub indicator inputs, we can still use default values for ALL sub indicator levels

In this first example, we use the default values for ALL main indicators inputs.

     /fastchart/0.1/{symbol}?main=bb&main=sma

In the second example, we use the default values of ALL sub indicators inputs, but specify the values for ALL sub indicator levels. Note that **atr** has no sub level.

     /fastchart/0.1/{symbol}?sub=macd&sub=atr&sublvl=0

In the last example, we use the default values of ALL subindicator inputs, but specify the values for all main indicator inputs.

     /fastchart/0.1/{symbol}?main=bb&main=sma&maininp=14&maininp=2.0&maininp=200&sub=macd&sub=atr

However, in our case, the order of the query string parameters DOES matter.

## Step 6b - Add Splitable Query Parameter

After searching on Google and looking at the results for "query string parameter for search filters", it appears that there is no convention for complex search filters. Instead, it is left to the developer to implement a convention that is both:

* readable
* parsable

In our CLI app example above, we can use the query parameter to model our optional MAIN parameter, but we replace **maininp **with hyphen (-).

     /fastchart/0.2/{symbol}?main=bb-14-2.0&main=sma-200

Also, we perform the same for our SUBWINDOW parameter.

     /fastchart/0.2/{symbol}?sub=macd-21-13-9&sub=atr-14

**Readable**

The above examples have improved in readability significantly as we can clearly read what the inputs are for each indicator, and also how many indicators there are in total. 

Also, an additional benefit of splitable query parameter over the traditional query parameter is that we do not have to specify the values of ALL indicator inputs, but mix default values with customized values.

For example, we specify MACD subwindow indicator with customized inputs, but use default input values for ATR indicator.

     /fastchart/0.2/{symbol}?sub=macd-21-13-9&sub=atr

**Parsable**

Using split function, we can easily parse a subwindow indicator using hyphen (-). For example, macd-21-13-9 has FOUR elements after the split, where the first element is the name of the indicator, followed by its number of inputs.

*Warning*: The separator should not be used in any key names or values within the query string.



## Step 7a - Describe Path Parameter

**Version 0.2**

We now describe the path parameter of version 0.2 in OpenAPI 3.0. This is the minimal specification for a path parameter.

     /fastchart/0.2/{symbol}

We describe the parameters on the path level, same as **get** operation, as this is shared by all operations.

     paths:
       /fastchart/0.2/{symbol}:
         parameters:
           - in: path
             name: symbol
             schema:
               type: string
             required: true
             description: symbol of price chart to get
         get:
           summary: Fastchart is an alternative to TradingView

## Step 7b - Describe Query Parameter

**Version 0.2**

Next, we describe the query parameter of version 0.2 in OpenAPI 3.0.

     /fastchart/0.2/{symbol}?main=bb-14-2.0&main=sma-200

We describe the parameters under the **get** operation, as these are specific to this operation.

         get:
           summary: Fastchart is an alternative to TradingView
           parameters:
             - in: query
               name: main
               schema:
                 type: string
               required: false
               description: add a main indicator (remove hyphenated values to use default inputs)

     /fastchart/0.2/{symbol}?sub=macd-21-13-9&sub=atr-14

             - in: query
               name: sub
               schema: 
                 type: string
               required: false
               description: add a subwindow indicator (remove hyphenated values to use default inputs)

## Step 7c - Describe Response Media Type

Finally, we describe the response of version 0.2 in OpenAPI 3.0.

We describe the response under the **get** operation. Each response start with a status code. You can mask a status code using 'XX', e.g. '4XX'.

At a minimum, you will need to cover successful responses and all known errors, i.e. 400 Bad request, 404 Not found. You can use **default** response to cover all unexpected errors.

         get:
           responses:
             '200':
               description: A PNG file that contains the price chart of symbol.
             '400':
               description: Bad request
             '404':
               description: Not found
             default:
               description: Unexpected error

Each response must have media or **content** type, which is described under the **response** level. By far the most common type is **application/json**. 

However, for our app, we return the type **image/png** instead. The **schema** is described under **type** level.

             '200'
               description: A PNG file that contains the price chart of symbol.
               content:
                 image/png
                   schema:
                     type: string
                     format: binary

## Step 7d - Describe Reusable Components

If multiple operations return the same response, you can define it in the responses section of the global **components** level, and then reference that definition via `$ref` at the operation level.

The definitions in** components** have NO direct effect on the API unless you explicitly reference them from somewhere outside the **components**.

The following example lists ALL available subsections. The order of subsections is not relevant.

     components:
       callbacks:
       examples:
       headers:
       links:
       parameters:
       requestBodies:
       responses:
       schemas:
       securitySchemas:

We define some **components** under **responses** and **schemas**.

     components:
       responses:
         resBadRequest:
           description: The specified resource was a bad request
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/schError'
         resNotFound:
           description: The specified resource was not found
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/schError'
         resUnexpected:
           description: There was an unexpected error
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/schError'
       schemas:
         # Schema for error response body
         schError:
           type: object
           properties:
             code:
               type: string
             message:
               type: string
           required:
             - code
             - message

 We can now reference our definitions above at the operation level.

         get:
           responses:
             '400':
               $ref: '#/components/responses/resBadRequest'
             '404':
               $ref: '#/components/responses/resNotFound'
             default:
               $ref: '#/components/responses/resUnexpected'

## Step 8 - Full Specification

The full OpenAPI 3.0 specification for our CLI app is shown below.

**Version 0.2**

     openapi: 3.0.0
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     #                                  F R O N T   M A T T E R                                 |
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|      
     info:
       version: 0.2.0
       title: FastChart API
       description: stock chart as a service
       contact:
         name: dennislwm
         url: [https://github.com/dennislwm](https://github.com/dennislwm)
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     #                                      B A S E   U R L                                     |
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|      
     servers:
       - url: [https://fastchart.myvnc.com/0.2](https://fastchart.myvnc.com/v1)
         description: Dev Server
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     #                                         P A T H S                                        |
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|      
     paths:
     /{symbol}:
         parameters:
           - in: path
             name: symbol
             schema:
               type: string
             required: true
             description: symbol of price chart to get
         get:
           summary: Fastchart is an alternative to TradingView
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     #                               Q U E R Y   P A R A M E T E R                              |
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
           parameters:
             - in: query
               name: main
               schema:
                 type: string
               required: false
               description: add a main indicator (remove hyphenated values to use default inputs)
             - in: query
               name: sub
               schema: 
                 type: string
               required: false
               description: add a subwindow indicator (remove hyphenated values to use default inputs)
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     #                                     R E S P O N S E S                                    |
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
           responses:
             '200':
               description: A PNG file that contains the price chart of symbol.
               content:
                 image/png:
                   schema:
                     type: string
                     format: binary
             '400':
               $ref: '#/components/responses/resBadRequest400'
             '404':
               $ref: '#/components/responses/resNotFound404'
             default:
               $ref: '#/components/responses/resUnexpected'
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     #                                    C O M P O N E N T S                                   |
     #----------+---------+---------+---------+---------+---------+---------+---------+---------|
     components:
       responses:
         resBadRequest400:
           description: The specified resource was a bad request
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/schError'
         resNotFound404:
           description: The specified resource was not found
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/schError'
         resUnexpected:
           description: There was an unexpected error
           content:
             application/json:
               schema:
                 $ref: '#/components/schemas/schError'
       schemas:
         # Schema for error response body
         schError:
           type: object
           properties:
             code:
               type: string
             message:
               type: string
           required:
             - code
             - message

**OpenAPI Tool**

[SwaggerHub](https://app.swaggerhub.com/) is a tool for OpenAPI 3.0 specificaiton. It performs linting and auto-generates SDKs for various programming languages.

First, create a public project in SwaggerHub, and then create a new version. In our case, we create a version 0.2.0.

Copy and paste the above code to your SwaggerHub app. Save the project and ensure there are no errors or warnings.

**Export API**

Unfortunately, SwaggerHub doesn't have auto-generation of FastAPI for Python (it offers Flask for Python instead). 

Hence, we will download the API as a YAML file instead. Click on `Export -> Download API -> YAML Resolved`.

![][2]

[2]: images/059-automatically-convert-any-click-application-to-an-openapi-specification/step-8---full-specification.png