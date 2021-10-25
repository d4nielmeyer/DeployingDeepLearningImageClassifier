# DeployingDeepLearningImageClassifier

This project is about creating an application that will take a photo of a food item, then return the image name with an associated probability. This can be a helpful application to aid in food recognition. This application will run on a website on the computer and also on a phone. 

## Project Outline:

- Train a deep learning model using TensorFlow.
- Store that model on a server and locally.
- Create a web application that allows users to upload an image or URL of a food item and return the predicted image class name with some probability.
- Create a mobile application that allows users to take a photo or upload an image from their phone and returns the predicted image class name with some probability.

## Tech Stack:

- Colab: Train deep learning models using GPU
- TensorFlow/Keras: Open source libraries to perform deep learning
- TensorFlow.js: Use the TensorFlow ecosystem to deploy the deep learning model
- Expo/ReactNative: Create the web application
- Heroku/GitHub Pages: Create the web application
- Docker: Deploy the web application
- Expo: Deploy the mobile application
- Node.js/NVM: Deploy the mobile application


## Overview 

The following lines illustrate how to do TensorFlow browser based and server side inference.

[Backend](backend), contains the server side inference code written in Python and served with FastApi.
[Frontend](frontend), comprises the browser based inference code written in Typescript / React 

The TensorFlow artifacts should be served [here](backend/artifacts).
A sample artifact can be found in the repo [releases](https://github.com/reshamas/deploying-web-app/releases/tag/1.0.0-tfjs) 

## Demo 

Server Side Inference : [Heroku](https://manning-deploy-imagenet.herokuapp.com/)
Browse Based Inference: [Github Pages](https://reshamas.github.io/deploying-web-app/)


![Demo](assets/demo.gif)


## Setup


## Converting TensorFlow model

It is strongly recommended to create a separate environment for `tesnorflowjs`

Installing tensorflowjs 
``` 
pip install tensorflowjs==2.3.0
```

Converting keras model located at `artifacts/model_tf_keras.h5` and saving to `artifacts/model_tfjs`
The `99999999` indicates that the model file should be split to 100 MB partitions.

The `quantize_float16=*`  decrease the default 32-bit precision to 16-bit precision which will reduce the model file size by half 

```
tensorflowjs_converter \
--input_format=keras \
--output_format=tfjs_graph_model \
--split_weights_by_layer \
--weight_shard_size_bytes=99999999 \ 
--quantize_float16=* \
artifacts/model_tf_keras.h5 artifacts/model_tfjs

```

## Local Deployment

```
docker build -t app .
docker run -p 8000:8000 -t app 
```

If you want to develop outside docker,
python (backend)
```
conda create -n dl_env python=3.7 
pip install -r backend/requirements.txt
```

frontend
```
yarn 
```

## Server Deployment

This app is deployed at Heroku.
Here are the steps for mac

Setup 
``` 
brew tap heroku/brew && brew install heroku
heroku login
heroku container:login
```

Replace `APP_NAME` with something unique
```


APP_NAME="manning-deploy-imagenet"
heroku create $APP_NAME

heroku container:push web --app ${APP_NAME}

heroku container:release web --app ${APP_NAME}
heroku open --app $APP_NAME
heroku logs --tail --app ${APP_NAME}
```

## Customizing
In order to prevent most frontend changes, most of the text and options are configured in this [config.yml](config.yaml).

