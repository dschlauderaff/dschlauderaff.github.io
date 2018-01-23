---
layout: post
title:      "Rails 5.1 with Webpack, component focused frontend "
date:       2018-01-23 17:10:22 +0000
permalink:  rails_5_1_with_webpack_component_focused_frontend
---


Now that I'm finished with Flatiron's coursework, I'm finding that front-end development has evolved quite a bit from the "classic Rails" with Asset Pipeline, Sprockets, CoffeeScript, and Sass. In particular, the shift from front-end code from "pages" to *components*.

There is a lot that is new out there, and while the "old way" still works, but the ever-growing complexity of front-end, navigating all of the separate folders for view templates, javascripts, stylesheets, and images can lead to a seemingly unstructured mess.

Speed can become an issue: according to Heroku, handling the Asset Pipeline is the slowest part of deploying a Rails app, over 20x slower than installing depencies via `bundle install`. Changing a line of CSS in development and reloading the browser to see the result can also take time - seconds that add up quickly over the course of app development.

Keeping dependencies up to date can be a hassle. To add a JS library to your project, you either load the code from a CDN, cut and paste it into `lib/assets`, `app/assets`, or `vendor/assets`, or (hopefully) it has been wrapped into a gem. Meanwhile, in the JavaScript community, the same thing can be managed with a single command: `yarn add`, Yarn is the Bundler of Javascript, and it is hot stuff.

Rails 5.1 brought Webpack integration with the webpacker germ, node_modules through Yarn, out-of-the-box sjupport for Babel, React, Vue, and PostCSS. But the Asset Pipeline and CoffeeScript still reign, and `rails new` will still start your project the "good old way". This post sets out some best practices others have developed to build a modern front-end.

Components are king in the modern front-end: every logical part of a UI should be self-contained. Rails does this through view partials. But if you are using JavaScript in those partials, it is (what my grandfather would call:) "a fur peice" away, in the `app/assets/javascripts`. There is a way to bring everything together, have partials, scripts, and styles, living happily. And changes can be made easily because we know exactly where to look.

## Using PostCSS

PostCSS is written 100% JavaScript, and easily extendable and customizable with many plugin. One of them `cssnext`, generates polyfills for features that are not supported by browsers yet, but only as long as it is necessary.

## The starting point

So `rails new` won't work for the new hotness. This is the starting command:

> `$ rails new project-name --skip-coffee --skip-sprockets --skip-turbolinks --webpack --database=postgresql`

Using PostgreSQL as the default database makes it easier to deploy to Heroku. `--webpack` tells Rails to use the webpacker gem to bundle our assets with Webpack. This gives us the following tools:
* A `node_modules` folder that contains the JS dependencies (also adds it to you .gitignore)
* A `package.json` to declare dependencies, and a `yarn.lock` for adding packages with `yarn add`, awesome.
* `.babelrc` file configured for transforming ES6 int JavaScript code compliant with any browser that currently has more than 1% market share
* `.postcssrc.yml` configured with postcss-import and postcss-next plugins that allow use of cssnext features

Some things need to be added manually:

Add file `.browserslistrc` to the root directory and in the file add this single line:

> `> 1%`
> 
That's browser compatibility done!

Also, the default behavior of Rails generators needs to be changed. They won't need to put anything into `app/assets`, because the folder is going to be removed. In `application.rb` add these lines:

> `# config/application.rb
> config.generators do |g|
>   g.stylesheets   false
>   g.javascripts     false
>   g.helper              false
>   g.channel           assets: false
> end`
> 

Remove the `app/assets `folder. The `--webpack` option creates a folder named `app/javascript`. Move it to the root directory, rename it `frontend`. Do not remove anything inside the folder. The `application.js` is the entry point for Webpack.

In `application.html.erb`, repalce the `javascript_include_tag "application"` with `javascript_pack_tag "application"` and move the pack tag from the `<head>` to the very end of the `<body>`, right after the `yield` statement.

Replace `stylesheet_link_tag 'application', media: 'all'` with `stylesheet_pack_tag 'application'`. CSS will be done on a per-component basis with the help of Webpack and ES6 `import` statement. That means all our styles will be handled by webpacker too.

In the rails `config` folder, change the `webpacker.yml` to reflect the changes in our folder structure: 

```
  default: &default
  source_path: frontend
  source_entry_path: packs
  public_output_path: packs
  cache_path: tmp/cache/webpacker
```

ERB partials will be in the frontend folder, so we need to tell the controllers how to find them. In `application_controller.rb`:

```
# app/controllers/application_controller.rb
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception
  # That's all there is:
  prepend_view_path Rails.root.join("frontend")
end
```

To include webpacker's hot reload of JS/CSS code, `webpacker-dev-server` needs to be running alongside `rails s`. Create a `Procfile` and put this in it:

```
server: bin/rails server
assets: bin/webpack-dev-server
```

You can then use Foreman to launch all processes. Create a controller with a root page:

`rails g controller pages home`

```
# config/routes.rb
Rails.application.routes.draw do
  root to: "pages#home"
end
```

Remove any generated code from `views/pages/home.html.erb`.

##Creating components

`application.js` will only be used to contain import statements. It is our entry point. For app-wide stylesheets and javascripts, we need some place to keep them:

```
$ mkdir frontend/init
$ touch frontend/init/index.js
$ touch frontend/init/index.css
```

Now that folder needs tobe registered with our entry point. In `packs/application.js`: 

```
// frontend/packs/application.js
import "init";
```

Now in `init/index.js`:

```
// frontend/init/index.js
import "./index.css";
```

and in `init/index.css`, general font styling and `normalize.css`:

```
/* frontend/init/index.css */
@import "normalize.css/normalize.css";

body {
  font-family: "Helvetica Neue", Helvetica, Arial, sans-serif;
  font-size: 16px;
  line-height: 24px;
}
```

Create a `componenets` folder inside of `frontend`. Each component will be a folder with 3 files: the partial, the javascript, and the css. To create a page as a layout template:

```
$ mkdir -p frontend/components/page
$ touch frontend/components/page/{_page.html.erb,page.css,page.js}
```

In `page.js`
```
// frontend/components/page/page.js
import "./page.css";
```

In `page.css`:
```
/* frontend/components/page/page.css */
.page {
  height: 100vh;
  width: 700px;
  margin: 0 auto;
  overflow: hidden;
}
```

In `_page.html.erb`:
```
<!-- frontend/components/page/_page.html.erb -->
<div class="page">
  <%= yield %>
</div>
```

Note, the `yield` allows us to nest components inside each other.

Reference the component in `application.js` by adding `import "components/page/page";`

Finally, add some ERB code to `home.html.erb` veiw"

```
<!-- app/views/pages/home.html.erb -->
<%= render "components/page/page" do %>
  <p>Hello from our first component!</p>
<% end %>
```


