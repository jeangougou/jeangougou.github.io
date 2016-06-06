---
layout: post
title:  "JavaScript journey to CQRS/ES in JS - pt1"
date:   2016-06-06 18:49:23 +0000
categories: programming
description: "evolution from classical N-tier development architectures to CQRS, and subsequently Event sourcing with sample JavaScript implementation; part 1"
published: true
tags:
- CQRS
- JavaScript
- event sourcing
- MEAN
---



Everyone knows what an MVC architecture is, or let's assume so.
We can start this journey with a simple example by focusing on improving the sample [Todo MVC][TodoMVC-angularPerf]. The version selected is a simplified one leveraging angularjs with a little performance optimization. Don't worry about this, it is quite simple and we will explain what happens under the hood.

There are reasons why this version was picked over the others, mainly because it is the best combination of clarity of implementation and it does not go too far away from what are the best practices of building a larger production ready application.

### First steps

We will pull our interesting bit of code, either from the main repository of TodoMVC or from the dedicated repository for this article set.
Also a small set of changes is required to evolve the application later on; but first things first.

```
// we can pull everything from the main TodoMVC project, then filter out only what we need
git clone https://github.com/tastejs/todomvc.git

// and copy the correct folder into our repository
cd todomvc\examples\angularjs-perf
cp .* ~/JsJourneyToCqrsEs

```

We can notice that node_modules folder is not empty already, and that is not a good long term strategy for a project, particularly in a team. There a few files to move outside of this folder, and we can address where they should go.


```
// from the project root, let's create the css folder and a subfolder of js
mkdir css
cd js
mkdir todomvc-common
cd ..

// back to the project root
mv node_modules/todomvc-app-css/index.css ./css/index.css
mv node_modules/todomvc-common/base.css ./css/base.css
mv node_modules/todomvc-common/base.js ./js/todomvc-common/base.js

```

Then we'll use [bower][bower] to install the angular dependency and update the ```index.html``` and the ```.gitignore``` files accordingly.

Alternatively we can grab the [initial setup from the existing project][v0.0.1].

### Adding a backend

A MEAN stack application is the logical next step.
First we need to add express to the mix to be able to serve the static single page application in angular, this requires again some restructuring of the project. After having moved the ```index.html``` page into the ```/views``` folder in root we can store every other library into the public folder.
Express has a dedicated way of serving static files and we can enhance it even further by adding CDNs, cache and ETag headers.

It is quite common to add the following snippet to the ```package.json``` file:

```
"scripts": {
  "start": "node server"
},
```

to allow npm automated start scripts to work under different contexts.

We can now create a simple ```server.js``` file that will serve our application. Keeping in mind the importance of separating configuration, routing and business logic, the file structure will be as follows:

```

├───config
│   ├───config.js
│   └───express.js
├───controllers
│   ├───index.js
│   └───todos.js
└───routes.js

```
Where ```express```, ```config``` and ```routes``` are respectively files dedicated to store generic application configuration, express specific configuration and routing procedures. The other files in the controllers folder are placeholders for our next step.
The initial implementation will leave ```todos``` completely empty and ```index``` resolving the static html file for the single page application.

You can [download this version][v0.0.2] from git.

By adding a small API layer and a MongoDB storage it is possible to start separating concerns better, isolate UX/UI from business logic and persistence mechanisms.


[v0.0.2]:https://github.com/jeangougou/JsJourneyToCqrsEs/releases/tag/v0.0.2
[v0.0.1]:https://github.com/jeangougou/JsJourneyToCqrsEs/releases/tag/v0.0.1
[bower]:http://bower.io/
[TodoMVC-angularPerf]:https://github.com/tastejs/todomvc/tree/gh-pages/examples/angularjs-perf
