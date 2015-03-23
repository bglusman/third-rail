third-rail
==========

##voltrb on rails

The goal is to provide access to voltrb from within existing rails apps via addition of a third-rail gem.

Any thoughts or discussion is welcome at https://gitter.im/catprintlabs/third-rail

##install

Currently this repo is just a demo rails app, then pulls in a modified version of volt.
Clone the repo,
bundle install
bundle exec rails s

The app should come up...  let me know via gitter if you have any problems.

##implementation status:

So far the only thing implemented is the being able to use volt to create a layout within the rails app, and to have volt request a partial be delivered as part of the volt view.  This is _very preliminary proof of concept implementation_.

Volt code goes into a directory called "voltage" within the top level rails "app" directory.

The volt code follows the normal volt framework conventions, within that directory, with the addition of a the

    {{ content_for }}
    
binding.  `content_for` will be replaced with a view from rails that has the matching name as the current volt view being rendered.

To get things started on the rails side use the `volt_layout` helper 

   <%= volt_layout %>

The `content_for` binding can tie into a particular part of the rails view by specifying a symbol.  The matching rails view will have a matching `content_for` with the same symbol.  For example

    {{ content_for :footer }}

will be replaced by the some section of the rails view that is defined by

    <% content_for :footer do %>
       # stuff to go into the footer
    <% end %>

To summarize:

* Within the rails layout do a `<%= volt_layout %>` to hand control for rendering the the layout to volt.
* The volt code goes in a `voltage` directory within the rails `app` directory.
* Within a volt view you give control of rendering back to rails by doing a `content_for`
* Within the matching rails view you can break up parts of the content by labelling them as `<% content_for symbol do %> ... <% end %>`.  These sections will matched to any `{{ content_for symbol }}` bindings in the volt view.


 
##Initial thoughts (sort of stream of conscience random ideas)

rails and volt will coexist with volt being a separate rack app.  Communication between the two will be via mongodb.

add mongoid or mongomapper to active record models ala https://github.com/hayesdavis/active-expando (very old but provides a starting point)  similar concept here: http://britg.com/2012/01/07/forging-forgecraft-a-hybrid-sql-mongodb-data-solution/

this should provide the basic communication mechanism.

##syncing the two databases

###Brute Force:
Syncronize every change between the databases using activerecord and mongos builtin monitoring...

###More Intelligent - but more work:
Describe mapping in the active record models to volt models 

###Harder still - but probably how you would do it from scratch:
Map volt models to rails, and manually connect

###Forget Mongo + Rails...  
use an automatically generated API to talk between the volt app and rails.

##Different Approach altogether

think of a typical migration.  You have some pages that are rails views, but you want to start using volt.
What you would like initially is something like this:
* request the page, 
* controller does normal stuff
* then makes sure that any "volt" models are synced to the rails data.
* then does a "special" render operation that hands control over to volt, which delivers the page
