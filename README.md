# IMAPEX - UCS Director

Cisco UCS Director abstracts hardware and software into programmable tasks that are assembled together to provision infrastructure across computing, networking, and storage resources that reside on multiple hyper-visors. 

## UCS Director Workflows

##### **Importing**
Workflows are stored in the system database and are maintained outside of the product by importing and exporting them.  Although they are packed in a binary format this makes them easily portable and shared on communities.
##### **version management**
Workflows may be managed by an internal versioning system stored in the database.  This allows you 
##### **Rollback**
One of the most powerful components of UCS Director is its ability to maintain and manage the elements it automates.  Users take action on those elements by executing service requests which in turn calls workflows.  UCS Director inherently supports the ability to rollback a workflow and it is best practice to always include this.

## UCS Director Custom Tasks
A task is a single action or operation with inputs and outputs.  Use these to create repeatable atomic units of work.  Anytime you find yourself writing cloudpia script consider if it would be better placed in a custom task.<BR>
 Refer to the Custom Task Getting started guide.<BR> <http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/ucs-director/custom-task-getting-started-guide/5-5/b_UCSDirector_Custom_Task_Getting_Started_Guide_55.html>
 
 Example Custom Task and report integration

```
importPackage(java.lang);
importPackage(java.util);
importPackage(com.cloupia.lib.util.managedreports);
 
function getReport(reportContext, reportName)
{
     var report = null;
      try  {
             report = ctxt.getAPI().getConfigTableReport(reportContext, reportName);
      } catch(e) {
      }
 
      if (report == null)
      {
             return ctxt.getAPI().getTabularReport(reportName, reportContext);
      } else
     {
           var source = report.getSourceReport();
           return ctxt.getAPI().getTabularReport(source, reportContext);
     }
}
 
function getReportView(reportContext, reportName)
{
      var report = getReport(reportContext, reportName);
 
     if (report == null)
     {
           logger.addError("No such report exists for the specified context "+reportName);
 
           return null;
     }
 
     return new TableView(report);
}
 
// following are only sample values and need to be modified based on actual UCSM account name
var ucsmAccountName = "ucs-account-1";

// repot name is obtained from Repot Meta data 
var reportName = "TABULAR_REPORT_VMS_PAGINATED_CONFIG_REPORT";
 
var repContext = util.createContext("global", null, null);

var report = getReportView(repContext, reportName);
 
// Get only the rows for which our user is part of
report = report.filterRowsByColumn("Assigned To User", input.UserID, false);

var matchingIds = [];
var count = 0;
 
logger.addInfo("report has this many rows: "+report.rowCount());

for (var i=0; i<report.rowCount(); i++)
{
     logger.addInfo("User "+report.getColumnValue(i,"Assigned To User")+" is assigned to VM "+report.getColumnValue(i,"VM Name"));
	count++;
}

output.VMsAssigned =  count
```

## Developer Integration
####Administration --> Downloads
##### **Rest API SDK**<BR>
UCS Director REST API SDK Bundle is part of the UCS Director REST API. In addition to documentation, such as Cookbook, the SDK Bundle provides examples that you can use with the REST API. These examples include test cases and sample code that demonstrates the use of the SDK classes. 
##### **PowerShell Console**<BR>
UCS Director PowerShell Console provides cmdlet wrappers for the JSON-based APIs. Each cmdlet performs a single operation. The cmdlets are executed in a Microsoft Windows server. Depending on the data returned by the JSON-based APIs, the cmdlets automatically interpret the data and convert it into Windows PowerShell objects. You can chain multiple cmdlets together. After installed to view a list of available cmdlets, execute 'Get-Command'.
##### **Open Automation SDK**<BR>
The platform architecture consists of two components: the Cisco UCS Director Platform Runtime and the set of modules that get executed on the platform. The platform provides the required APIs and management functionality so that the modules can perform their intended functions. The modules provide the necessary intelligence and features. The platform provides a loosely-coupled plug-in architecture where modules can be developed, deployed and executed against the platform.  Using tools such as UCS Director Open Automation and the SDK, you can develop and deploy a module that can be executed on the Cisco UCS Director platform runtime.
##### **Custom Tasks Scripts Samples**<BR>
A long list of example Custom Tasks as well as supporting documentation.

## The Developer Experience
Because UCS Director is a coding platform itself it is important to understand the differences between what the platform does internally and how you as a develop can interface with it.

The base code of UCS Director runs on java and is extended through Cloupia libraries.  The main interface the user is going to have inside the system is through javascript.  All custom tasks are supported through javascript only.  To break outside this the user can utilize java inherently inside javascript but you will still have to abide by the rules of the javascript server.

Javascript can't do everything and sometimes accomplishing tasks inside of UCS Director needs to be done with a different approach.  If a windows based task is needed consider using the Powershell agent as this is a swiss army knife plugin that allows the user to interface and accomplish anything that can be scripted against windows or other vendors that provide powershell functions.

If the need is to interface with Linux based systems we can ssh into a Linux server and write custom scripts there.  

## Logging

#### Administration --> Support Information

 - Show log
 - Debug Logging
 - API Logging

##  REST API
####Admin (upper right) --> Advanced

Explain UCSD Rest API here, setting it up, API Key

Call a UCSD workflow from Python - <https://communities.cisco.com/docs/DOC-56724> 

##  REST API Browser


##  Communities

Explain UCSD communites

**Workflow Index**
https://communities.cisco.com/docs/DOC-56419

**Task Index**

## Some Important Examlpe Worflows
+ Calling a workflow from a workflow - https://communities.cisco.com/docs/DOC-55973

+ Calling a REST API from a task

## Open Automation

Explain open automation here

## Derivatives

Outside of the main product suite, UCS Director comes in different forms.

##### **UCSD Express (Hadoop)** - <http://www.cisco.com/c/en/us/products/servers-unified-computing/ucs-director-express-big-data/index.html>
 <p>Cisco UCS Director Express for Big Data provides a single-touch solution that automates Hadoop deployment on the Cisco UCS Common Platform Architecture (CPA) for Big Data infrastructure. It also provides a single management pane across both physical infrastructure and Hadoop software. All elements of the infrastructure are handled automatically with little user input.</P>
##### **UCSD ICF Plugin (Inter Cloud)**
<p></P> 
##### **UCSD VACS Plugin (Containers on Steroids)** 
<p>A serpately licensed product inside UCS Director to logically isolate virtual application workloads at the virtual layer.</P> 
##### **UCSD IMC Supervisor** - <http://www.cisco.com/c/en/us/support/servers-unified-computing/integrated-management-controller-imc-supervisor/tsd-products-support-series-home.html>
<p>Management Controller (IMC) Supervisor enables centralized management for standalone Cisco UCS® C-Series Rack Servers and E-Series Servers located across one or more sites.  Everything that can be done IMC Supervisor can be done in UCSD.  This</P> 
 
 

 
 

## Further Help
UCSD Coding Mailer - <ask-ucsd-api@cisco.com>

## Further Reading

UCS Director Fundamentals Guide:<BR>
http://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/ucs-director/fundamentals-guide/5-4/b_UCS_Director_Fundamentals_Guide_54.html

Programing Guides<BR>
<http://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-director/products-programming-reference-guides-list.html>

All Configuration Guides<BR>
<http://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-director/products-installation-and-configuration-guides-list.html>




