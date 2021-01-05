---
title: Rails 6 and TailwindCSS 2.0
date: 2020-12-02 12:17:46 +0800
categories: [rails]
tags: [rails, tailwind]
---

I started working on a new Rails 6 app and thought this would be a good time to
try the newly released [TailwindCSS 2.0](https://tailwindcss.com).
Below are the steps I followed to set it up.

### Create a Rails 6 app
{% highlight ruby %}
  rails new app
{% endhighlight %}

I followed the installation noted in the [official docs](https://tailwindcss.com/docs/installation),
but had some issues with PostCSS and got the following error,
{% highlight shell %}
  Error: PostCSS plugin tailwindcss requires PostCSS 8.
{% endhighlight %}

so I followed the [PostCSS 7 compatibility build](https://tailwindcss.com/docs/installation#post-css-7-compatibility-build)

Used `yarn` to install the packages
{% highlight shell %}
  yarn add tailwindcss@npm:@tailwindcss/postcss7-compat @tailwindcss/postcss7-compat postcss@^7 autoprefixer@^9
{% endhighlight %}

That will update our `package.json` file to add those packages.
In order to customize our Tailwind installation, we need to generate a config file for our project with:
{% highlight shell %}
  npx tailwindcss init
{% endhighlight %}

The config file is created in the root of the app and you can leave it there, but I usually create a new folder and move it to `app/javascript/stylesheets`. 
We can also add files to purge in the config under the purge array like this:
{% highlight javascript %}
  purge: [
      './app/**/*.html.erb',
      './app/helpers/**/*.rb',
      './app/javascript/**/*.js'
    ],
{% endhighlight %}

Then we need to create a CSS file under `app/javascript/stylesheets` named `applciation.scss` and add the following code:
{% highlight css %}
  @import "tailwindcss/base";
  @import "tailwindcss/components";
  @import "tailwindcss/utilities";
{% endhighlight %}

Edit `app/javascript/packs/application.js` and import the `application` file we just created with the following line after the requires.
{% highlight javascript %}
  import "stylesheets/application"
{% endhighlight %}

In the layout `app/views/layouts/application.html.erb` we need to add a stylesheet_pack_tag after the imports
{% highlight html %}
  <%= stylesheet_pack_tag 'application', media: 'all', 'data-turbolinks-track': 'reload' %>
{% endhighlight %}

If we have any custom CSS, we need to import it in `app/javascript/stylesheets/appication.scss` between these lines
{% highlight html %}
  @import "tailwindcss/components";
  // Custom stylesheets

  @import "tailwindcss/utilities";
{% endhighlight %}

Last, we need to add the Tailwind configuration to our PostCSS config. Under 'plugins', require Tailwind:
{% highlight javascript %}
  plugins: [
    require('tailwindcss')('./app/javascript/stylesheets/tailwind.config.js'),
{% endhighlight %}


We can add some code to our view and check if everything is working
{% highlight html %}
  <div class="max-w-md mx-auto bg-white rounded-xl shadow-md overflow-hidden md:max-w-2xl">
    <div class="md:flex">
      <div class="md:flex-shrink-0">
        <img class="h-48 w-full object-cover md:w-48" src="https://images.unsplash.com/photo-1515711660811-48832a4c6f69?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=448&q=80" alt="Man looking at item at a store">
      </div>
      <div class="p-8">
        <div class="uppercase tracking-wide text-sm text-indigo-500 font-semibold">Case study</div>
        <a href="#" class="block mt-1 text-lg leading-tight font-medium text-black hover:underline">Finding customers for your new business</a>
        <p class="mt-2 text-gray-500">Getting a new business off the ground is a lot of hard work. Here are five ideas you can use to find your first customers.</p>
      </div>
    </div>
  </div>
{% endhighlight %}


Now we can start the Rails server
{% highlight ruby %}
  rails s
{% endhighlight %}

And we should see a card like this:
![relay](/assets/img/2020-12-02/tailwind-card.png)