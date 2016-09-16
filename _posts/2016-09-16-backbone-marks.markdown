---
layout: post
title:  "Backbone - заметки"
date:   2016-09-16 10:14:02 +0300
categories: backbone js
-----------------------


# Models and Views

http://backbonejs.org/docs/images/intro-model-view.svg

A Model manages an internal table of data attributes, and triggers "change" 
events when any of its data is modified. Models handle syncing data with 
a persistence layer — usually a REST API with a backing database. 
Design your models as the atomic reusable objects containing all of 
the helpful functions for manipulating their particular bit of data.


A View is an atomic chunk of user interface. It often renders the data 
from a specific model, or number of models — but views can also be 
data-less chunks of UI that stand alone. Models should be generally 
unaware of views. Instead, views listen to the model "change" events,
and react or re-render themselves appropriately.


Each View manages the rendering and user interaction within its own DOM
 element. If you're strict about not allowing views to reach outside 
 of themselves, it helps keep your interface flexible — allowing views
  to be rendered in isolation in any place where they might be needed.


# Collections

http://backbonejs.org/docs/images/intro-collections.svg

A Collection helps you deal with a group of related models, handling 
the loading and saving of new models to the server and providing helper 
functions for performing aggregations or computations against a list of 
models. Aside from their own events, collections also proxy through 
all of the events that occur to models within them, allowing you to 
listen in one place for any change that might happen to any model in 
the collection.



# Backbone.Events
