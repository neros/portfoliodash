# Questions for Soren
2018-02-13

## Question 1: Portfolio selection - only one

In your comments (see below), you say that only one portfolio should be able to be selected at a time. This means that the app can't be used for aggregate analyses of multiple portfolios. From previous conversations, I had understood the opposite (that one of the goals was to be able to aggregate different portfolios to explore). Can you confirm that only one portfolio should be able to be selected at a time?

>>Soren>> Yes, I confirm that only one portfolio should be selected at a time.  This said, I have some concern that you're fully understanding what is meant by a "portfolio" as some of the comments in the marked-up document note, for example, that organizational departments are being referred to incorrectly as portfolios.  This is not a simply symantic matter but speaks fundamentally to how IFC projects are organized (and how we want to pull them into the dashboard).  Let's ensure we're all fully on the same page on these points.   And how a portfolio is defined through the app is a somewhat nuanced point as well.   In brief, yes, we want to enable aggreagtes (among other things); and yes, we want users to select only one portfolio at a time.  This is easily accomplished by enabling a portfolio to be defined as a collection of attributes with respect to which we want to view and aggregate underyling portfolio data.  At the same time, to also provide standard filters so that a broadly-defined portfolio can be quickly sub-aggregated on-demand by using filters.

![](img2/Selection_001.png)

## Question 2: Portfolio selection - none

In the same comment (see above), you say that this selection is "not necessary" and that it can be "none". I don't understand. In this case, what does the user see? Are all the charts and tables then empty?

>>Soren>> Keeping in mind that standard filters are available, that a user could, if so desired, simply access the full AS portfolio and then just use standard filters to get what they want.   The only issue here is that we don't want to pull-in the full AS portfolio as a matter of default since the data is huge and will require people to wait while downloaded and loaded into R.   That annoyance should only be endured by people that are explicitly looking to load the full portfolio.   Instead of "none" perhaps there can be a built-in "Full AS Portfolio" portfolio.   This might presumably be a path through which people can create portfolios with respect to standard filters rather than (or in addition to) specifying individual project.   Eg, select "Full AS Portfolio" and then "FIG" department and then "SSA" region and then "active" projects and then "save as portfolio" ... They could then select that saved portfolio next time.  But conceptually, it's a one-off need and they just want to load based off the full set of projects without defining a portfolio.

## Question 3: What to do with useless "Stage" categories

In your comment (see below), you say that certain "stage" categories aren't useful. Would you like me to manually hard-code a filtering of these in the app, or is this something that will be handled on the database side?

>>Soren>> In terms of built-in filters, we'll probably want to ceate some hard-coded meta filters.  Eg, one for 'completed' 'portfolio' and 'pipeline' (which is the group 99% of the people are interested in 99% of the time) and another filter for everything else.  But then to also enable more granual filter selection for any user that wasn't some unusually customized grouping of project statuses.  

![](img2/Selection_002.png)

## Question 4: What are "portfolio names"

In your comment (see below), you point out that the names used for the "Select portfolio(s)" input refer to departments, not portfolios. I used the `primary_business_line_code` field to create these. This table reads directly from  the database (ie, whatever the "portfolio_name" field in the `portfolios` table is what will show up here). My assumption, therefore, is that I don't need to change anything here. Correct me if I'm wrong.

>>Soren>> See first comment above.  Computationally speaking, no, I don't think you're wrong.  But conceptually speaking, the incorrect use of nomenclature raises a concern for me that we're on the same page in terms of what a portfolio is versus what a department (department and 'business line' are interchangable for all practical purposes).   A lot of these departments are sort of useless too, or archaic and outdated.  We may also want create a filter...or at least a sort to put useless departments at the bottom of a filter list.  (usless departments are easily identified by seeing how many have active projects in the current dataset -- few to zero mean useless).

![](img2/Selection_005.png)



## Question 5: What are the fields for other other requested filters?

You mention that we should filter by "dept" and "product" (see below).
- By department, do you mean `owning_department_code`?
- By product, do you mean the semi-colon separated values of `business_line_product_pcts1`?

>>Soren>> By department I mean primary business line/primary business line code.   By product, yes, I mean the semi-colon delimied list.  There's a view in the database that string splits these and aggregates to be able to pick-out primary product(s).   I think I also set a script to update this information in the underlying portfolio data for simplicity's sake.  The full semi-colon list with all the various meta information isn't super useful and it's annoying to parse.  Better to pull from the view and/or rely on the generated field that now exists in the portfolio datasets.

![](img2/Selection_004.png)

## Question 6: How to simplify the first page?

You mention that the charts on the first page are overly complex (by region, etc.) and suggest using simpler or pie style charts. Is this what you had in mind?  (see below)

>>Soren>> Yes, looks better.  Small, concise, at-a-glance.  Oleksiy can also share screenshots of the standard portfolio reporting dashboards.  There will be some replication between these.   Stepping back, what sets our project apart is enabling user-defined custom portfolios and also pulling in results data (eventually).  I think we'll deliver a cleaner and nicer UI, but that's not what will set it apart.

![](img2/Selection_007.png)

## Question 7: How to calculate "amount spent"?  
 
In the above right-most chart, I am using the `total_fytd_expenditures` field for amount spent. Is there something else I should be using?

>>Soren>> use "incepton to date" expenditures,  "idt" and not "fiscal year to date", "fytd" -- fytd is only relative since the most recent July 1st (start of IFC's fiscal year) and is a running total from July 1st through end of FY on June 30th.   It's useful for looking at annual budget vs actual expenditures, but not useful for total project progress.  It's also only useful in context of the annual budget (which isn't available in the portfolio data--I don't know why--and needs to be accessed through separate budget reports).  We may eventually start pulling these in but first portfolio data; second results data; then let's see about budget data.

## Question 8: Database schema, etc.

(Not really a question) I've overhauled the documentation which covers how to build the database from scratch. Feel free to comment. [HERE](https://github.com/databrew/portfoliodash/tree/feature/mid_february#portfolio-dashboard)

>>Soren>>Project isn't building database schemas from scratch...but ok.

## Question 9: What is the "start date" and "end date" for each project?

My understanding is that the `dataset_date` field is the date of observation for each product. For the spending fish, the x-axis will be the percentage of project completion, right? In this case, I need to know what the start date and end date for each project is, and it's not clear from the raw data what this would be.

(For start date, I could use earliest observation of `dataset_date`, but I'm wondering if there is a pre-first-observation date)

(For end date, I could use latest observation of `dataset_date`, but many projects have only one `dataset_date`)

>>Soren>> A good question and non-trivial answer.  The short answer is that the view_reporting_project_longevity and view_reporting_quarterly_timeseries should provide the relevant dates for their respetive reporting needs.  The longer answer is that 'project implementation start date' should be used as the start date; and 'project implementation end date' should be used as the end date.  That said, 'project approval completion date' is when then project actually closes as the official end date.  That date doesn't appear until the project actually closes, administrateively in the system.  And seldom does the actual closing date match the projected implementation end date.  There can also be some discrepancy between start date and the 'implementation plan approval date' which is also the official start -- but sometimes projects actually begin work and spending money before they're approved (naughty, naughty, finger wag); but so it goes and it shows up in the data.  Some projects also have financial closure end dates that extend beyond official closure dates, causing operationally-closed projects to linger in the 'active' portfolio for a while because they're used to continue to pay salaries or fund some donor obligation even if operations have ceased and it's officially closed.  These are some of the complexities and gray areas.  Again, referring to the short answer: we should hard-code answers to these questions in relevant views that pull data into the UI.  But also be aware of the gray areas and include that in any definitions or discussions that may be made-available to users.

