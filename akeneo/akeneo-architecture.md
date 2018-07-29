Audience: developers / all proficiency levels 

# Akeneo PIM architecture and extension points for developers

## Main points (plan)
* architecture: components, bundles, best practices.
* points of extensions in Akeneo: tagged services (mostly), custom bundles.
* in general: short learning curve (only for developers with Symfony expirience though).


## Intro

Product Information Management (PIM) systems are a class of software 
that enables the management and maintenance of product data.

Akeneo PIM is one of the leading open source enterprise solutions 
which helps companies to centralize their product information 
to enrich, translate and export it to multiple channels.

Akeneo is a web-based software platform that has user friendly UI and
plenty of features. 
However, PIM is most effective when fully customized and tailored 
to company specific needs. Fortunately for software developers, 
Akeneo is based on Symfony framework and inherits its powerful modularity 
and high flexibility.


## Architecture

While creators of platforms like Drupal, Spryker, Magento 2 preferred to use 
only some of Symfony components, Akeneo software architects decided to 
rely on the Symfony full-stack framework.
For developers who are going to customize Akeneo it 	
primarily means a well-organized application structure and a 
possibility to use bundle system.

Just like Symfony, Akeneo PIM introduces its own components.
/ that contain business or technical logic 
/ decoupled and reusable PHP libraries

Components are organized in 3 groups:
1. `Akeneo` - general-purpose components, 
1. `Pim` - components for the technical tasks related to product management,
1. `PimEnterprise` - components for extended product management available only in the Enterprise Edition.

Every component has corresponding bundles that contain the glue code to embed 
the component into application.


![alt text](image/architecture.png)

?entry poins: console, controllers?

## Extension points


### Tagged services

?Akeneo core developers carefully took care of extensibility and
used Registry pattern in order to allow external intergrators to
implement their own logic to be injected in Akeneo application.


What are tagged services.
What do you need to register.
What kinds of tagged services exist.
Example with dashboard.

Events

Extending entities??


Out-of-the box extension points

Reference data 


### What else?
Decorating services?


## Adding new functionality


Custom bundles using PIM services


## Akeneo extension points

* Events
* Tagged services: pim_enrich.mass_edit_action, 
* Decorating services
* Reference data
* custom action rule? (EE) https://docs.akeneo.com/latest/manipulate_pim_data/rule/index.html
* config: programmatically add new measures. metrics? https://docs.akeneo.com/latest/manipulate_pim_data/catalog_structure/how_to_add_a_custom_unit_of_measure.html
* Customize Product Assets (EE) https://docs.akeneo.com/latest/manipulate_pim_data/product_asset/index.html

* adding jobs?

Not recomended: extend entity
BUUUT: here how to extend: https://docs.akeneo.com/latest/manipulate_pim_data/category/add_new_properties_to_a_category.html
?


