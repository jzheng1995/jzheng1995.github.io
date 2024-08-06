---
title: "Qualtrics in R"
author: "Jason"
---

## Introduction - Qualtrics and R

Are you working in R and use Qualtrics surveys? Have your fingers fallen off from downloading each Qualtrics survey individually? Well, you have come to the right place.

And for those of you who don't know what's going on? Well, hello! You might be wondering what this whole "Qualtrics" and "R" jazz is about. R is a programming language that's often used for statistical analysis. Qualtrics is a platform that allows users to design and collect survey data. Why do we want to mix the two?

To work your statistical magic in R, you have to get down and dirty with data. But where do you get it from? You get the idea. If you have data in Qualtrics, they have a handy interface to download your collected survey data in its webpage navigation. 

[![Qualtrics Manual Download](https://www.qualtrics.com/m/assets/support/wp-content/uploads//2015/04/exporting-responses-1.png)](https://www.qualtrics.com/support/survey-platform/data-and-analysis-module/data/download-data/export-data-overview/)

You can fiddle with the options, hit download, and voila, you have your data. Now do that for every survey. Or in my case, some 36 surveys for a research study. 

Now personally, I'd like to spare my carpal-tunneled hands the misery of repetitive downloads. And as it turns out, we can skip all that by interacting with the Qualtrics API! Instead of requesting each survey separately through the front page, we can ask for all the surveys in several lines of code. 

## Overview

Here's an overview of our requirements. 

![overview-plan](/assets/posts/Qualtrics/grapha.png)

So this is how we'll approach it.

![overview-plan](/assets/posts/Qualtrics/graphb.png)


## Step 1: Qualtrics API token

For my non-technical friends out there: think of Qualtrics as our house of data, and the Application Programming Interface (API) as their door. We can get our data if we knock on their door, but we have to prove we aren't strangers. That's where the API token comes in, it's our identification card!

You can access the token generation page by clicking on account settings.

![account-settings](https://www.qualtrics.com/m/assets/support/wp-content/uploads/2015/04/account-settings-1.png)

Once you're in the account settings, you can click on Qualtrics IDs and then generate token from there. 

![generate-token](https://www.qualtrics.com/m/assets/support/wp-content/uploads/2021/05/generate-api-34.png)

<span style="color:red">***Warning*** </span> If you don't see the token generation, it's likely that your account has restricted access. Please contact your IT crew or whoever manages the Qualtrics subscription to enable it for you. 

Once you have successfully generated your token, keep it handy because we're going to use it soon! For more details on the API token, please see [Qualtrics' guide](https://www.qualtrics.com/support/integrations/api-integration/overview/)

## Step 2: QualtRics

As you can imagine, talking to an API ain't easy. You got to do some GET requests, learn it in R, learn about the particulars of [Qualtrics' API](https://api.qualtrics.com/). Luckily for us, there's a package called [QualtRics](https://github.com/ropensci/qualtRics) that has done most of the heavy lifting. All we have to do is set up our API token from earlier and use their library commands. 

## Qualtrics authorization
My method to going about this is to create a R script and save it in the same folder as my Qualtrics wrangling code/notebook. Use the following code below as a template for your R script credential.

```
library(qualtRics)

qualtrics_api_credentials(api_key = "<YOUR-QUALTRICS_API_KEY>", 
                          base_url = "<YOUR-QUALTRICS_BASE_URL>",
                          install = TRUE)
```
You'll notice that two fields have to be filled in for this to work.The API key is the API token we generated from earlier. The base url can be found using [this Qualtrics document](https://api.qualtrics.com/1aea264443797-base-url-and-datacenter-i-ds).

If you're working from a Canadian institution, you're likely going to use "https://ca1.qualtrics.com/API/v3" for your base_url since there's only one Canadian Qualtrics datacenter. Check [here](https://api.qualtrics.com/YXBpOjYwOTEz-data-access-ap-is) for other base urls if you're from an US institution.

Once you have your R script credential set up, insert the following chunk in the document you plan on using to access Qualtrics surveys.
```
source("qualtrics_key.R")
```

## Code and query

Now that we've set up our authentication, we should be able to access our Qualtrics account and connect to our surveys. The following code below should return the names of all your surveys. If this doesn't work, that probably means the authentication key is not working and you'll have to go back to troubleshoot. 

```
# Retrieve all survey names in account
survey_list <- all_surveys() 
survey_list
```

For my case, I was interested in extracting the surveys that were related to a COVID-19 study from my lab. I use regular expression to filter out survey names related to COVID-19. Feel free to substitute the "covid" string for your own filter purposes. There are two fields in this output you'll want to take note of. The first is survey `name`, which is the name that you've given to your suvey. The second is survey `id`, which is an unique identifier that Qualtrics gives to each of your surveys. We'll use this information later.

```
# Filter covid-related study surveys
covid_survey_list <- survey_list %>% as_tibble() %>% filter(str_detect(name,regex("covid", ignore_case = T))) 
```

The function that extracts surveys is `fetch_survey()`. There are several parameters in this function for you to explore. I'll be simply going over my use case but feel free to check out [the documentation](https://docs.ropensci.org/qualtRics/reference/fetch_survey.html) for more information. 

I wanted to read every survey as separate RDS files into a designated folder. I have a code chunk below to achieve this, feel free to adapt it. Remember to change `project_directory` to your desired location.

```
# Save each covid survey within temporary directory and read into environment
for (i in 1:nrow(covid_survey_list)){
  assign(covid_survey_list$name[i], 
         fetch_survey(surveyID = covid_survey_list$id[i],
                      unanswer_recode = -99,
                      save_dir = project_directory
                      ))
}
```

I'll now go over each parameter input.

Surveys are requested by on survey `id`. Notice that I created a table earlier that maps survey `id` to `name`. I identified the surveys I wanted by `name` and then map it to `id`. `id` is also fixed and isn't subject to change. I like using this for name assignment because the workflow will be more likely reproducible. 

`fetch_survey()` works by first saving the files to a temporary file folder but you can add the `save_dir` parameter to specify a target folder. 

`unanswer_code` represents questions that are seen but unanswered: To differentiate from simply not finishing the survey, I assign a `-99` value to this scenario. 

<span style="color:red">***Warning*** </span>: When you download a Qualtrics survey manually, the exported datetime fields are dependent on the timzezone set in your account settings. However, API-exported survey data have datetime set as UTC by default. See [here for data export documentation](https://www.qualtrics.com/support/survey-platform/data-and-analysis-module/data/download-data/understanding-your-dataset/).

## Summary

This has been an overview of how to connect to Qualtrics surveys in R. We reviewed how to create API credentials and access surveys via the `QualtRics` package. I hope you found this guide helpful, feel free to contact me for feedback or questions!
