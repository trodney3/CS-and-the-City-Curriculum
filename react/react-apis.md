# APIs in React

## Learning Objectives

- SWBAT read developer documentation to construct an API call
- SWBAT read developer documentation to interpret an API response
- SWBAT request data from NYC Open Data
- SWBAT integrate API data into a React component

## Sequence

1. [Launch](#launch)
2. [APIs Overview](#apis-overview)
3. [Viewing Data](#viewing-data)
4. [APIs + JavaScript](#apis--javascript)
5. [NYC Open Data APIs](#nyc-open-data-apis)
6. [Close](#close)

## Launch

Click on this url: [http://jservice.io/api/random?count=1](http://jservice.io/api/random?count=1)

- What do you see when you click on that URL?
- Does the result look familiar? What's the name of the format you see?
- Look closer: can you find the trivia question?
- Look closer: can you find an answer to the trivia question?

> Using a browser plugin like [JSON Formatter (Chrome)](https://chrome.google.com/webstore/detail/json-formatter/bcjindcccaagfpapjjmafapmmgkkhgoa) will reformat that site to make it easier to read.

The [jservice.io](http://jservice.io/) site is a place where you can go to access over 156,800 trivia questions. We've reformatted one of the trivia questions below so you can more easily inspect the data you get from this API.

```javascript
[{
	"id": 117776,
	"answer": "the Globe",
	"question": "This London theatre was razed in 1644, 2 years after the Puritans closed it down",
	"value": 800,
	"airdate": "2013-04-05T12:00:00.000Z",
	"created_at": "2014-02-14T02:47:13.857Z",
	"updated_at": "2014-02-14T02:47:13.857Z",
	"category_id": 306,
	"game_id": null,
	"invalid_count": null,
	"category": {
		"id": 306,
		"title": "potpourriiii",
		"created_at": "2014-02-11T22:48:12.939Z",
		"updated_at": "2014-12-08T12:51:44.677Z",
		"clues_count": 320
	}
}]
```

- What if you wanted 20 questions instead of just 1? How would you change the URL to get more results?

## APIs Overview

An API, or an Application Programming Interface, is a way of reading data from, or writing data to, a database that you don't necessarily own. Although some developers do set up and use their own APIs, here we'll focus on using other people's APIs to get their data.

APIs are great because we can hook into a service - that is maintained by someone else - to ensure that our app is always showing current data. For instance, let's say you want to build a weather app. Would you rather maintain a database of the weather in every location in the world? Or would you rather tap into a meterological database where they're already doing that?

Using an API involves two steps:

1. The Request: a request is where you're asking for someone else's data, and you typically need to specify what data you want according to the rules that the database owner sets up. Those rules are documented in developer documentation.
2. The Response: if the database accepts your request, then it sends back a response. The response is the data you've asked for, and the structure of the response data is typically also documented in developer documentation.

Have you ever noticed how the google URL changes when you google something? The URL is the request, and the data on the search results page is the response.

Can you guess what data this URL will show?: [https://www.google.com/search?q=red+lobster](https://www.google.com/search?q=red+lobster)

Now click on the URL. What happens if you change part of the URL from `red+lobster` to `blue+lobster`?

## Viewing Data

Most API requests are built from three parts:

1. An Endpoint: an endpoint is the stem of the request URL. For the example above, the endpoint is `https://www.google.com/search`.
	> For the first example we saw, the endpoint is `http://jservice.io/api/random`.
2. Parameters: parameters are the variables that are passed to the database as part of the request and follow the `?`. For the example above, there is one parameter, `q`.
	> For the first example we saw, the parameter was `count`.
3. Values: values are paired with variables and are the data that is passed to the database in the request. For the example above, the value `red+lobster` is paired with the parameter `q`.
	> For the first example we saw, the value was `1`.

Not all API requests will return the data for a webpage like Google search results; some APIs like jservice.io just return raw data.
	
- For more on working with JSON data, see the [JSON & Firebase](json-firebase.md) mini-unit
- APIs can also be used to write data. To learn more about writing data to a database, see the [JSON & Firebase](json-firebase.md) mini-unit

## APIs + JavaScript

Now that you see how an API is a request for data, you'll probably want to use that data somewhere in an app. Although there are a number of ways of making API requests using JavaScript (e.g. using [vanilla JavaScript](https://plainjs.com/javascript/ajax/send-ajax-get-and-post-requests-47/) or [jQuery](https://www.w3schools.com/jquery/ajax_ajax.asp)), we're going to focus on how to integrate API requests with React.

Before jumping into React, however, we need to strategize a bit about how we want to use APIs to make for a seamless user experience.

### Async & Await

When we make a request to an API, sometimes it takes a bit of time for the API to respond. So if we wait to fully load a webpage until the API responds, we might be making our user wait a long time before the page loads, if it even loads at all. In the worst case scenario, if we wait to load a page until an API responds and the API never responds, then the page will never load and the user will have a terrible experience; we can't rely on the success of a call to the API in order to load the page.

To overcome this problem, we need to use a way of calling the API where the call is done as soon as a page loads, and then the response (or lack thereof) can trigger a secondary event such as rendering the data. This pattern of requesting data in parallel to the normal loading order of the page is done using a "promise" pattern called `async` (for asynchronous). Then, the code will `await` a response from the API, and depending on the response - usually either `success` or an `error` - will do something. `async` and `await` are useful because they won't block the rest of the page from continuing to load even though data hasn't been received yet.

### Using `async` & `await`

There's a lot to be said about using `async` and `await` properly, and there's a lot of complexity to handling and manipulating promises efficiently, but the basic anonymous version of the `async-await` functional pattern (with error handling) is below:

```javascript
(async () => {
  try {
    let response = await fetch('https://someAPIcall');
    // other await statements could go here

    // other code to execute once response is defined
  } catch(err) {
    // catches errors in any of the await statements in try {}
    alert(err);
  }
})();
```
- the `async` keyword is placed before the function to define it as an asynchronous function and to indicate that it will return a promise
- `try ... catch` is used to handle any errors that might result from an unsuccessful API request
- `await` makes JavaScript wait until the `fetch()` request has returned a result
> Note: `await` only works within an `async` function

So the anonymous function will wait for `https://someAPIcall` to return a result. If it does return a result, then it will store that as the `response` variable and then do something with it. If the request throws an error, then the function will do what's the in the `catch` code block, in this case showing an `alert()` with the error.

> Note: you may also come across a different pattern that uses `.fetch()`, `.then()`, and `.catch()` to handle API data. It is similar to `async await` in that `.fetch()` is used to make a request, `.then()` indicates what to do once a response is received (and any steps thereafter), and `.catch()` indicates how to handle errors. It doesn't, however, operate asynchronously so it will block the page loading until the promise resolves.

### Using `async` & `await` in React

Now that we know what an API is and how it works, and how to make `async` API requests, we can see how to make an API call in React.

API calls are typically done in the `componentDidMount` method of a component, and we can identify `componentDidMount` as an `async` function in order to use `async await` and `try ... catch` as part of the method:

```javascript
import React from 'react';
// any other import statements

const Item = () => {
  const component = new React.Component();
  component.state = {
    // define state variables here
  }

  async component.componentDidMount = () => {
    try {
      let response = await fetch('https://someAPIcall');
      // other await statements could go here

      // other code to execute once response is defined
    } catch(err) {
      // catches errors in any of the await statements in try {}
      alert(err);
    }
  }
  
  component.render = () => {
    return (
      // render component HTML here
    )
  }

  return component;
}
   
export default Item;
```
> Note: you can use `setState` within the `componentDidMount` method to update any state variables

## NYC Open Data APIs

- Documentation
- Peculiarities, difficulties, troubleshooting

> This is both the hardest part of the task I've set before you, Jeffrey, and also the most important.
> 
> If the WHOLE lesson can be focused mostly around this, with an aside to "btw, giphy's api is much easier", that feels right to me.
> 
> If that's not possible, let me know and we can brainstorm scaffolding.

- For visualizing API data - check out the [Victory](victory.md) mini-unit

## Close

Some text here

#### Questions for students

- Some text here