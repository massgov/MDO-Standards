---
output: pdf_document
---
# Massachusetts Data Office Standards - DRAFT
## Version 0.1

### Table of Contents
1. Tools
    * Compute - CM
    * Storage - KM
    * AWS Management - KM
    * Identity Management - CM
2. Code
    * R - CM
    * Python - KM
    * Directory Structure
    * Unit Tests
    * Code Reviews
    * Dependency Management
    * Documentation
3. Process
    * Writing Style
    * Plotting
    * Presentations
    * Requesting Additional Resources
    * Productivity and Communication
    * Data Storage and Versioning - KM

### Tools

#### Compute

This section will discuss MDO standards with respect to compute resources, both internal and external.
The following should be viewed as a rough guideline for compute resources to be used at each phase of
any analytics project:

|  Phase  | Compute Resources                                                                          |
|---------|--------------------------------------------------------------------------------------------|
| Acquire | Laptop/desktop                                                                             |
| Munge   | If data can fit in local memory, laptop/desktop. Else, offsite resource.                   |
| Model   | Dependent on model complexity. Strive for models which can be fit on small, local samples. |
| Validate| Offsite resource, freeing local resources.                                                 |
| Deploy  | Offsite resource.                                                                          |

Regardless, members of the MDO should strive to keep as much compute work local as possible, both for
security and cost savings.

Here is a list, in no particular order, of offsite compute resources members of the MDO should
leverage when necessary.

###### AWS EC2
Amazon Web Services' EC2 instances are the preferred medium for offsite compute resources, both from an
access and interoperability standpoint. You can find a guide on EC2 basics [here](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) and a tool which
will allow you to search for compute instances and compare costs given project requirements, [here](http://www.ec2instances.info/).
The MDO standard with respect to OS is Ubuntu for all EC2 instances, unless otherwise required.

* Connecting to an Instance

Connecting to any EC2 instance should be done via ssh tunnel using a locally generated private-public
key pair. See the section on ssh within identity management for more information on key pairs and how
to create them.
[comment]: # (may need to update if we do vpc option)

* Efficient Use and Power v. Cost

It is important to efficiently use the compute resources while they are being rented. Therefore,
whenever possible, code should be written in such a way to reduce processing time and minimize idle
resources by leveraging parallelism, more efficient code in other packages, and other tools which
optimize code, such as [cython](https://github.com/cython/cython).

#### Storage

The priorities for the Massachusetts Data Office for data storage are security, internal accessibility, stability and cost. These priorities can shift based on the project duration, staffing and sensitivity but all four are still the main concerns.

 * Security

 Storage should be easy to secure by changing access controls. The data should be protected with modern security features and robust authentication for access. It also will conform to all legal standards that might accompany the data such as FERPA or HIPAA. See below for specifics on authentication and access controls.

 * Internal Accessibility

 All the data used in storage must be easily accessible by members of the data team. This means that all work will be done on the same standard data and any changes will be available to all members of the team as discussed in the data storage section. This access should be as fast as the data size allows and work with multiple analysis platforms. This also covers the ease of starting up a new storage location, which ideally is instantaneous.

 * Stability

 The data must be stored in a place that is reliably accessible and stable.

 * Cost

 The team should always attempt to minimize costs while still meeting all other requirements for completing the project successfully and quickly. This includes doing due diligence on internal and external options for storage.

##### Primary Storage Options

All storage options will be set up in the format spelled out in data storage section for easy portalbility of code between team members and platforms.

* S3/Amazon RDS/Other Cloud Services

Amazon's S3 product is the preferred storage location for all shared data used by the data team due to its format agnostic storage. It also provides internet access that can be secured with unique keys. The data is secured and connections are encrpyted. If the data is better stored in a database an Amazon (or other brand) RDS instance can be used that purpose with a similar process to the S3 instance.

This is our default storage location for project and a new bucket is created for each project.

As the team explores other cloud-based options other systems might take precendence.

* Agency servers

Agencies might not be able to provide data directly and will only give access to their servers. If this is the case we prefer a dedicated directory and shared access credentials so the team can access the same data. This also may require permissions from MassIT to get around the firewall to access the agencies server.

* Local

Data can be locally stored for temporary processes but for any process that will be shared the data used should be backed up in a location where all other team members can access it. Each project bucket will have a personal folder for individual's branched work.

* MassIT servers

Certain specialized servers including RServers could be requested from MITC. For MassIT specific projects such as Mass.gov, data is already stored there and the servers can then be treated as an agency server.



##### Storage Security

All access to cloud and server storage will be controlled with public and private keys. These will either be keys set up from agencies or MassIT or credentials supplied by the cloud vendor. Helpful information on setting up an ssh config file: http://www.howtogeek.com/75007/stupid-geek-tricks-use-your-ssh-config-file-to-create-aliases-for-hosts/

S3 security assumes that all buckets are set to only be accessed by holders of a MassIT AWS account that will be verified by credentials stored locally or on each call. More information on this will be added as the security system is tested.

#### AWS Management

##### Structure

* S3

Each S3 bucket will be named with the following convention PROJECT_NAME.data.digital.mass.gov and will be seeded with a permissions file described below and with the file structure specified in data storage. Each bucket will also be tagged with a tag (data-office:project) NEED THIS for billing purposes.

Tags:

Finance - {"owning_project":project_name}

* EC2

There will be shared EC2 instances to run processes and scripts on large files. These instances will be set up with the appropriate packages and libraries installed to run scripts.

EC2 instances that are supporting dashboards or a web interface will be

Individuals can also set up their own instances for one-off tests

Over time the team will create or find an AMI with all the tools and libraries pre-installed to satisfy internal needs.

Tags:

Finance - {"owning_project":project_name}
Ownership - {"team_owner":owner_name}
Language - {"language":language} - which libraries have been prepared



##### Security

By default all the data office's work will be done in a single VPC (virtual private cloud). This cloud will allow team members to run various instances within the cloud and have them all talk to each other securely without any access permitted from external servers. This might require a "jumpbox" setup on a small EC2 instance which serves as a intermediary that team members will use to access database instances on the cloud.

* S3

The S3 buckets will all be set up with a common security policy in JSON form that will only allow members of the data team access to read and write from the bucket. These permissions can be changed on a case by base if certain data needs to be shared outside of the team. The policy will be written as follows :{policy:WE NEED TO COME UP WITH A POLICY!}

* EC2

THIS IS SOMETHING WE'LL NEED TO IRON OUT
Each EC2 instance is secured with a specific .pem file. Individual's instances will store their .pem keys locally which collective ones will be stored on an EC2 instance in the VPC that each user will have the .pem that accesses that server and that server only.

### Code Standards


=======
### Code

#### Python

The data office will conform to PEP8 standards found [here](https://www.python.org/dev/peps/pep-0008/). These standards can be tested locally by team members using the package [pep8](http://pep8.readthedocs.io/en/release-1.7.x/index.html).

#### R Style Guide

##### Summary: R Style Rules

1. File Names: end in .R
2. Identifiers: `variable.name`, `FunctionName` (or `functionName`), `kConstantName`
3. Line Length: maximum 80 characters
4. Indentation: two spaces, no tabs
5. Spacing
6. Curly Braces: first on same line, last on own line
7. `else`: Surround `else` with braces
8. Assignment: use `<-`, not `=`
9. Semicolons: dont use them
10. General Layout and Ordering
11. Commenting Guidelines: all comments begin with # followed by a space; inline comments need two spaces before the #
12. Function Definitions and Calls
13. Function Documentation
14. Example Function
15. TODO Style: TODO(username)

##### Summary: R Language Rules

1. attach: avoid using it
2. Functions: errors should be raised using stop()
3. Objects and Methods: avoid S4 objects and methods when possible; never mix S3 and S4
4. Notation and Naming
5. use library() for sourcing packages from your library, never use require()

##### File Names

File names should end in .R and, of course,
be meaningful.

GOOD: `predict_ad_revenue.R`
BAD: `foo.R`

###### Identifiers

Don't use underscores ( _ ) or hyphens ( - ) in identifiers. Identifiers should be named according to the following conventions.

The preferred form for variable names is all lower case letters and words separated with dots (variable.name)

GOOD: `avg.clicks`
BAD: `avg_Clicks`

Function names should be descriptive wrt the functions utility and follow a camel case format. 

GOOD: `calculateAvgClicks`
BAD: `calculate_avg_clicks`

Make function names verbs.

Exception: When creating a classed object, the function name (constructor) and class should match (e.g., lm).

Constants are named like functions but with an initial k

GOOD: `kConstantName`
BAD: `i, j, k etc...`

##### Syntax

* Line Length

The maximum line length is 80 characters. Use tools such as break lines to identify where this is positionally.
It is all right to stray from this standard if doing so significantly affects how comprehensible a given block of code
is.

* Indentation

When indenting your code, use two spaces. Never mix tabs and spaces. Exceptions exist for legacy code or agency code
which utilizes tabs.

Exception: When a line break occurs inside parentheses, align the wrapped line with the first character inside the
parenthesis.

GOOD:
```
someFuntion(arg1,
            arg2)
```

BAD:
```
someFuntion(arg1,
  arg2)
```

* Spacing

Place spaces around all binary operators (`=, +, -, <-, ==, /, *, ^`).

Do not place a space before a comma, but always place one after a comma.

GOOD:
```
tab.prior <- table(df[df$days.from.opt < 0, "campaign.id"])
total <- sum(x[, 1])
total <- sum(x[1, ])
```

BAD:
```
tab.prior <- table(df[df$days.from.opt<0, "campaign.id"])  # Needs spaces around '<'
tab.prior <- table(df[df$days.from.opt < 0,"campaign.id"])  # Needs a space after the comma
tab.prior<- table(df[df$days.from.opt < 0, "campaign.id"])  # Needs a space before <-
tab.prior<-table(df[df$days.from.opt < 0, "campaign.id"])  # Needs spaces around <-
total <- sum(x[,1])  # Needs a space after the comma
total <- sum(x[ ,1])  # Needs a space after the comma, not before
```

Place a space before left parenthesis, except in a function call.

GOOD: `if (debug)`
BAD: `if(debug)`

Extra spacing (i.e., more than one space in a row) is okay if it improves alignment of equals signs or arrows (<-).

```
plot(x    = x.coord,
     y    = data.mat[, MakeColName(metric, ptiles[1], "roiOpt")],
     ylim = ylim,
     xlab = "dates",
     ylab = metric,
     main = (paste(metric, " for 3 samples ", sep = "")))
```

Do not place spaces around code in parentheses or square brackets.
Exception: Always place a space after a comma.

GOOD:
```
if (debug)
x[1, ]
```

BAD:
```
if ( debug )  # No spaces around debug
x[1,]  # Needs a space after the comma
```

* Curly Braces

An opening curly brace should never go on its own line; a closing curly brace should always go on its own line.

```
if (is.null(ylim)) {
  ylim <- c(0, 0.06)
}
```

Always begin the body of a block on a new line.

BAD:
```
if (is.null(ylim)) ylim <- c(0, 0.06)
if (is.null(ylim)) {ylim <- c(0, 0.06)}
```

Surround else with braces

An else statement should always be surrounded on the same line by curly braces.

GOOD:
```
if (condition) {
  one or more lines
} else {
  one or more lines
}
```

BAD:
```
if (condition) {
  one or more lines
}
else {
  one or more lines
}
```

BAD:
```
if (condition)
  one line
else
  one line
```

* Assignment

Use `<-`, not `=`, for assignment. Exception; within functions and loops `=` should always be used and never `<-`

GOOD: `x <- 5`

BAD: `x = 5`

* Semicolons

Do not terminate your lines with semicolons or use semicolons to put more than one command on the same line.

##### Organization

* General Layout and Ordering

If everyone uses the same general ordering, we'll be able to read and understand each other's scripts faster and more easily.

Copyright statement comment (not always necessary)
Author comment
File description comment, including purpose of program, inputs, and outputs
source() and library() statements
Function definitions should go in a separate file (*_functions.R)
Executed statements, if applicable (e.g., print, plot)
Unit tests should go in a separate file named originalfilename_test.R.

* Commenting Guidelines

Comment your code. Entire commented lines should begin with # and one space.

Short comments can be placed after code preceded by two spaces, #, and then one space.
```
# Create histogram of frequency of campaigns by pct budget spent.
hist(df$pct.spent,
     breaks = "scott",  # method for choosing number of buckets
     main   = "Histogram: fraction budget spent by campaignid",
     xlab   = "Fraction of budget spent",
     ylab   = "Frequency (count of campaignids)")
```

* Function Definitions and Calls

Function definitions should first list arguments without default values, followed by those with default values.

In both function definitions and function calls, multiple arguments per line are allowed; line breaks are only allowed
between assignments.

GOOD:
```
PredictCTR <- function(query, property, num.days,
                       show.plot = TRUE)
```
BAD:
```
PredictCTR <- function(query, property, num.days, show.plot =
                       TRUE)
```
Ideally, unit tests should serve as sample function calls (for shared library routines).

* Function Documentation

Functions should contain a comments section immediately below the function definition line. These comments should consist
of a one-sentence description of the function; a list of the function's arguments, denoted by Args:, with a description
of each (including the data type); and a description of the return value, denoted by Returns:. The comments should be
descriptive enough that a caller can use the function without reading any of the function's code.

Example Function

```
CalculateSampleCovariance <- function(x, y, verbose = TRUE) {
  # Computes the sample covariance between two vectors.
  #
  # Args:
  #   x: One of two vectors whose sample covariance is to be calculated.
  #   y: The other vector. x and y must have the same length, greater than one,
  #      with no missing values.
  #   verbose: If TRUE, prints sample covariance; if not, not. Default is TRUE.
  #
  # Returns:
  #   The sample covariance between x and y.
  n <- length(x)
  # Error handling
  if (n <= 1 || n != length(y)) {
    stop("Arguments x and y have different lengths: ",
         length(x), " and ", length(y), ".")
  }
  if (TRUE %in% is.na(x) || TRUE %in% is.na(y)) {
    stop(" Arguments x and y must not have missing values.")
  }
  covariance <- var(x, y)
  if (verbose)
    cat("Covariance = ", round(covariance, 4), ".\n", sep = "")
  return(covariance)
}
```
* TODO Style

Use a consistent style for TODOs throughout your code.

`TODO(username): Explicit description of action to be taken`

##### Language

* Attach

The possibilities for creating errors when using attach are numerous. Avoid it.

* Functions

Errors should be raised using `stop()`.

* Objects and Methods

The S language has two object systems, S3 and S4, both of which are available in R. S3 methods are more interactive and
flexible, whereas S4 methods are more formal and rigorous. (For an illustration of the two systems, see Thomas Lumley's
"Programmer's Niche: A Simple Class, in S3 and S4" in R News 4/1, 2004, pgs. 33 - 36:
https://cran.r-project.org/doc/Rnews/Rnews_2004-1.pdf.)

Use S3 objects and methods unless there is a strong reason to use S4 objects or methods. A primary justification for an
S4 object would be to use objects directly in C++ code. A primary justification for an S4 generic/method would be to
dispatch on two arguments.

Avoid mixing S3 and S4: S4 methods ignore S3 inheritance and vice-versa.

* Exceptions

The coding conventions described above should be followed, unless there is good reason to do otherwise. Exceptions
include legacy code and modifying third-party code.

##### Parting Words

Use common sense and BE CONSISTENT.

If you are editing code, take a few minutes to look at the code around you and determine its style. If others use spaces
around their if clauses, you should, too. If their comments have little boxes of stars around them, make your comments
have little boxes of stars around them, too.

The point of having style guidelines is to have a common vocabulary of coding so people can concentrate on what you are
saying, rather than on how you are saying it. We present global style rules here so people know the vocabulary. But local
style is also important. If code you add to a file looks drastically different from the existing code around it, the
discontinuity will throw readers out of their rhythm when they go to read it. Try to avoid this.

### Process

#### Data Storage and Versioning

The general file structure of data storage which should be duplicated across all types of storage and locations should be as follows.

```
data
│
│
└─── raw_data - data directly from the source
|   |
│   │   README.md
│   │
│   └───source_1
│   |   │   file012.csv
│   |   │   file112.RDS
│   |   └─── ...
|   |
|   └─── source_2
|       |
|       |
│       └───...
|
|
|
└─── final_data
|   |  README.md - Description of transformations
|   │
|   │
│   └─── analysis_1
│   |   │   file015.csv
│   |   │   file111.RDS
│   |   └─── ...
|   |
|   └─── analysis_2
│       │   file012.csv
│       │   file112.RDS
|       |
|       └───...
│
└─── personal_folders
    |  README.md - Description of test transformations/tests
    │
    │
    └─── user1
    |   │   file0120.csv
    |   │   file11.RDS
    |   └─── ...
    |
    └─── user2
        │   file2.csv
        │   file156.RDS
        |
        └───...



```
