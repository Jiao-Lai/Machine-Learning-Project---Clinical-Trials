# Machine-Learning-Project---Clinical-Trials
Setting: The clinic designed a control experiment to test the efficacy of the new medication for treating schizophrenia. On the initial day, day 0, the clinic collected hundreds of patients, and randomly separated them into treatment group and control group after assessing their severity scores, which consisted of 30 dimensions (7 dimensions for Positive scores, 7 dimensions for Negative scores, and 16 dimensions for General scores). The treatment group was given new medication, while the control group was given placebo. During the experiment, which lasted for 18 weeks, the clinic re-assessed each patient. Experts were hired to review each assessment to make sure they are valid. 

Data structure: for each observation, we have patientID, TxGroup (treatment vs control), assessmentID, reviewerID, patient score (30 dimensions), patient country, clinicID, siteID, VisitDay (how many days have passed since day 0), flag (passed vs. non-passed depending on whether the assessment is valid or not).

Goal 1: determine if the new medication is useful for treating schizophrenia? This is extremely important for clinics and patients as clinics want to make money by selling useful medications, and patients are willing to pay for medications which are proved to be effective.
Goal 2: patient segmentation. Segmentation could be very useful depending on the segmenting standard. For example, the clinic might want to know the distribution of disease severity among patients.
Goal 3: predict last visit assessment score for intentionally left out data set. This task is important when clinics lack the last visit assessment for some patients. 
Goal 4: build a classification model to predict the flag for test data set. This is extremely useful since a well trained classification model can decrease both labor and monetary cost for clinics to hire experts to review assessments.

Challenges: 
1. The biggest challenge for this project is the validity of experimental design. I wasn?t involved in the process of experiment design nor aware of any additional information besides the data. My work was based on the assumption that the experiment was well designed. Specifically, I assumed patients between treatment and control groups were all else equal, i.e. they were similar except the medication they were taking. It was possible that patients were seeking other treatment, but that was beyond my considerations. The second assumption is that the clinic is professional. That?s why I didn?t add qualitative information regarding clinics in my machine learning models. For example, I don?t think the results would be different if the study were performed in another clinic.

2. The second challenge is that this is a real project which has data challenges. Specifically, we have unbalanced data for each patient. Some patients visited the clinic regularly while many patients only visited the clinic a few times.

My work:
Data pre-processing: check for duplicates, missing data, outliers, error inputs

For goal 1-3, only valid assessments were used.

For goal 1, I used the Difference in Difference model, which is typically used to compute post-pre changes between two groups. If there is truly an effect, then it should be shown by comparing the assessment score at the end of the study with the assessment score at the beginning of the study. Therefore, I selected each patient?s last observation and initial observation for both the treatment group and control group. I would like to see a bigger change in post-pre assessment score for the treatment group than the control group. 

I first did a sample test for post-pre difference between these two groups regarding Positive/Negative/General/Total scores.

Next, I used linear regression to confirm the result. Specifically, I used the final visit assessment score - initial assessment score (Positive/Negative/General/Total) as dependent variable, independent variables I selected were: treatment group indicator, patientID, patientCountry. The coefficient of interest is for the treatment group indicator.

In addition, I compared day 0 assessment scores between treatment and control group to make sure they are comparable. 

For goal 2, only observations from day 0 were used since our subject was patient. Patients related features are patients? assessment scores, and patientCountry. For this segmentation, I used patients? day 0 assessment scores as features. I used k-means clustering to segment patients into 2 - 5 groups, and selected the optimal k (which is 3) by looking at the elbow curve. Finally, I visualized the 3 groups using Scatter D3 to find the similarity within each group and difference among groups.

Another way to segment patients is through their leave status, which is available only at the end of the study. Specifically, we can observe the last day for each patient and then segment them into 2 groups: those who stayed until the end of the study, and those who didn?t. This segmentation is helpful for maintaining the retention rates for patients especially when a large portion of patients dropped out early from the experiment. For this purpose, I built a logistic regression to find the trend for leaving. I used the patient's leave status as dependent variable, and day 0 assessment score, patientCountry, treatment group indicator as independent variables. I also used visualization to plot the histogram of patients? last visit day to find the leave trend, which is patients who left the study tend to leave early. 

For goal 3, I used a simple linear regression to predict a patient's last visit assessment score. The logic is, a patient?s severity doesn?t change in a short period of time, so their day t score can be a good proximity of their day t + 1 score. To train this model, I selected patients with the last visit assessment score and their most recent assessment score is within one week of their last visit assessment score. 

For goal 4, I first segmented observations into two groups (passed vs non-passed) based on the flag from reviewers. The features predictive for flag status are VisitDay, treatment group indicator, and relevant visit day (how many times the patient showed up by now since day 0). From between group visualization of input features, relevant visit day standed out. And its importance is confirmed by logistic regression with dependent status flag status. 
