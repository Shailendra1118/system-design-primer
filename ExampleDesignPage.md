1. Objective
2. References
3. Terminologies
4. Functional requirements overview
  4.1 Template Management
  4.2 Template resolution for PDF
5. UX designs
6. Dependencies
7. Consumers
8. Backend Implementation
  8.1 Components
  8.2 Design
    8.2.1. Data model
    8.2.2 Workflows
      8.2.2.1 Fetch template list and template details
        8.2.2.1.1 Fetching the list of templates
        8.2.2.1.2 Mapping status for template
      8.2.2.2 Copying templates
      8.2.2.3 Publishing templates
      8.2.2.4 Assigning segments to templates
      8.2.2.5 Removing segments from templates
      8.2.2.6 Defining priority for the templates
    8.2.3 API Specifications
9. DB change logs to support segment feature + backward compatibility
10. Configurability and extensibility
11. Security
12. Performance and scalability
13. Reliability and fault tolerance
14. Testability

## 1. Objective
Allow MM to define different invoice templates depending on the customer groups (or segments)


## 2. References
<Ticket #> - New functionality for Product


## 3. Terminologies
Read-only template: OOTB template provided by the product which is by default active once feature is enabled. This templates cannot be edited.

Default template: Any active template without any segments associated with it. Can be read only or customised. Used as a fallback template for any given locale when customised template for that locale or for a segment is not defined

Segmented template: Template (active / inactive) having segments associated with it.

Published template: Template which is active for a given locale (and/or segments) PDFs are available in this template format once the invoices are generated


## 4. Functional requirements overview
Assigning segments to a template is possible only if segments are enabled for a MP. 


4.1 Template Management
Read only template cannot be assigned any segments

Default template cannot be assigned any segments.

Templates can be copied with or without segments

Segments can be assigned to any active or inactive templates (except read only and default)

Segments can be assigned or removed from a published template. 

Segments are always associated with the published version of json for any template.

It is possible to remove all segments for a published template-

Manually remove all and save: This will make the template to be in inactive state

Assign this template to all customers of that locale: This will make the current template as a default template

One segment cannot be associated to two different published templates

Published templates in any locale have the priority assigned to them (1 being highest). Default templates always have the lowest priority

If user belongs to multiple segments, then the template is chosen according to the priority.

If there is no template defined for a particular segment, then default (read only or customised) template is used. 


4.2 Template resolution for PDF
Identify company id of the user from invoice data

Identify segments associated with the customer of the invoice

Generate PDF for the invoice in expected format.


5. UX designs
<URLs>


6. Dependencies
AppConfigr flags to enable segment feature on MP

Segment MS

To fetch segments created in MP

To listen to various events for segments (create, update, delete)

To fetch the segments associated with any user 


7. Consumers
UI: For template management

Backend service : For PDF generation using applicable template for a user


8. Backend Implementation

8.1 Components
Configration MS: Required for template management

Main MS: Required to generate PDF using the applicable template for user


8.2 Design
This section describes the design and implementation changes required to support segment feature for the product. 
Any changes or new implementation should take care of the fact that if in any case, segment feature is disabled for a MP, template management and resolution will take place only on the basis of locale.


8.2.1. Data model
a) template collection
1) Removed status field. Status will be resolved on the fly using the data from published_template collection for read only templates and using version statuses for user defined templates.
2) Added 2 new fields -
segmentIds: An array field consisting of segment idds associated with the template
isDefault: Identify if the template is default


b) published_template
Added isPublished, segmentId and priority fields to resolve the template when MP supports segment functionality


 


8.2.2 Workflows

8.2.2.1 Fetch template list and template details
Displaying the list of templates on the landing page will differ depending on the value of flags for segment feature. It will also take care of the scenario when segments are assigned to some templates and segment flag is disabled afterwards.


8.2.2.1.1 Fetching the list of templates
a) If segment feature is disabled

Fetch all templates for required tenant, client and scope where segmentIds is null or an empty array. Group the templates by locale.

b) If segment feature is enabled

Fetch all templates for required tenant, client and scope. Group the templates by locale.


8.2.2.1.2 Mapping status for template
if read-only template
  find the latest published template for required tenant, client, scope, locale where segmentId is null
    if empty, set the template status to ACTIVE
    else, set the template status to DEACTIVATED
else
  if size of array of versions is 1 and status is MODIFIED, set the template status as INACTIVE
  else if versions contain an entry with status PUBLISHED, set the template status to ACTIVE
  else set the template status to DEACTIVATED
  

8.2.2.2 Copying templates
Create a new template with a modified version of json same as the modified version of the original template. Copy the segment associations if copySegments flag is true. Set the values for isDefault and activatedAtLeastOnce fields of this template to false.


8.2.2.3 Publishing templates

8.2.2.4 Assigning segments to templates

8.2.2.5 Removing segments from templates

8.2.2.6 Defining priority for the templates

8.2.3 API Specifications

9. DB change logs to support segment feature + backward compatibility
Need to set the isPublished field in published_template collection for existing active templates to true and others to false.
Add a change log →  get templateIds and corresponding versionIds from template collection whose status is active. Set isPublished field to true for these templates in published_template collection.
(Note: Need to revisit if this change log is actually required)

Add a change log to set isDefault to true in template collection where status is active and template is not a read only template


10. Configurability and extensibility 
Segment associations can be managed on Invoice Builder side by consuming different events published by segment MS. In this way, if segments are maintained at Invoice Builder side, there will be less number of requests calling the external apis.

TODO: add design and implementation workflow for event listener changes


11. Security
pis are secured with fine grained roles like ROLE_X and ROLE_Y

PDF generation api is secured with Role X and can be access only by a MS


12. Performance and scalability
Template management apis should have an SLA of 2s

API to resolve the active template for user needs to be fast as it will be called while generating PDF for the user (which happens once the large amount of invoices are generated during the billing run). For this, we can create an index on published_template collection.


13. Reliability and fault tolerance
This feature has an external dependency on the segment MS. The default timeout of 2s has been configured for it. All the external calls will be retried for 2 times and even after that we do not receive any response, an error will be returned to the consumers of this feature


14. Testability
Unit tests and integration tests will be part of the respective microservices

Functional test cases: <URL>
