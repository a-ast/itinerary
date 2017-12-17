Audience: developers / all proficiency levels 

Idea is to show Akeneo architecture and how PIM benefits of using Symfony as an underlying framework.

Main points:
* architecture: components, bundles, best practices.
* points of extensions in Akeneo: tagged services (mostly), custom bundles.
* in general: short learning curve (only for developers with Symfony expirience though).


## Architecture


## Extending Akeneo

Events

Tagged services


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


