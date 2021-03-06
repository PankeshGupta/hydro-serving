## What's ML Lambda?

##### Story
Deploying skit-learn models for serving online user requests is pretty simple: just spin up an HTTP server, load the model and call `predict()` method. Performance wise it will satisfy most of the use cases.
TensorFlow serving is a bit more complicated to manage but feasible as well.
For Spark ML you would need to use [spark-ml-serving](https://github.com/Hydrospheredata/spark-ml-serving) library and call it from HTTP action.
JVM friendly H2O and DL4J would go the same way.

##### Challenge
In real projects you have to deploy pipelines of different models and custom functions rather that a single model.
You could not lock data scientist to use just 1 machine learning framework and build 1 HTTP server for that.
Data scientist needs to build->test->deploy to production quickly and continuously experimenting different models and ensembles.
Production data and predictions quality should be carefully monitored and the system should be adjusted in real time.

##### Example

To build a natural language processing pipeline that takes input JSON from online web application, extracts simple features, classifies a document into particular category and then applies a neural network pre-trained for detected category you’ll need something like this:

![Image](/docs/images/NLP-serving-pipeline.png)

Then you've decided to try embedding word2vec into DL4J neural network as a first two layers. Your production experiment will look this way:

![Image](/docs/images/serving-experiment.png) 

Then you've decided to execute the same serving pipline in a streaming context, i.e. deploy a prediction pipeline into Kafka. 

![Image](/docs/images/serving-pipelines-in-kafka.png)

How would you do that as a data scientist? There is an option to ask engineers to re-implement it in Java/Spark that takes another 6 months and unpredictable outcome. There is another solution described below.

##### Solution
Hydro Serving manages multiple serverless runtimes and allows chaining them into end-to-end serving pipelines. Monitoring, versioning, auto-scaling, models auto discovery and user friendly UI goes on top.
It implements side-car architecture that decouples machine learning logic from networking, service discovery, load balancing. 
So, the transport layer between model runtimes could be changed from HTTP to unix sockets and even to Kafka without changing containers with actual machine learning code.

![Image](/docs/images/Diagrams.png?raw=true)

Additional out of the box features include Rate limiting, Load balancing, Circuit breaking, Tracing, Statistics.
