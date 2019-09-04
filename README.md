# App for Web Proxies

## Deprecation Warning!!!
__This app has been deprecated. There will be no new development or bug fixes. It has been replaced by the [Aplura Web Proxies App for Splunk](https://splunkbase.splunk.com/app/4231/).__

## Overview
In many organizations, web proxies separate users from the Web at large. User web activity can often be a good indicator of possible compromise, phishing attempts, abuse, and outdated software. This app provides Splunk dashboards, forms, and reports which can be used to explore your web proxy events, and make sense of what can often be a large volume of data.

To do this, the app relies on the Splunk Common Information Model (CIM) for Web events. This means that the app can report on any web proxy data, as long as it has been on-boarded properly, and is available through the Web data model.

## A note on Splunk Data Model Acceleration and Disk Space
This app requires data model acceleration, which will use additional disk space. If you are using the Splunk App for Enterprise Security, this is already enabled, and should have been factored into your retention policies. If not, you should review the documentation on data model acceleration, how it uses disk space, and how to plan for it. This documentation can be found here: [Data Model Summary Size On Disk](http://docs.splunk.com/Documentation/Splunk/latest/Knowledge/Acceleratedatamodels#Data_model_summary_size_on_disk).

## A note on the Splunk Common Information Model
As mentioned above, the app uses the CIM for web events. The CIM allows you to take events from a number of sources or products, and report on them in one cohesive manner, using a common set of names for fields and event types.

## Available Dashboards and Forms
These are the dashboards and forms currently present in the app:

### Proxy Traffic Overview
This dashboard serves as a jumping-off point for exploring your web proxy data. It includes panels for traffic over time, categories, top talkers, and users. Clicking on panels in this dashboard will drill down to the appropriate profile page for further exploration.

### User Profile
This form provides panels based on a user's activity, including source IP addresses, category history, and destinations. By focusing on a particular user, you can see browsing activity, generate a report for HR, or see where a user may have become infected.

### Source Profile
This form provides panels based on source IP addresses, often the user's computer or servers. This includes activity over time, categories, users, user agents, and destinations. Use this form when you need to see what sites a particular IP has been visiting, which is useful if you find an un-authenticated session which is attempting to visit different sites.

### Category Profile
This form provides panels based on a category. This includes requests over time, destinations, users, and sources. If particular categories are of concern for your organization, you can use this form to find users or systems which have visited the category.

### Destination Profile
This form provides panels based on destination, which may be a hostname or IP address. This includes URLs, categories, users, and sources. Using this form allows you to see what users visited a particular site, and which URLs are popular with your users. On sites which are categorized based on different URLs, you can see all of the categories for visits to the destination.If you know of a compromised site (such as a watering hole attack), you can use this form to quickly find users and systems which may have visited it.

### Policy Exceptions
This dashboard shows users going to "bad" categories, showing the categories and user activity. Note that the selection of "bad" categories, which often changes from organization to organization, can be customized by following the steps in the section titled "Customizing Categories for Policy Exceptions". Using this dashboard can highlight users which may be violating your organization's usage agreement or systems which may be compromised.

### User Agents
This dashboard provides analysis of the user-agent strings present in the web proxy events. To do this, the dashboard searches use a lookup which parses the user-agent strings. There are other lookups which perform this function, but TA-user-agents is used by default. For information on customizing the lookup being used, see the section titled "Customizing user-agent lookups". Using this dashboard will help you find outdated or unauthorized browsers/software in your environment. Additionally, because some malware either modifies user-agent strings, or have their own, you may find compromised systems on your network.

### Proxy Event Search
This form provides an easy way to search proxy events for particular activity.If you already know what you are looking for, this form makes it easy to generate a search that return the events you need. Don't forget that you can use the "search" icon at the bottom of the events panel to open the search in the Splunk search interface.

### About
A simple HTML version of this document.

## Prerequisites

### Splunk Versions
This app has been tested with Splunk versions 6.0.x, 6.1.x, and 6.2.x. This app should be installed on the same search head on which the web data model has been accelerated.

### Splunk Common Information Model Add-on
This app depends on data models included in the Splunk Common Information Model Add-on, specifically the "Web" data model. Information on installing and using the Splunk Common Information Model Add-on can be found here: [Common Information Model Docs](http://docs.splunk.com/Documentation/CIM/latest/User/Install). Information on configuring the acceleration on the data model can be found here: [Enable Data Model Acceleration](http://docs.splunk.com/Documentation/Splunk/latest/Knowledge/Acceleratedatamodels#Enable_persistent_acceleration_for_a_data_model).

The Splunk Common Information Model Add-on can be downloaded from here: [Splunkbase](https://apps.splunk.com/app/1621/).

This app has been tested with versions 3, 3.1, and 4.1 of the CIM add-on.

### Data model Acceleration on the Web data model
In order to make the app respond and load quickly, accelerated data models are used to provide summary data. For this data to be available, the Web data model must be accelerated. Information on how to enable acceleration for the Web data model can be found here: [Enable Data Model Acceleration](http://docs.splunk.com/Documentation/Splunk/latest/Knowledge/Managedatamodels#Enable_data_model_acceleration).

### User-agents Add-on
User-agent strings are very difficult to parse efficiently using regular expressions. To make this easier and faster, an external lookup is used. Be default, the TA-user-agents is used, as it requires no additional data download or internet access. TA-user-agents can be downloaded from here:  [TA-user-agents](https://apps.splunk.com/app/1843/).

The user-agent lookup being used can be customized. See the section titled  "Customizing user-agent lookups" for more information.

## Installation
This app should be installed on a search head where the Web data model has been accelerated. More information on installing or upgrading Splunk apps can be found here: [Where to get more apps](http://docs.splunk.com/Documentation/Splunk/latest/Admin/Wheretogetmoreapps).

__Note__: This app will require a restart of Splunk.

## Simple Installation Process
1. Make sure the field extractions and tags on your proxy events are correct.
2. Install the Splunk Common Information Model Add-on (skip if you are installing on an ES search head).
3. Install the TA-user-agents Add-on.
4. Install the App for Web Proxies. Restart Splunk.
5. Enable accelerations on the Web data model (skip if you are installing on an ES search head).
6. Wait for the accelerations to start. After the acceleration searches have run, you should start seeing the dashboards populate.

## Customizing Categories for Policy Exceptions
Not all organizations are the same, so there is a good chance that the default "bad" categories will need to be customized to meet the needs and policies of your organization. Additionally, customizations may be required based on the type of web proxy device generating the events.

### Adjusting the lookup for the vendor and product
Different web proxy vendors is different category names, so one list wouldn't cover all vendors. To enable support for different vendors, a macro named `wfa_lookup` is used.

Currently this app comes pre-configured with lookups for 7 different products:

* Barracuda: `barracuda_wfa`
* Bluecoat (default): `bluecoat_wfa`
* Checkpoint: `checkpoint_wfa`
* Cisco WSA: `cisco_wsa_wfa`
* McAfee Web Gateway: `mcafee_wg_wfa`
* Palo Alto Firewalls: `palo_alto_wfa`
* URLBlacklist: `urlblacklist_wfa`
* Websense: `websense_wfa`

These lookup files can be found in `$SPLUNK_HOME/etc/apps/web_proxies/lookups`.

To configure the app to use the correct lookup for your data, edit the `wfa_lookup` macro to match the vendor from the list above. More information on editing Splunk macros can be found in the Splunk documentation: [Use Search Macros](http://docs.splunk.com/Documentation/Splunk/latest/Search/Usesearchmacros).

__Note__: While Splunk has configuration layering for configuration files, it does not have the same layering for lookup files. This means that if you customize one of these lookup files instead of creating your own, you need to make sure to back it up before upgrading this app, as it may be overwritten.

### Creating your own lookup, or customizing an existing one
Because your organizations policies may differ from what is represented in the provided lookups, or you may be using a product which is not listed above, you may wish to use your own custom lookup for Policy Exceptions. A custom lookup for Policy Exceptions should have two columns, "category" and "wfa". The category column should match the name of the category produced by your proxy device to which visits from clients are not allowed, or need to be reviewed. The second column, "wfa", should always have a value of "TRUE". You can always look at the existing lookups for an example. More information on create a lookup in Splunk can be found here: [Lookups](http://docs.splunk.com/Documentation/Splunk/latest/Knowledge/Addfieldsfromexternaldatasources).

Once you have created your lookup, edit the `wfa_lookup` macro with the name of the lookup you have created. Information on editing macros in Splunk can be found here: [User Search Macros](http://docs.splunk.com/Documentation/Splunk/latest/Search/Usesearchmacros).

### Using Wildcards in category lookups
The use of wildcards for the lookup file is not configured out of the box, however, it can by copying the `transforms.conf` file from the `/default` directory to the `/local` directory and adjusting the configuration of the `web_proxies_wfa` lookup. More information can be found here: [Can We Use Wild characters in lookup table](http://answers.splunk.com/answers/52580/can-we-use-wild-characters-in-lookup-table.html#answer-52581).

## Customizing user-agent lookups
By default, this app uses an external lookup to provide analytics on user-agent string. The default lookup used is the `TA-user-agents` lookup, however, this can be customized to use another lookup which may be more accurate or provide more fields for customizing the dashboard.

To customize the lookup, download and installed the other lookup. Copy the
`macros.conf` file from the `/default` directory of the `web_proxies` app to a `web_proxies/local` directory (which you may need to create). Edit the `macros.conf` file, and edit the `user_agent_lookup` macro to be the name of the custom lookup, and then edit the `ua_family_field` to match the high-level product name (for example "IE" or "Firefox"). The use of the macros expect that the field being used for the lookup will be the CIM-compliant `http_user_agent`. If the lookup is expecting something else, then the dashboard will need to be customized to match.

## About support for this app
Support for this app is provided on a best-effort basis. We have released this app for free, and want to help solve issues, and add features, but we also have day-jobs.

Need help? Use the Splunk community resources! I can be found on many of them:

* [Splunk Answers](https://answers.splunk.com/)
* [#splunk on Efnet IRC](https://wiki.splunk.com/Community:IRC)
* [Splunk Slack channel](http://splunk402.com/chat/)

The git repo for this app is located [here](https://github.com/automine/web_proxies).

## Changes

### v1.2.9
* Added deprecation warning and link to replacement.

### v1.2.8
* Fix typos in about.xml
* added "allow_old_summaries" to tstats macros. Defaults to "true"

### v1.2.7
* Corrected the search behind the distinct users per source on the Source Profile page (thanks, Thomas!)

### v1.2.6
* Certification changes
* removed linkView tags from XML views

### v1.2.5
* Added Checkpoint lookup
* Fixed issue with spaces in usernames
* Adjusted Palo Alto lookup

### v1.2.4
* Added more support into the the README for Splunk App Certification

### v1.2.3
* Fixed a typo in the websense lookup for waste fraud and abuse.

### v1.2.2
* Removal of OSX files for App Certification

### v1.2.1
* Typo fix in the README.md

### v1.2
* Reduced default search range to 24 hours (from 7 days) to prevent searching too much data
* Corrected metadata (permissions) to remove the local.met
* Added "About" page
* Added docs section "A note on Splunk Data Model Acceleration and Disk Space"
* Added more information about the individual forms, views, and dashboards

### v1.0
* Initial release

## References

### Splunk Common Information Model
* [Splunk Common Information Model Add-on Docs](<http://docs.splunk.com/Documentation/CIM/latest/User/Overview)
* [Splunk Common Information Model Add-on Web data model](http://http://docs.splunk.com/Documentation/CIM/latest/User/Web)

### Downloads
* [Splunk Common Information Model Add-on Download](https://apps.splunk.com/app/1621/)
* [TA-user-agents Add-on Download](https://apps.splunk.com/app/1843/)

### Web Proxy Categories
* [Barracuda Web Filter](https://techlib.barracuda.com/bwf/contentcategories)
* [Blue Coat](https://sitereview.bluecoat.com/categories.jsp)
* [Checkpoint](https://www.checkpoint.com/urlcat/categories.htm) (login required)
* [Cisco Web Security](http://www.cisco.com/c/en/us/products/collateral/security/web-security-appliance/datasheet_C78-718442.html)
* [McAfee Web Gateway](http://www.trustedsource.org/download/ts_wd_reference_guide.pdf)
* [Palo Alto](<https://urlfiltering.paloaltonetworks.com/CategoryList.aspx?AspxAutoDetectCookieSupport=1)
* [URLBlacklist](http://urlblacklist.com/?sec=download)
* [Websense](http://www.websense.com/content/websense-url-categories.aspx)

## Credits
This app was created by David Shpritz of [Aplura, LLC.] (http://www.aplura.com/consulting.html)

Thanks to other members of the Splunk Professional Services team, as well members of the [#splunk IRC on effnet](http://wiki.splunk.com/Community:IRC)

Icon made by Freepik from http://www.flaticon.com is licensed under CC BY 3.0.
