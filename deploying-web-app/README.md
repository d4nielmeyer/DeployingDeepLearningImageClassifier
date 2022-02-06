## Overview 

The following lines illustrate how to do TensorFlow browser-based and server-side inference.

[Backend](backend), contains the server-side inference code written in Python and served with FastAPI.
[Frontend](frontend), comprises the browser-based inference code written in Typescript / React 

The TensorFlow artifacts can be found [here](backend/assets).

## Demo 

Here is the app in action.

![Demo](assets/demo.gif)


## Setup

### 1. Set up the environment
It is strongly recommended to create a separate environment for `tensorflowjs`. There are various libraries to set up a virtual environment. Here is the code to create a virtual environment named dl_env. This code works, assuming conda is installed.

The virtual environment must be set up with tensorflowjs installed within the virtual environment before tensorflowjs_converter can be used in the next step.

Installing tensorflowjs 
``` 
conda create -n dl_env python=3.6
conda activate dl_env
pip install tensorflowjs==2.3.0
```

### 2. Convert the model
The model created is in the format "TensorFlow Keras". But for client-side inference the “TensorFlow.js” format is required.
Converting keras model located at `backend/assets/model_tf.h5` and saving to `backend/assets/model_tfjs`:

- Model shards: The model file is large; by specifying weight_shard_size_bytes of 50,000,000 bytes the model is brokend down to get one file
- Inference-speed optimization using GraphModel conversion
- Inference-speed optimization: Quantization is a post-training technique to reduce the size of models; by using quantization to decrease the default 32-bit precision to 16-bit precision the model file size is reduced by half.

```
tensorflowjs_converter model.h5 model_tfjs \
--input_format keras \
--output_format tfjs_graph_model \
--weight_shard_size_bytes 50000000 \
--quantize_float16
```

### 3. Set up the repo

Using the following [template](https://github.com/reshamas/deploying-web-app) repository, take the converted TensorFlow.js files and copy them to this folder in the template using this directory structure. The classes.json file should also be included in this directory.

The structure of your forked repository with the model files will look similar to this after the original and converted model is placed in the directory backend/assets.

```
├── backend
│   ├── app.py
│   ├── assets
│   │   ├── classes.json
│   │   ├── model_tf
|   |   |──├──model.h5
│   │   └── model_tfjs
│   │       ├── group1-shard1of1.bin
│   │       └── model.json
```

### 4. Local Deployment

The browser app will be served with [FastAPI](https://fastapi.tiangolo.com/); React will be used for the frontend framework. 

A Docker file is provided to run the app. Running the two commands will start a webserver running locally on the machine.
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

## 5. Server Deployment

This app is deployed at [Heroku](https://www.heroku.com/home), a nice, free option for deploying the app. Once the Heroku command-line tools are installed, it can be run using the below commands.

Replace `APP_NAME` with something unique
```
APP_NAME="manning-deploy-imagenet"
heroku create $APP_NAME

heroku container:push web --app ${APP_NAME}

heroku container:release web --app ${APP_NAME}
heroku open --app $APP_NAME
heroku logs --tail --app ${APP_NAME}
```

Hereafter the link for the Heroku application: [Link](https://dlimgclassifier.herokuapp.com/)  
