+++
title = "Second Technology Preview"
description = "A lot of work has been achieved since our first preview"
weight = 20
draft = false
date = "2018-11-17T14:31:36+01:00"
+++
It's been a long time since we released our first technology preview back in January.
But actually, behind the scene, a lot of work has been done since then.

By playing with the new demo, you will certainly notice the new UI theme and a few improvements here and there.
But the main part of the work has been on the core of Hexya, which, sadly, cannot be seen on the demo itself.

Remember, Hexya is both a business application development framework and an ERP built on top of it.
But before we can actually built the ERP part with all the business code, we must be sure that our core is rock solid.

We have also worked on OTH, our Odoo-To-Hexya porting tool. 
We expect it to be mature enough to start real ports of business modules with it.

## The future

By now, we have approximately 90% of the core features we wanted,
that is almost all technical features that exist in Odoo. 

Our next big step will be to switch to Go modules to have a version and dependency management, which is crucial for a modular ERP like Hexya.
Each Hexya module will be a Go module and have its own git project to manage its dependencies. 

Our work will now focus on :

- Finishing Hexya core:
    - Implementing a job queue with cron scheduler
    - Improve database schema synchronization, especially on updates
- Porting Odoo modules to Hexya
