---
title: How to Export and Attach a Power BI Report as a PNG and PDF in an Email
author: 'Emaly Vatne'
date: '2024-11-05'
slug: Periodization
categories: []
subtitle: ''
summary: 'Power Automate can be used to export a Power BI report (either a single page or the entire report) in the body of an email as a PNG and/or as an attachment as a PDF. This brief article will outline how to do this. However, it is important to note that some of the features described below may required paid licenses of Power BI and/or Power Automate.'
authors: []
lastmod: '2024-11-09T20:57:45-05:00'
projects: []
---

The Microsoft Power Platform has a lot of exceptional features for data analysis, data visualization, and scaled communication. One use-case in sport science is sending out daily email reports summarizing athletes' recovery information, training or game loads, or daily availability reports. While this task is easy to complete, scaling this service to many teams can easily consume large amounts of staff bandwidth and team. Therefore, the use of the Power Platform, specifically Power BI and Power Automate, can offload and automate this task to improve efficiency. This post will describe how to configure a Power Automate flow to export a Power BI report (either a single page or the entire report) in the body of an email as a PNG and/or as an attachment as a PDF and save the PDF file in a Teams/Sharepoint folder. I want to note that I'm writing this is as of November 2024. Microsoft is often releasing updates to the Power Platform so these steps may become outdated quickly.

First, you need to publish a Power BI report to the Power BI service. Power BI reports can be generated on Power BI desktop, but once you'd like to share it with other users, it's best to publish the report to Power BI service so other users can access it using a link. It's also important to distinguish a Power BI report and a Power BI dashboard. A Power BI dashboard is generally limited to a single-page and intended to share real-time or near real-time insights at a high-level. Power BI dashboard also have "data alerts" for gauges, KPIs, and cards. This will be relevant for when we want to trigger our Power Automate flow. A Power BI report typically includes multiple pages that can be filtered and provide in-depth analysis. These allow users to interact and slice/filter the data. While we share Power BI reports with our end-users, we will use Power BI dashboards to trigger a Power Automate flow using data driven alerts.

Data driven alerts allow for notifications when the value of a gauge, KPI, and card changes beyond a threshold that you set. Configuring a data driven alert is going to be the first step in this process. To do this, navigate to your Power BI report and identify a gauge, KPI, and card that you would like to trigger an email of the report being sent. Make sure the title of whatever you'd like to use is unique and identifiable. For example, if you're configuring this for multipe teams, it'd be best to note the abbreviated team name in the title and what it's representing. In the context of sport science, this might look like the number of athletes who have synced a sleep and recovery device like an Oura ring, submitted a daily wellness survey, or if new data are available for today's training session. Once you have the gauge, KPI, and card you'd like to use, zoom in to it and click the pin icon. A pop-up window will appear and prompt you to pin it to a Power BI dashboard. If you already have one, it'll give you the option to use it or you can create a new one. See the screenshots below.

[Where to click to pin a card in a Power BI Report](https://github.com/emalyvatne/portfolio_resources/blob/main/EmailPowerBI_PowerAutomate/pinvisual.png)



Once it's pinned, navigate to the Power BI dashboard you just pinned the gauge, KPI, and card to. I created a new Power BI dashboard and pinned my tile to it, so mine looks like this:





Click on the ellipsis on the top right corner of the tile and then click on manage alerts. This is where we'll define a threshold that we want to use to send an email when the report page contains all of the information that is relevant. I'm using a daily report of sleep and recovery information collected using an Oura ring for myself and my sport science teammates. There are only 3 of us connected to this report in sport science team, so I want the email report to be sent out when there are 3 individual users in the report. The tile I'm using is "# Athletes Synced Today" that filters for users in the "Sport Science" group and for only today. Since I want the report to include all 3 users, I'm going to set the threshold to 2 when the tile becomes greater than 2 and set it to check every 24 hours. Now, this data-driven alert will check once per day for when there are more than 2 users' data available in today's report. When this is true, it will result in an alert. You can choose to receive an email when this alert is triggered, but I opted not to use this.





Next, we're going to work in Power Automate to create our flow. Again, note that you may need "Premium" licenses to create what I'm describing below. I'm not a Power Platform license expert by any meants, but if you're still reading this, I'm guessing you have the right license. Go to [make.powerautomate.com](https://make.powerautomate.com) and click on "Templates" in the left-hand sidebar and then search "Power BI data alert". We're going to use the "Send an e-mail to any audience when a Power BI data alert is triggered" template. Make sure that it connects to the right accounts before proceeding with this template. In other words, whatever Microsoft account is associated with the Power BI dashboard and that you want the email sent from.

In the first step of the flow, select the name of the tile, gauge, or KPI that you configured on the Power BI dashboard. If nothing appears in this dropdown, you may not have created the data driven alert correctly. You can also choose the frequency you want this flow to check for updates, but I usually leave it at the default of every minute.





Next, we're going to insert steps to export the Power BI report as a PDF and PNG. Click "Insert Step" and choose "Export To File for Power BI Reports" and add (PDF) to the name. Click this step and then choose the workspace where your Power BI report is located (Remember! this is different from the Power BI dashboard we were just working with for the data alert even though the data are related) and then choose the Power BI report name that you want attached to this email. In the advanced parameters, toggle "Export Format" and select PDF. You can also specifiy the page of the Power BI report. For example, if you don't choose this, it will attach a PDF of every page in the report. If you choose specific pages, it will only attach a PDF with the page you explicitly define. This is where it's a little tricky; the "Pagename" for the Power BI report page is not what you made it in the Power BI report. It is actually going to be part of the URL. In a new tab, open the Power BI report you want sent in the email. If you have multiple pages, click between two pages and watch for where the URL changes. Towards the end of the URL and between a "/" and "?experience=power-bi?", there will be a ~20 alphanumeric string that you will put in the Pagename in Power Automate. (I wish it could just be the literal page name but that would just be too easy) You could also just include certain bookmarks or visuals but I honestly have never tried to do this.

To add the image of the Power BI report page to the body of the email, you're going to complete the same step but specify the export format as PNG. Also be sure to name the "Export To File for Power BI Reports" so you can tell the difference between the two. (The PDF and PNG don't have to be same page, but for the sake of keeping this article somewhat simple, I'm going to keep them the same)

In order to add the image in the body of an email, we're going to have to convert it to a variable. Between the second Power BI Export step and the email step, insert a step and choose "Initialize a Variable". Name it "Power BI Export Image" and specify it as a string. In the value box, paste the following code:

```html
<div style="height:auto; overflow:auto;">
    <img src="data:image/png;base64,@{outputs('Export_To_File_for_Power_BI_Reports')?['body']?['$content']}" style="width:auto; max-width:100%;" alt="Power BI Export" />
</div>
```

This code is going to build the image. 

The email step I am using is Outlook Send an Email (V2). Add your recipients, a subject, and then any text you'd like in the body of the email. To add the image, click the lightning bolt and choose the name of the variable you initalized in the previous step. Continue down to the advanced parameters step and select Attachments and Importance. Depending on the version of Power Automate, it might let you choose the item or it may require you to create it using code. Paste the following code and select your export step to include the PDF as the attachment:

[
  {
    "Name": "Daily Repovery Report - @{utcNow()}.pdf",
    "ContentBytes": @{body('Export_To_File_for_Power_BI_Reports_(PDF)')}
  }
]

I added the "@{utcNow()}" so that the PDF include the date and time that it was created. This will allow me to go through archived files and find a particular day I'm interested in very easily. 

In practical application, an email report is convenient but no one is going to want to go back through their emails and try to find a specific day. One way to address this is to add a step that saves the PDF in a Teams/Sharepoint folder. In whatever Team makes sense in your context, create a folder for these files to land. Come back to Power Automate and add a step after the email and add a step. Search for Sharepoint create file. Navigate to the folder you just created or that you want these to land in (find the team, Shared Documents, channel, and folder). Specify the name you want the file to be given (you can add the utcNow function again) and then for "File Content", select the file you want to created in the "Export To File for Power_BI Reports (PDF)" step.

Once this is done, make sure to save your flow, hit back, and turn it on and it should be ready to go! If you have questions, feel free to email me and I'd be more than happy to try help troubleshoot.