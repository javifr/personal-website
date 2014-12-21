---
title: Consuming Hypermedia APIs with Restangular
date: 2014-12-21 13:17 UTC
tags: order, angular, hypermedia, restangular
---

# Consuming Hypermedia API's with Restangular

At [Loyal Guru](https://loyal.guru/) we've build an [Hypermedia](http://stateless.co/hal_specification.html) Rest API that is powering our different apps ( hybrid mobile applications and product backend ). This different clients are [Angular](https://angularjs.org/) applications consuming the API with the great [Restangular](https://github.com/mgonto/restangular) library.

Before jumping into Restangular we've tried with [ngResource](https://docs.angularjs.org/api/ngResource/service/$resource) ( too simple for the task at hand and [not based on promises](https://github.com/mgonto/restangular#differences-with-resource) ), [Angular-hal](https://github.com/LuvDaSun/angular-hal) ( nice but immature ) and some others ( [Traverson](https://github.com/basti1302/traverson) ). Restangular has proven to be very easy to use, with a good documentation and is very extended among the Angular community.

Let's jump into some code that shows how to consume an Hypermedia API ( embedded resources, links, etc... ) with Restangular.

## Musicians and instruments

Let's say we have an endpoint for musical **instruments** and another endpoint for **musicians** with and hypothetical https://api.accme.com base url. An instrument has many categories, and a musician has many instruments. So for the sake of the example, categories will be embedded resources for an instrument resource and instruments will be embedded resources for the musician resource.

### Singular Instrument resource 
Response to a GET https://api.acme.com/instruments/1

~~~json
{
  "id": 1,
  "name": "Fender Telecaster American Deluxe",
  "description": "This Guitar has a new compound-radius fretboard ...."
  "_embedded":{
    "categories":[
      {
        "id": "2",
        "name": "guitar"
        "_links":{
          "self":{
            "href": "/categories/2"
          }
        }
      },
      {
        "id": "10",
        "name": "solid body"
        "_links":{
          "self":{
            "href": "/categories/10"
          }
        }
      }
    ]
  }
  "_links":{
    "self":{
      "href": "/instruments/1"
    },
    "thumbnail":{
      "href": "http://path.to/an/awesome/image.jpg"
    }
  }
}
~~~

Some comments:   

- Note the **_embedded** attribute, it has the categories key with an array of category resources ( summarized ).

- The thumbnail image of the resource is represented as a **_link** attribute, the same goes for the **self** reference of the resource.

### Paginated collection of Musician resources 
Response to a GET https://api.acme.com/musicians?page=2

~~~json
{
  "_embedded":{
    "musicians":[
      {
        "id": 10,
        "name": "Bo Madsen",
        "age": 34,
        "_embedded":{
          "instruments":[
            {
              "id": 1,
              "name": "Fender Telecaster American Deluxe"
              "_links":{
                "self":{
                  "href": "/instruments/1"
                }
              }
            },
            {
              "id": 12,
              "name": "Guild Starfire V"
              "_links":{
                "self":{
                  "href": "/instruments/12"
                }
              }
            }
          ]
        }          
      },
      ... more musicians
    ]   
  }
  "_links":{
    "self":{
      "href": "/musicians?page=2"
    },
    "next":{
      "href": "/musicians?page=3"
    },
    "prev":{
      "href": "/musicians"
    }
  }
}
~~~

Some comments:

- An index of resources is represented as a list of embedded resources ( this is a practice followed by [Teowaki Api](https://developers.teowaki.com/api-hypermedia#listsasembeddedresources) that we also adopted ).

- Every musician resource has an **embedded collection** of instrument resources. 

- The **_links** next and prev help you with the pagination.

### Restangular seetings for the API

~~~js
// Keep in mind that a provider must be used only on a config block
app.config(function(RestangularProvider)){

    // Set the base url for your API endpoints
    RestangularProvider.setBaseUrl("https://api.acme.com");

    // You can set some default headers for calling the API
    RestangularProvider.setDefaultHeaders(
        { "Accept": 'application/vnd.acme-v1'},
        { "Content-Type": "application/json+hal" }
    );

    // Set an interceptor in order to parse the API response 
    // when getting a list of resources
    RestangularProvider.setResponseInterceptor(function(data, operation, what) {
      if (operation == 'getList') {
        resp =  data._embedded[what];
        resp._links = data._links;
        return resp
      }
      return data;
    });

    // Using self link for self reference resources
    RestangularProvider.setRestangularFields({
      selfLink: 'self.link'
    });

});
~~~

Some comments:

- Check [Self reference resourcees](https://github.com/mgonto/restangular#using-self-reference-resources)

- Check [Response wrapped with metadata](https://github.com/mgonto/restangular#user-content-my-response-is-actually-wrapped-with-some-metadata-how-do-i-get-the-data-in-that-case)


### API Consumption

~~~js
app.run(function(Restangular)){

    // Fetch a single product 
    Restangular.one("instruments",1).get().then(function(instrument){
        // Get an attribute
        console.log(instrument.name);
        // Get the name of a category related to the requested instrument
        console.log(instrument._embedded.categories[0].name);
        // Get the thumbnail image of the resource
        console.log(instrument._links.thumbnail.href);
    })

    // Fetch a collection of musicians
    Restangular.all("musicians").getList().then(function(musicians){
        // Get the first musician
        var musician = musicians[0];
        console.log(musicians.name);
        // Get the first instrument related to the musician
        console.log(musicians._embedded.instruments[0]);
        // Get the uri of the next page of musicians
        console.log(musicians._links.next.href);

        // Change the name attribute for the musician
        musician.name = "Steven";

        // This will do a PUT to https://api.acme.com/musicians/1
        // Thanks to the self reference property specified above
        musician.put()
    })

});
~~~


That would be it! With a bunch of lines of code you can start consuming Hypermedia Rest Api's with Restangular.

For the moment I'm quite happy with the whole "pseudo" Hypermedia implementation that we've done on our company. I say pseudo becasue we are not that strict. We are not using **curies** and we don't even have self reference links in every resource, we are just taking some parts of the specification that helps us on building faster ( pagination as _links, not needing to build manually the urls of the Api calls, accessing easily to nested or embedded resources, etc... ). 

I can see a huge potential on Hypermedia but I'm missing some big things too. For example the ability to have a form specification, perhaps something like [Json Form](https://github.com/joshfire/jsonform) would be a nice starting point.

## Related resources:

- [What is an API?](http://apievangelist.com/)
- [Kin Lane on Hypermedia](http://hypermedia.apievangelist.com/)
- [Hypermedia Specification](http://stateless.co/hal_specification.html)
- [Teowaki Api](https://developers.teowaki.com/api-hypermedia)
- [Hypermedia the next "cloud"?](http://blog.smartbear.com/api-testing/kin-lane-why-all-the-hate-toward-hypermedia/)

