---
layout: post
title: "Analysis Services: service refuses to start with XML parsing error"
date: 2015-04-30 21:00:48 -0600
comments: true
categories: 
- Analytics
tags:
- ssas
---
## The problem
The SQL Server Analysis Services (SSAS) service cannot be started and Event Viewer lists the following error:

>The service cannot be started: XML parsing failed at line 1, column 8193: Illegal qualified name character.

## Investigating
After doing some searches for the above error, I came upon exactly one post detailing something similar.  That [post on Teo Lachev's blog](http://prologika.com/CS/blogs/blog/archive/2011/04/23/analysis-services-refusing-to-start.aspx) listed an error involving an 'Illegal xml character' and a different column than the error here, but it was close enough to be worth investigating.  He mentioned his error was caused by a corrupt master.vmp file in `C:\Program Files\Microsoft SQL Server\<SSAS Version>\OLAP\Data`.  For those like me who have no idea what master.vmp does, luckily some [other people on the Internet](http://blogs.msdn.com/b/sqlserverfaq/archive/2009/07/08/tampering-master-vmp-file-may-result-in-losing-all-analysis-services-databases.aspx) do.  From that article:

>"The master.vmp file is the master version map that contains the GUIDS for all of the objects and the version of each object that currently exists on the server."

I could see how a corrupted version of that file would make it hard for the SSAS service to start.  To double check that this was the issue, I took a cursory glance at the master.vmp file.  Even scrolling through the file quickly I was able to spot the problem.  Can you see anything that looks off here?

![The corrupt master.vmp file][1]

Good work Windows, you stuffed some output right in the middle of an XML tag.  But at least this told me it was most likely the problem.

The posts above mentioned two ways to fix this 1) copy the master.vmp file from another SSAS instance or 2) delete the master.vmp file.  Deleting master.vmp and starting the service causes master.vmp to be regenerated but will also wipe out all objects in the Data directory.  Since this error occured on a dev box, I had no problems with the objects being wiped so we simply deleted master.vmp and re-deployed the Analysis Services database.  If this is not an option you can try copying master.vmp from another SSAS instance, but keep in mind that any objects that do not have GUIDs in this copied master.vmp file will be deleted when the SSAS service starts.

## The solution
Delete master.vmp from `C:\Program Files\Microsoft SQL Server\<SSAS Version>\OLAP\Data` and start the SSAS service.  **This will wipe out all objects in the Data directory**, so this may not be the best course of action in a production environment.

[1]: http://res.cloudinary.com/biskillpoints/image/upload/v1435363656/ssas-master-vmp-corrupt_jmcuz9.jpg