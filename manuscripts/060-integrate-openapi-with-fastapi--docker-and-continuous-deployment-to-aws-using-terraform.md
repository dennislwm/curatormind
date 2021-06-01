- Integrate OpenAPI with FastAPI, Docker and Continuous Deployment to AWS using Terraform
- Introduction
  
  **TL;DR**
  
  The Fastchart python program is a FastAPI app that returns a price chart of any SYMBOL.
  
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
- Schema for error response body
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
- Prerequisites
  
  (1) Python 3
  
  * FastAPI - Python package for creating APIs
  * [fast-api-code-generator](https://github.com/koxudaxi/fastapi-code-generator) - Python package that creates a FastAPI app from an OpenAPI file
  
  (2) Docker
  
  (3) Terraform
- Step 1 - Setup Basic Project
  
  **Project Structure**
  
     +- fastchart/                                 <-- Root of your project
         |- .dockerignore                          <-- Docker ignore file
         |- Dockerfile                             <-- Docker file
         |- fastapi.sh                             <-- Bash alias file
         |- requirements.txt                       <-- Python requirements file
         +- api_0.1/                               <-- Auto-generated by fast-api-code-generator
            |- main.py
            |- models.py
         +- api_0.2/                               <-- Auto-generated by fast-api-code-generator
            |- main.py
            |- models.py
         +- api_open/                              <-- Exported from SwaggerHub
            |- fxgit-FastChart-0.1.0-resolved.yaml
            |- fxgit-FastChart-0.2.0-resolved.yaml
         +- app/                                   <-- Main Python files
            |- fastchart.py
  
  We use VSCode dev containers to run our FastAPI app. All files belonging to our API will be put in the *app/* folder.
  
  **Dockerfile**
  
  The base Docker image **tiangolo/uvicorn-gunicorn-fastapi** includes both Python packages **fastapi** and **uvicorn**. Hence, we don't need to install these packages in our requirements file.
  
     FROM tiangolo/uvicorn-gunicorn-fastapi:python3.7
     RUN apt-get update \
       && apt-get clean && rm -rf /tmp/* /var/tmp/*
     EXPOSE 7000
     COPY . .
     RUN pip install --no-cache-dir -r requirements.txt
     WORKDIR /app
     CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "7000"]
  
  **Python requirements**
  
  The package fastapi-code-generator is used to auto-generate the FastAPI code under subfolders *api_x.x/*. For example, *api_0.1/* subfolder was auto-generated from *api_open/fxgit-FastChart-0.1.0-resolved.yaml*.
  
  The other packages are used by our API to create chart-as-a-service.
  
     fastapi-code-generator
     numpy==1.18.5
     pandas==1.0.5
     pyfxgit==0.0.1
     requests
     ta==0.6.1
     yfinance==0.1.54
- Step 2 - Basic API Endpoint
  
  **FastAPI Auto-Generated File**
  
  Let's start with the most basic API endpoint in the auto-generated *main.py* file.
  
     from fastapi import FastAPI
     
     app = FastAPI(
         title='FastChart API',
         description='stock chart as a service',
         contact={'name': 'dennislwm', 'url': 'https://github.com/dennislwm'},
         version='0.2.0',
     )
     
     @app.get('/{symbol}', response_model=None)
     def get_symbol(
         symbol: str, main: Optional[str] = None, sub: Optional[str] = None
     ) -> None:
         """
         Fastchart is an alternative to TradingView
         """
         pass
  
  Let's go over this simple piece of code:
  
  * On line 3, an instance of the FastAPI is created, and parameters include our front matter, such as title, description, etc.
  * On line 10, the decorator **@app.get** tells FastAPI that our function corresponds to the path parameter **/{symbol}** with a GET operation.
  * On line 11, the function get_symbol() is created to hold any custom code from our API app, i.e. *fastchart.py*.
  * On line 12, the function parameters **symbol**, **main** and **sub** corresponds to the path and query parameters from our OpenAPI specs
  
  **Serve Endpoint Using Uvicorn**
  
  Run the following command in the **root** folder. The location of our *main.py* file is denoted by **api_0_2.main**, while **app** denotes the FastAPI instance within the *main.py* file.
  
     $ uvicorn api_0_2.main:app --host 0.0.0.0 --port 7000
  
  The output of uvicorn tells you exactly where you should go to [`http://0.0.0.0:7000`](http://0.0.0.0:7000)[.](http://0.0.0.0:7000) You should be able to see the output of the default path (/).
  
  While running uvicorn, simply navigate your browser to  [`http://0.0.0.0:7000/docs`](http://0.0.0.0:7000) for detailed documentation.
  
  ![][1]
  
  [1]: images/060-integrate-openapi-with-fastapi--docker-and-continuous-deployment-to-aws-using-terraform/step-2---basic-api-endpoint.png
- Step 3 - Insert Class
  
  **Insert Class**
  
  As the *main.py* file is auto-generated, it does not contain any implementation. Hence, we need to insert a class into this.
  
  The reason that we use a class is because we want to keep our implementation separate from the *main.py* file. This ensures that we insert only a minimal amount of code into the *main.py* file.
  
  Each time we upgrade our OpenAPI specs to a new version, e.g. version 0.3, we have to auto-generate the *main.py* file. Hence, it is quite tedious to re-insert our class into this. Ideally, we have a bash script that inserts our class automatically, so that we don't have to do this manually.
  
  **Insert Import Code**
  
     # generated by fastapi-codegen:
     #   filename:  fxgit-FastChart-0.2.0-resolved.yaml
     #   timestamp: 2021-01-20T03:10:35+00:00
     from __future__ import annotations
     from typing import Optional
     from fastapi import FastAPI
     # Insert import code
     from class import fastchart
  
  The file *fastchart.py* is located under a subfolder named *class*.
  
  **Insert Instance Code**
  
     app = FastAPI(
         title='FastChart API',
         description='stock chart as a service',
         contact={'name': 'dennislwm', 'url': 'https://github.com/dennislwm'},
         version='0.2.0',)
- Insert instance code
     objFastchart = fastchart.FastchartCls()
  
  The instantiation of our class **FastchartCls** should be performed once before any method is called. We pass to the constructor any initialization parameters, in our case, none.
  
  **Insert Method**
  
     @app.get('/{symbol}', response_model=None)
     def get_symbol(
         symbol: str, main: Optional[str] = None, sub: Optional[str] = None
     ) -> None:
         """    Fastchart is an alternative to TradingView    """
- Insert method
         objFastchart.getSymbol(symbol, main, sub)
  
  For each path that exists, we should have a method in our class. At the very least, each method should have the same parameters as the OpenAPI specs. 
  
  For example, for our definition the path parameter **/{symbol} **has two optional query parameters **main** and **sub**. Hence, in our class, we define a method **getSymbol** that accepts three parameters, **symbol**, **main** and **sub**.