

## Add an image from the template 

For a page with the path : ``` src/templates/ab-landing-page.html ```

```html 
<img src="{{get_asset_url('../images/fs-trackman.jpg')}}" height="200" class=" block m-auto w-8/12">
```

## Make content editable without using a module 

Instead of creating simple raw content like :

```
<p>Lorem ipsum dolor sit met, qui at desert mandamus, adduce ullum apeirian mea at. Eu mel vide saltando vituperata, sonet quidam deterruisset te qui. Te cum vivendum explicate abhorrent. Id venom argumentum vel. Ut lorem bocent hendrerit eam.</p>
```

It's better to use a widget block like :

```
    {% widget_block rich_text "intro_paragraph" overrideable=True, label='intro_paragraph'  %}
      {% widget_attribute "html" %}
        <p>Lorem ipsum dolor sit met, qui at desert mandamus, adduce ullum apeirian mea at. Eu mel vide saltando vituperata, sonet quidam deterruisset te qui. Te cum vivendum explicate abhorrent. Id venom argumentum vel. Ut lorem bocent hendrerit eam.</p>
      {% end_widget_attribute %}
    {% end_widget_block %}
```

The content is now editable in the editor see : http://recordit.co/4TdPMknzRc

widget_block is now deprecated use instead: 

```jinja
{% module_block module "my_rich_text_module" path="/My Rich Text Field Module",
  label="My Rich Text Field Module" 
%}
    {% module_attribute "rich_text_field_variable" %}
       <div>My HTML block</div>
    {% end_module_attribute %}
{% end_module_block %}
```

## Create a custom module

Here "hero-banner" is my custom module name. 

```
 hs create module hero-banner  ./src/modules/
```

If in meta.json ```  "global": true,``` is set. Any modification to the module will chabge the module content on every page. 

I guess to have multiple instance of that module I should put it inside a dnd section.


### fields.json 

possible values :

```
    "type": "boolean",
    "display": "checkbox",
```

```
 "type": "number",
 "type": "font",
  "type": "text",
```  



**Make it available for new content***

In meta.json  be sure to have is_available_for_new_content set to ```true````

```
 "is_available_for_new_content": true
 ```

### meta.json

It's posssible to restrict a module to a type of pages. Just add in the array the compatible page types

```JavaScript 
 "host_template_types": [ "BLOG_LISTING","PAGE" ],
```

### Overides fields when the module is called in a template 

Here I'm calling my custom ```ab-heading.module``` from a section ``` src/sections/ab-forms.html ```

```
     {% dnd_module
        path='../modules/ab-heading.module',
        bold_sentence="Contact Us",
        light_sentence="ask a question"
      %}
      {% end_dnd_module %}
``` 

with 

```
  bold_sentence="Contact Us",
  light_sentence="ask a question"
```

I overided the default values see : https://recordit.co/BkxSgdLK9q


### Create repeatable fields in a Module 

It's possible to create repeatable fields by adjusting the ```fields.json``` file. But it's easier to create the fields throught the design manager. 

To do so : 

1 - Create the fields 
2 - create a group and add the fields to the group 
3 - In the group option activate "repeater options" 
4 - In the group on top user Hubl vatiable name click on copy then "copy snippet"

Follow the gif for more details : https://recordit.co/wOUcGAjqNz



## Sections 

Sections are living in ```  src/sections/ab-forms.html ```

**HeadsUp** : If you modify a section in the code, you have to remove it and to readd it in the editor to see the modifications. 


<hr/>

## Blocks 

Blocks are like Hooks in WordPress 
```
    {% block body %}
    {% endblock body %}
``` 

I can for example create a landing page without navigation by overwriting the block 

This is my original block I have on ```base.html```

```
{% block header %}
    {% global_partial path='../partials/header.html' %}
{% endblock header %}
``` 


```
{% block header %}
  {% global_partial path='./partials/header-custom-antoine.html' %}
{% endblock header %}
``` 


<hr/>

## Drag and drop 


To include custom modules in my page I have to create a dnd area like this 


```
  {% dnd_area 'dnd_area'%}  

    {% dnd_section vertical_alignment='TOP' %}
        {% dnd_module
        path='../modules/hero-banner.module',
        width=12
        %}
        {% end_dnd_module %}
    {% end_dnd_section %}



    {% dnd_section vertical_alignment='TOP' %}
      {% dnd_module
      path='../modules/hero-banner.module',
      width=12
      %}
      {% end_dnd_module %}
    {% end_dnd_section %}


  {% end_dnd_area %}

```  

**The dnd name has to be unique** which means you can't have two ``` {% dnd_area 'dnd_area_'%} ``` in the page. Otherwise only one will be displayed ! 

**Issues** : 

My module doesn't take the full with even If I set ```  width=12 ``` there's some default inline CSS added to my page... 

Solution : 

Be sure to add this include to the main.css !

```
{% include './objects/_containers-dnd.css' %}
```



### Dnd Grid

Here is an example of a grid with 3 cols : 

```
 {% dnd_area 'footer'
    label='Footer',
    class='footer__container content-wrapper'
  %}


    {% dnd_section %}

      {% dnd_column %}

        {% dnd_row %}


        {% dnd_module
          path='@hubspot/rich_text',
          html='<div style="text-align: center; font-size: 0.7rem;" class="border"> COL1 </span></div>'
          offset=0,
          width=3
        %}
        {% end_dnd_module %}


        {% dnd_module
        path='@hubspot/rich_text',
        html='<div style="text-align: center; font-size: 0.7rem;" class="border"> COL2 </span></div>'
        offset=3,
        width=3
        %}
        {% end_dnd_module %}



        {% dnd_module
        path='@hubspot/rich_text',
        html='<div style="text-align: center; font-size: 0.7rem;" class="border"> COL3 </span></div>'
        offset=6,
        width=3
        %}
        {% end_dnd_module %}





        {% end_dnd_row %}


      {% end_dnd_column %}
      
    {% end_dnd_section %}

  {% end_dnd_area %}

``` 

## Heads up !!

**Comenting Hubl includes in a CSS file with CSS coment doesn't work**

Dont do this : it's useless

```css
/* {% include './objects/_layout.css' %} */
/* {% include './objects/_containers-dnd.css' %} */
``` 


## Templates 

### Blog listing page 

Access the blog listing page in the HubSpot interface like this : https://recordit.co/4AjL1zNyEL 

The template to modify is ``` src/templates/blog-index.html```

It's possible to pick a specific template by going in the Admin interface ( see Admin interface section).

To pick a specific template follow the gif : http://recordit.co/JDzoP6TzKz

### Blog single page 

```
src/templates/blog-post.html
```

### Registration template and sepecific templates 

It's possible to overide default template by creating them in the design manager like so : https://recordit.co/wtiBpfMeLt

The default url for the registration page is : 

https://hs.antoinebrossault.com/_hcms/mem/register


**To Link** the custom template to the site you have to go in the CMS settings : https://recordit.co/CLyXw64m6M 
to tell the CMS to use a custom template. 

PS : changes to the template in local are available right away on the live site ( if you ran ``` hs watch src my-theme ```)


### sytem teamplates 

Customize the 404 etc... 

If I update the 404 in this file : 

```
src/templates/system/404.html
``` 

I have to activate in in the setting to make it work see : https://recordit.co/zF1uwPXK0r


## Hubl functions 


### Debug a Hubl variable 

``` 
     <pre> 
      {{rec_post|pprint}}
      </pre>
``` 




Get the latest 5 posts : 
``` 
{% set rec_posts = blog_recent_posts('default', 5) %}



  {% for rec_post in rec_posts %}


    <article>

    <img src="{{rec_post.postFeaturedImageIfEnabled}}">
    <div class="post-title">{{ rec_post.name }}</div>
    <p>{{rec_post.postSummary}}</p>

    </article>



  {% endfor %}

```


## HubDB Dynamic page ( Custom Post Type style )

Create a table with HubDB and be sure to put the "Enable creation of dynamic pages using row data" ON. 

Then Create a template in the theme, see in ``` src/templates/launchmonitors-single.html ```

The template will be in charge of displaying the archive page and the single with the following code : 

```
  {% if dynamic_page_hubdb_row %}        
    <h1>{{ dynamic_page_hubdb_row.hs_name }}</h1>
    <h2>{{ dynamic_page_hubdb_row.name }}</h2>
    <h3>{{ dynamic_page_hubdb_row.role }}</h3>
  {% elif dynamic_page_hubdb_table_id %}
    <ul>
    {% for row in hubdb_table_rows(dynamic_page_hubdb_table_id) %}
        <li><a href="{{ request.path }}/{{ row.hs_path }}">{{ row.hs_name }}</a></li>                
    {% endfor %}
    </ul>
  {% endif %}

```

**The page settings**
Then in the editor create a new page and in the settings go under Dynamic Pages then Data source and pick the hubDB table. 
It's possible to set a slug too for that page, in my case I chose https://hs.antoinebrossault.com/launch-monitors.

See : https://recordit.co/TTLYLZvlj2 





## Dev 

Start the project with 

``` hs watch src my-theme ```

For a first start use :

``` hs watch src my-theme --initial-upload  ```

This will upload the local modification automaticly

To preview the modifications :

Go on ``` https://app.hubspot.com/website/20783552/pages/site ``` and create a page.

The local modification will appear automatilcy in the page editor.


Preview the modification  : https://recordit.co/yAiS2TxRPn


## Admin interface 

### Options 

There's an option / setting panel for the pages : 

```
https://app.hubspot.com/settings/20783552/website/pages/all-domains/page-templates
```

See how to access : https://recordit.co/M00tMHBd3i

### Connect a custom domain, redirect, sitemap... htaccess like 

Everything happens in the otpions under Website > Domains & Urls. 
See the gif : https://recordit.co/kpyMlROTpt

### Manage users and roles 

https://app.hubspot.com/settings/20783552/users



