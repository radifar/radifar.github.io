---
title: Prototyping NoSQL-backed API with FastAPI and Mongita
description: ""
date: 2023-05-10T12:54:57.732Z
preview: ""
draft: true
tags: []
categories: []
slug: prototyping-nosql-backed-api-fastapi-mongita
---

Sometimes we have some web apps idea that we would like to immediately try. Something so simple that using Docker or production-level DB could be overkill. And it would be great if the DB is tiny that adding small Python library would be enough.

After some research, I found few Python-based DB like TinyDB, MontyDB, and Mongita. And after some consideration I pick Mongita as I thought it is the simplest, MongoDB like, and perhaps the fastest one to start with.

I wrote this post and testing FastAPI & Mongita at the same time. This post is meant to be note-taking (for me) and not for educational purpose. So take this post with a grain of salt.

## Simple CRUD App API

My first step is to figure out what to build, and to do so I did some quick search on Fastapi and MongoDB tutorial and I found a nice tutorial on [TestDriven.io](https://testdriven.io/blog/fastapi-mongo/). So what I am trying to do here is to replicate that tutorial but I will be using Mongita instead of MongoDB.

## Set up environment

First is creating the directory and the environment:

```bash
mkdir fastapi-mongita
cd fastapi-mongita

python -m venv venv
source venv/bin/activate
```

Next is installing fastapi, uvicorn, and mongita. Before I continue I should warn in case you tried to follow these steps. I got an error message so you should check how I deal with it below before you follow:

```bash
python -m pip install fastapi uvicorn mongita
```

And I got the following error message:

```md
  error: invalid command bdist_wheel'                                                                       
  ----------------------------------------                                                                   
  ERROR: Failed building wheel for mongita
```

As it turns out I need to install wheel first using pip before I install Mongita.

Next is to setup the files and directories:

```
├── app
│   ├── __init__.py
│   ├── main.py
│   └── server
│       ├── app.py
│       ├── database.py
│       ├── models
│       └── routes
└── requirements.txt
```

Next I populate the requirements.txt using `pip freeze` command.

## First try

Now is to create the simplest API by creating entry point through `main.py` and use it to access the FastAPI instance in `app.py`

`main.py`

```python
import uvicorn

if __name__ == "__main__":
    uvicorn.run("server.app:app", host="0.0.0.0", port=8000, reload=True)
```

`app.py`

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/", tags=["Root"])
async def read_root():
    return {"message": "Welcome to this fantastic app!"}
```

Then run the server using the command `python app/main.py` and you will see something similar to this:

```
(venv) radifar->fastapi-mongita$ python app/main.py
INFO:     Will watch for changes in these directories: ['/home/radifar/project/fastapi-playground/fastapi-mongita']
INFO:     Uvicorn running on http://0.0.0.0:8000 (Press CTRL+C to quit)
INFO:     Started reloader process [79057] using WatchFiles
INFO:     Started server process [79059]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```

And as usual, check `localhost:8000` and `localhost:8000/docs` for greater satisfaction.
