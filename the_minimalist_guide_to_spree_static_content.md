#The minimalist guide to Spree Static Content 

In many [Spree](http://spreecommerce.com/) projects where I worked previously, it had become necessary to handle static pages like Help, Terms and Conditions, About Us, etc... in short, all those *"stock pages"* and some other that came out of the mold. 

I always recommend to my colleagues to deliver a simple way to generate the pages, although I ***"LOVE"*** to write all the HTML with my own hands, it is more efficient to use [Rails](http://rubyonrails.org/) helpers or some other markup as [HAML](http://haml.info/).

The point is, as consultants, it is our duty to deliver an stable, functional, installable and, somehow, recoverable product. For this, it has always given me good service the extension [Spree Static Content](https://github.com/spree-contrib/spree_static_content) because it is simple to configure and we can easily generate a task to regenerate all the *"core"* static pages of our project; for those cases where the customers ***"accidentally"*** deletes their contents.

*** lib/tasks/static_content.rake *** 

```
namespace: static_content do 
   desc 'Reload static pages' 
   reload task: environment do 
     Spree::Page.destroy_all 
     pages = YAML.load_file(Rails.root.join ('db', 'fixtures', 'static_content.yml')) 
     controller_instance = ApplicationController.new 
     pages.each_value do |data| 
       html = controller_instance.render_to_string (template: data ['body']) 
       data['body'] = html 
       Spree::Page.create (data) 
     end 
   end 
end 
```

As you see, the rake task is quite simple and consists of the following steps: 

1. ***Delete existing pages:***
 
	This is a matter of taste, it is possible to separate the *"default"*  static pages from any other with some scope or you are able to find specific pages you want to delete, in my case, I'm destroying all of them. 

2. ***Read the information on pages:*** 

	For convenience, I always create a "setup" file separately which contains the necessary information for our pages such as *Title*, *Slug*, and the name of the View that we'll be using as our static page's *Body*.

3. ***Create an instance of a controller:*** 

	This is to *delegate* the rendering of the views to the proper object allowing us, as I said before, to use view helpers or another abstraction markup language like [HAML](http://haml.info/).

4. ***Iterate each page:*** 

	For each entry we have in our configuration file, we're going to generate the body of the page letting our Controller instance the renderee, from there on out we only create the Spree::Page. 

#####Static Content Example config file: 

*** db/fixtures/static_content.yml*** 

```
privacy_policy: 
   title: Privacy Policy 
   body: 'spree/static_content/_privacy_policy' 
   slug: '/privacy-policy' 
   show_in_header: false 
   show_in_footer: true 
   position: 0 
   visible: true 
   layout: '' 
   render_layout_as_partial: false 

help: 
   title: My Site Help 
   body: 'spree/static_content/_help' 
   slug: '/help' 
   show_in_header: true 
   show_in_footer: true 
   position: 1 
   visible: true 
   layout: '' 
   render_layout_as_partial: false 
```

#####Example page: 

***app/views/spree/static_content/_privacy_policy.html.haml*** 

```
%h2 General Statement of Principles 

%w 
   As described in detail below, any information we gather at This web site is 
   Strictly for our use and is not shared With Any other entity, public or 
   private, for ANY reason - period. We will not sell or give away Any lists or 
   other data That We May Retain and we do not purchase Such information from 
   other sources ... 

= Image_tag 'banner.png', width: 100, height: 10, alt: 'Policy Banner' 

%h3 
   For Any question Please send us a mail to 
   = Link_to 'info@mysite.com', 'mailto: info@mysite.com', alt: 'Info' 
   %span Directly or send your question: 

= Form_tag do 
   Text_field_tag ​​=: name 
   .... 
```

That's all, we just need to go to a terminal an run:

***terminal***

```
$ be rake static_content:reload
```

And Voilà, we won't have headaches trying to update, maintain or recover any *"static page"*" we deliver and the customer has been erased ***"accidentally"***.

Questions, comments and suggestions are always welcome: 

twitter: ***@mumoc***

github: ***mumoc***

