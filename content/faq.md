+++
date = "2017-06-15T21:58:26+02:00"
title = "Frequently Asked Questions"
description = "Hexya's most frequently asked questions"
+++
# General

## What is Hexya exactly?

Hexya is both a business application development framework and an ERP built with this
framework. See also our [Design](/docs/design/) documentation page.

## What is the target audience?

With its modular and extensible approach, Hexya can address the need of all companies,
whatever their business. 

Because an ERP involves many aspects from software development to change management through
business process assessment, we believe that ERP projects should be led by IT specialists. 
That is why specific care has been taken in Hexya in order to ease ERP integrators operation,
especially in the coding API.

## Is Hexya a fork of Odoo?

Hexya is highly inspired from Odoo and plans to have almost all Odoo business modules ported.
However, it is not stricly a fork since Hexya is written in Go where Odoo is in Python. See
it rather like a complete rewrite from scratch, but with the
[same ideas in mind](/docs/design/).

# License

## What is the license of Hexya?

Hexya components are distributed under three open source licenses:

* The core Hexya framework is distributed under the Apache License 2.0.
* Hexya Base modules, which mainly includes the web client is licensed under the GNU LGPL.
* All other official modules are licensed under the GNU AGPL.

Do check the license of any community module that you include in your application.

## Why are Hexya components distributed under three different licenses?

Hexya is both an application development framework and an ERP built on top of it. To 
ease mass adoption of the framework, we released it under a rather permissive license:
The Apache License 2.0. 

The base modules, especially the web client include third party code that is already 
licensed under the GNU LGPL, so we kept this license.

Finally, to protect our work on business code that we consider the one with the most 
added value, we release it under the GNU AGPL.

## Will Hexya remain open source ?

Yes. Hexya will always be 100% open source.

See below.

## What is your business model ?

Like any other company, we need to earn money for living and this is particularly crucial for open source companies.

There mainly to business models for open source companies:

- Open core model: the core of the software is open source and advanced features are proprietary.
- Professional services model: the software is fully opensource and professional services are available for a fee.

Most open source companies developing great products go towards open core products, and we, as a software business company, understand this very well.

However, in the ERP world, we believe that the added value towards our customers is at least two thirds in what is called "ERP integration", that is:

- Analysing the customer's business processes
- Finding the compromise between modifiying company processes to match the ERP and adapting the ERP to the company processes
- Developing customization modules
- User training and change management
- Managing the project of setting up an ERP

And we see that customer companies are quite willing to pay for this services that give them instant return on investment.
On the other hand, customers are often reluctant to pay for license fees that are seen as the "editor's tax". 

That is why we believe that we should develop the software open source to benefit from the community and make our money on ERP integration.

## Can I create and distribute proprietary modules of Hexya?

NO. 

This only exception to this rule is if your module only depends on the modules in the
`hexya-base` repository which are LGPL, and that you do not modify these modules 
(following the terms of the GNU LGPL). 

## I'm a service company and my customer insists to have exclusivity on the code I write for him. Does it mean I cannot work with Hexya?

Yes you can work with Hexya. Remember that the GNU AGPL license forces you to license 
your customer with the GNU AGPL, but does not force you to license everybody nor to disclose
your code to people you did not give a license to.

In other words, you can give "exclusivity" of your code to your customer, but you cannot
forbid him to release it himself (nor forbid to modify, redistribute, etc. as per the GNU AGPL).
