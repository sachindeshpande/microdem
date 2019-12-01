1) Attempt to run the "recommendation service" docker container independently

    a) error with GOOGLE_APPLICATION_CREDENTIALS for google libraries using GCP StackDriver.

    Solution 

    Downloading keys from my GCP account and set the enviroment var to point to the json file as below

    import os
    os.environ["GOOGLE_APPLICATION_CREDENTIALS"] = "path_to_your_.json_credential_file"

    b) Issue with PRODUCT_CATALOG  var not set

    This is the service endpoint for the product catalog service that is used by the recommendation service.
    As this is a K8 demo , all the service endpoints are set in the yaml file. However in this case as we are running the 
    "recommendation service" docker container independently, it is not set

    Initially able to run the Product Catalog docker container.
    However not able to call the REST endpoint for the product catalog server. Got nothing.

    ** Then realized I needed to publish th eexposed port using following flag. The port does not get published automatically **

        docker run -p 3550:3550

    Now it seemed like I was getting something but the response did not make sense.

    Then tried using POSTMAN. Used following GET requests

    http://localhost:3550/hipstershop.ProductCatalogService/ListProducts
    http://localhost:3550/ProductCatalogService/ListProducts
    http://localhost:3550
    http://localhost:3550/productcatalogservice/server

    However no response in POSTMAN.

    Then I ssh to the docker container to see if the process was running.
    It was running and also was exposing port 3550 correctly.

    Then I tried to see if I can curl the REST endpoint from within the container.
    That also did not return anything (It connected , but had an empty response)

    Next I wanted to find out what the contents are in the server foler. However linux indicated "server" was a file and not a folder
    Well actually server was a file. It was a go executable file

    Realized 2 possible big problems in my understanding

        i) The protocol is grpc and not REST. Not sure if curl can recognize that. I ws assuming its REST 
        ii) Secondly the return from the call may be a binary object (Which should be possible with grpc)

