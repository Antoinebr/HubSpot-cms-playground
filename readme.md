

## Add an image from the template 

For a page with the path : ``` src/templates/ab-landing-page.html ```

```html 
<img src="{{get_asset_url('../images/fs-trackman.jpg')}}" height="200" class=" block m-auto w-8/12">
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

Create a table with HubDB and be sure to put the "Enable creation of dynbamic pages using row data" ON. 

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



