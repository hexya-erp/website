+++
title = "First Technology Preview"
description = "We are happy to present our first technology preview of Hexya"
date = "2018-01-28T10:00:00+02:00"
weight = 20
draft = false
+++

Our first technology preview of Hexya is online at [https://demo.hexya.io](https://demo.hexya.io) after almost two years of work.

It is a quick port of Odoo's sale module with its main dependencies on Hexya's framework.
The purpose of this preview is to show how several independent modules interact to build the whole application.
So do not expect business features to work seamlessly. 
In particular, the accounting module has only been scaffolded with all its models, views and menus, but none of the functions have been implemented.

Nevertheless, you can still try to create a few customers, some products and create a quotation or fiddle around with the different objects.

People who are familiar with Odoo are encouraged to have a look at the business code of the [product](https://github.com/hexya-erp/hexya-addons/tree/master/product) or the [sale](https://github.com/hexya-erp/hexya-addons/tree/master/sale) modules to have an insight of Hexya's API.
Each `.go` file is implementing the same models and methods as the corresponding `.py` file in Odoo v10. 

## What's next ?

We still have a lot of work on Hexya, both on the framework (see non exhaustive task list [here](https://github.com/hexya-erp/hexya/projects/1)) and on the business modules.
We are now looking for partners who are interested in helping us for Hexya's development.
