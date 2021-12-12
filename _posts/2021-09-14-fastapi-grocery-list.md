---
layout: post
title: Fast API — How to build a grocery list
description: Fast API — How to build a grocery list
summary: Tutorial on fast api
tags: typography
minute: 5
---

![Wallpaper]({{ site.url }}{{ site.baseurl }}/assets/images/fastapi-grocery-list/wallpaper.jpeg)

> This article is also available on [medium](https://medium.com/artificialis/fast-api-how-to-build-a-grocery-list-ee51d65f5bf2).

I recently approached Fast API and I realized that is an extremely useful, powerful, and easy-to-use web framework. Compared to Flask, it is much faster because it is built over Asynchronous Server Gateway Interface (ASGI) instead of Web Server Gateway Interface (WSGI). You can read something more about it in the following medium article: “[Difference between WSGI and ASGI?](https://medium.com/analytics-vidhya/difference-between-wsgi-and-asgi-807158ed1d4c)”.

Today I explain a simple way for implementing a grocery list with FastAPI. Obviously, once you understand the basics of this framework, “the sky’s the limit!”

First, we should start mentioning the CRUD operations, that are:
* CREATE: create a new item
* READ: retrieve all our items
* UPDATE: update the quantity of a given item that we need to buy
* DELETE: delete a given item

<br />
> The code of this tutorial can be found in [this](https://github.com/francescodisalvo05/fastapi-grocery-list) repository.

<br /><br />


## Structure 
This is the structure that I have used in my repository:
↳ app : __init__.py | app.py
↳ test.py : __init__.py | test_main.py
main.py

<br /><br />

## GET, POST, PUT, DELETE
The foundations of the requests that took place on the web lie in Hypertext Transfer Protocol (HTTP). It manages all the communications between **clients** (the ones that request a resource) and **servers** (the ones that own the resources).

Two of the most common **HTTP** methods are **GET** and **POST**. The major difference among them is that **GET** is used for requesting data to the server whereas POST is used for sending data to the server.

If you want to know something more, I suggest you have a look at [W3Schools](https://www.w3schools.com/tags/ref_httpmethods.asp). This website has been a bible for me while I was in high school and I was learning the Web Development stack.

Moreover, we also have **PUT** and **DELETE**. PUT is used for updating an instance on the server whereas DELETE is used, as you may imagine, for deleting an existing item.
<br /><br />

## API and JSON
An Application Programming Interface (API) is a software that allows the communication between two applications. JavaScript Object Notation (JSON), on the other hand, is a text format based on “key-value” pairs separated by commas. Here’s an example:

```json
{
    "name" : "Francesco",
    "surname" : "Di Salvo",
    "interests" : ["AI","Data Science","Finance"]
}
```
The reason why I am mentioning them both together is that JSON is commonly used for communication among applications through APIs. The main advantage of using JSON is that it is easily parsed by the machines and interpreted by us!
<br /><br />

## Requirements
To facilitate you, I uploaded the requirements [here](https://github.com/francescodisalvo05/fastapi-grocery-list/blob/main/requirements.txt). Therefore, once you have created your own virtual environment, you can simply install them with:

```python
pip install -r requirements.txt
```
<br /><br />

## main.py
Here we define our **uvicorn** server, which will run on localhost on **port 8000**. Notice that the option “reload=True” means that we are in debugging mode, Pay attention that **it must be turned to False in production**! It means that every time we change something on our code the server will be restarted.

```python
import uvicorn


if __name__ == '__main__':

    uvicorn.run("app.app:app", host="127.0.0.1", port=8000, reload=True)
```
<br /><br />

## app.py - CREATE
Here the magic begins. First, we need to define our FastAPI application and then, we can start implementing our routes. For the sake of simplicity, we will not use any persistent memory but we will just use a list of dictionaries as our basket items.

As we mentioned before, since we want to send data to the server we need to use a POST method. Now, we need to define an input dictionary, that will be sent from the client.

From the input dictionary, we need the key-value pairs for the **item** and **qty** (quantity). Therefore, assuming the input keys are correct, we have two scenarios:

1. The item already exists: we raise a 400 error
2. The item does not exist: we add it to our list, and we send a 201 status code

> For the status code you can also check [this](https://fastapi.tiangolo.com/tutorial/response-status-code/) resource.

```python

from fastapi import FastAPI, HTTPException


app = FastAPI()

grocery_list = [
    {"item" : "bread", "qty" : 1},
    {"item" : "milk", "qty" : 2}
]

@app.post('/create', status_code=201)
async def add_item(item : dict) -> dict:
    """Creates a new item to buy
    Args:
        item (dict): {"item" : (str), "qty" : (int)}
    
    Returns:
        notification, code 201
    Raises:
        HTTPException 400, if the element is already present on the list
    """

    for temp_item in grocery_list:
        if temp_item['item'] == item['item']:
            raise HTTPException(status_code=400, detail= f"{item['item']} already present!")

    grocery_list.append(item)
    
    return {"data" : f"{item['item']} added correctly!"}
```
<br /><br />

## app.py - READ
Now, we want to define a route for reading all our inserted items. We do not need to send data on the network. Therefore, we can use a GET method. So, the associated method will simply return our list of dictionaries under the key data.

```python
@app.get('/list', status_code=200)
async def get_list() -> dict:
    """Gets the whole grocery list
    
    Returns:
        Dictionary containing the list of items under the key "data"
    """
    return {"data":grocery_list}
```
<br /><br />

## app.py — UPDATE
The third operation is UPDATE, for which we update the quantity of a given item already present on our current list. Recall from before that to update an instance we use the PUT method.

Here we have the opposite situation of CREATE. We should raise an error if the item is not present on our list (because we cannot update it) and we can proceed otherwise.

Instead of using a body, now we use two items that will be given through the URL. We are waiting for “item_name” and “item_quantity”.

```python
@app.put('/update', status_code=200)
async def update_item(item_name:str, item_quantity:int) -> dict:
    """Updates the quantity to buy for a given item
    Args:
        item_name (str) : name of the item to update
        item_quantity (int) : quantity to update
    Returns:
        notification, code 200
    Raises:
         HTTPException 404, if the item is not present on the list
    """

    for temp_item in grocery_list:
        if temp_item['item'] == item_name:
            temp_item['qty'] = item_quantity
            return {"data" : f"{item_name} correctly updated!"}

    raise HTTPException(status_code=404, detail=f"{item_name} not found!")
view rawapp.py hosted with ❤ by GitHub
```
<br /><br />

## app.py — DELETE
Finally, according to the CRUD paradigm we have to take care of the last operation, DELETE. Its corresponding method is DELETE, and we proceed as before. Now, instead of giving both “item_name” and “item_quantity” we give just “item_name”.

However, to mess up everything a little bit, let’s see a different way for retrieving the item to delete. In particular, we may also give it in a parametric way through the route.

For example, we may delete the item “water” by simply using the following request:

```
/delete/water
```

Therefore, we use the name of the parameter inside the braces, and it can be used inside the method.

```python

@app.delete('/delete/{item_name}',status_code=200)
async def delete_item(item_name:str) -> dict:
    """Deletes item with a given iten_name
    Args:
        item_name (str) : name of the item that must be removed
    
    Returns:
        notification, code 200
    Raises:
        HTTPException 404, if the item is not present on the list
    """
    for temp_item in grocery_list:
        if temp_item['item'] == item_name:
            grocery_list.remove(temp_item)
            return {"data" : f"{item_name} correctly deleted!"}

    raise HTTPException(status_code=404, detail=f"{item_name} not found!")
```
<br /><br />

## Testing
Once we defined all these operations we may want to test them. There are several ways to do that and probably the most common way is through Postman. Alternatively, FastAPI provides a very intuitive GUI from the route /docs. Otherwise, we can always use our old friend pytest.

To define methods recognized as tests, we have to call the python file with “test_fileToTest.py”, so we define our “test_main.py”. Then, each method will be called with “test_stuffToTest()”.

For example, I want to test the creation of an item. We have two possibilities: the item already exists or not. Therefore, it is a common practice to test all the possible scenarios.

```python
from fastapi.testclient import TestClient
from app.app import app


client = TestClient(app)


def test_create_inexistent_item():
    response = client.post("/create", json={"item" : "new_item", "qty" : 10})
    assert response.status_code == 201
    assert response.json() == {"data" : "new_item added correctly!"}
    

def test_create_existent_item():
    response = client.post("/create", json={"item" : "bread", "qty" : 3})
    assert response.status_code == 400
    assert response.json() == {"detail" : "bread already present!"}
```

You can check all the tests that I have already performed [here](https://github.com/francescodisalvo05/fastapi-grocery-list/blob/main/test/test_main.py). To run all the tests, you need to run:

```
pytest
```

Since I have performed 8 tests, this would be my result:

![Pytest]({{ site.url }}{{ site.baseurl }}/assets/images/fastapi-grocery-list/pytest.png)
<br /><br />

## Conclusions
Today we have seen how to build a simple local server that can be used for a lot of (more useful) use cases. Recently, I needed to build it for a coding interview, and to be honest, it was quite fun!

I hope everything is clear, and if not, feel free to reach out on [LinkedIn](https://www.linkedin.com/in/francescodisalvo-pa/). I would be more than happy to hear your feedback!
<br /><br />

## References
* Wallpaper : https://unsplash.com/photos/8RaUEd8zD-U
* Available code: https://github.com/francescodisalvo05/fastapi-grocery-list
* GET and POST: https://www.w3schools.com/tags/ref_httpmethods.asp
* APIs: https://www.redhat.com/en/topics/api/what-are-application-programming-interfaces
* Status code: https://fastapi.tiangolo.com/tutorial/response-status-code/
* Postman: https://www.postman.com