# Drug Money

<h3><b>Introduction</b></h3>
Every year the pharmaceutical industry showers doctors with billions of dollars of cash and gifts for a chance to win over their prescription pads. Whether it be food, travel, gifts, or licensing fees each payment must be disclosed to the Centers for Medicare & Medicaid Services. In 2014, the government decided to make this information publicly available as a part of a dataset called Open Payments.

I wanted to not only see how much these companies are paying doctors but also if money truly does talk. Utilizing other datasets also publicly available through the Centers of Medicare & Medicaid Services, I analyzed more than 40 million payments and 95 million prescriptions to uncover if lining doctors' pockets affected the number of brand-name medications they prescribed and found trends within certain specialties displaying statistically significant differences in the number of brand name prescriptions written by doctors receiving payments versus their colleagues who were not receiving payments.

<h3><b>Background</b></h3>
<em><h5>The Sunshine Act</h5></em>
</div>
<div>
<p class="text-primary">In 2013, the United States Centers for Medicare & Medicaid Services (CMS) introduced a new program that mandated pharmaceutical companies and manufacturers to submit records of payments made to healthcare professionals. The CMS published this data under the Open Payments dataset to provide increased transparency regarding the types of financial relationships between the corporate healthcare industry and healthcare providers. It is important to note that this data is not meant to denote any sort of inappropriate or unlawful behavior. Yet, these relationships may influence the behavior of doctors potentially resulting in impaired patient care, compromised integrity, and increased healthcare costs.
<br>With this knowledge at hand, I wanted to explore the potential impacts these payments may have on healthcare providers, specifically on the types of prescriptions they write.</p>
</div>
<div class="title">
<h3><b>Data</b></h3>
<em><h5>The Open Payments & Medicare Part D Provider Utilization & Payment datasets</h5></em>
</div>
<div>
<p class="text-primary"><b>Open Payments</b><br>
  When the Open Payments data was first released in 2013, only data for August - December 2013 was gathered and released. Yet in subsequent years, full yearly data was available. This data encompasses two main types of payments, those for research purposes and general payments for things such as travel, food/beverages, gifts, speaking fees, licensing fees, etc. Altogether, over 40 Million payment records have been released for the years of 2013 through 2016, totaling an amount of $25 Billion. From these 40 Million records I focused on the 38 Million general payments going to healthcare providers rather than the 2.5 Million for research purposes. Beyond the number of payments, this dataset provided basic information regarding both the doctor receiving and the company providing the payment, along with a classification for the type of payment, and details about the form and nature of the payment.<br>

  <b>Medicare Part D Provider Utilization & Payments</b><br>To get a basis for the type of prescriptions doctors write, I utilized yearly Medicare Part D Prescription datasets which provided aggregate information about the prescriptions a doctor wrote for that year. This dataset outlined the amount and costs of each prescription written for and utilized by Medicare Part D participants along with basic information regarding the prescribing individual and the drug itself. Altogether, these yearly datasets contained more than 100 Million aggregate records between the years of 2013 and 2016. 
</p>
</div>
<div class="title">
<h3><b>Approach</b></h3>
<em><h5>Matching the payments providers received to the prescriptions they wrote</h5></em>
</div>
<div>
<p class="text-primary">
  Every single healthcare provider in the United States is given a unique National Provider Identifier (NPI), it is essentially a healthcare professional equivalent of a social security number. This NPI is used to track them and their data, in this instance, over multiple datasets and situations. Practically all datasets about healthcare providers include this number and the Medicare Part D Prescriptions database is no different.<br>
  <b>But, Federal law prohibits the government from releasing NPIs in the Open Payments data.</b> Instead, a randomly generated unique payment ID is utilized to link providers within all Open Payments databases, but not outside of them. While NPIs are not released, the government does include some basic contact information for each of the records. This introduced a unique challenge for me as I had to find a way to link a provider's NPI to their Open Payment ID with just a few pieces of information.<br>
  <em><h5>Linking Payment IDs to National Provider Identifiers (NPI)</h5></em>
  Since the Open Payments dataset included a provider's first name, last name, specialty, and full address I knew that this would be the best method for me to link NPIs to the Open Payments ID. Even though this information was given in the Open Payments dataset it was not included in the Prescriptions dataset. Instead, I utilized a descriptive NPI dataset and joined a provider's first name, last name, specialty, and full address to their records in the Prescriptions dataset based on their NPI. After I had these fields present in both the Open Payments and the Prescriptions datasets I could finally perform a linkage.
  I iterated through each dataset and created a dictionary of all the NPIs present in the Prescriptions dataset and all the Payment IDs present in the Open Payments dataset. These dictionaries contained the Payment ID or NPI as the key and had another dictionary as the value for these keys. This enclosed dictionary contained the first name, last name, specialty, state, address, and zip code as keys and the values were every value that appeared for that column under that NPI or Payment ID.
  Once these dictionaries were made, I created an algorithm that iterated through each dictionary and scored the similarity between an NPI and Payment ID based on the keys of the enclosed dictionary. If an NPI and Payment ID had more than a 90% similarity, they were linked together meaning that x Payment ID belonged to y NPI. Through this method, I was able to link >99% of NPIs present in the Prescriptions dataset to their Payment ID, with less than 0.001% of the linkages being incorrect due to duplicative information. <br>
  <em><h5>Exploring linked NPIs & Open Payment IDs</h5></em>
  The linkage of the NPIs to Open Payment IDs allowed me to work through the Prescriptions database to compare the mean brand-name prescribing rates of doctors who received and did not receive payments within the same specialty. Using an unequal variances t-test (Welch's t-test), I performed a statistical analysis on each specialty to see if payments affected the mean rate of brand name prescriptions being written. To ensure the data utilized for each specialty was sufficient to serve as an indicator of the population, I only analyzed specialties where there were at least 30 doctors in both the unpaid and paid groups. Through this analysis, I was able to find statistically significant results displaying that within the specialties of Family Medicine, Internal Medicine, and Opthalmology displaying that doctors in these specialties who received payments were writing brand-name prescriptions at higher rates than their colleagues who did not receive payments.
</p>
</div>
<div class="title">
<h3><b>Limitations</b></h3>
<em><h5>Correlation & Causation</h5></em>
</div>
<div>
<p class="text-primary">
  It is important to note that this analysis is not all-encompassing and has many limitations which could significantly alter its results.<br>
  <ol>
    <li><b>Generic Alternatives</b><br>
      A factor that I was not able to account for was checking if there are any current generic alternatives for the name-brand drug a doctor prescribed. This is important as doctors who have to prescribe their patients' medications without any generic alternatives would naturally have higher brand name percentages compared to those with the choice to prescribe generic alternatives. 
    </li>
    <li><b>Medicare Part D</b><br>
      The prescription data was drastically limited to only a small subset of the entire American population. Since these prescriptions were mainly written for an older population and certain people with disabilities, it is unlikely to be indicative of the medications prescribed for the entire American population. Results may differ if the analysis was able to encompass data for all prescriptions written rather than just Medicare participants, however, brand-name versus generic prescription rates would likely remain constant. 
    </li>
    <li><b>NPI linkage</b><br>
      Since the focus of this study was on the doctors who received payments and had written Medicare Part D prescriptions, NPIs for doctors not appearing in the Medicare Part D Prescriptions dataset were not considered. Only doctors who were a part of the Medicare Part D dataset and had received payments were included in this analysis. 
    </li>
    <li><b>Confounding Factors</b><br>
      It's important to understand several other factors may have influenced doctors to write greater or fewer brand-name prescriptions beyond those incorporated within this analysis. Whether it be a doctor's patient base, location, drug recalls, or other external forces without more information it is hard to determine whether receiving payments truly affects the number of brand-name drugs a doctor prescribes. <b style="color:crimson">The findings simply show a correlation between the two but do NOT imply causation.</b><br>
      </li>
      </ol>

  <h2>Findings</h2>
  <h4>
    <b>Basis</b>
  </h4>
  <p class="text-primary">To determine if there was a significant difference in the number of brand name prescriptions written by doctors who were paid and their colleagues who were not, I performed A/B testing on various specialties by utilizing unequal variances t-tests (Welch's t-tests). Due to the large number of t-tests that were being done, the probability of encountering a Type I error increases greatly. To counter this and reduce Type I error I utilized a Bonferroni correction where my significance level was divided by the number of tests being run, thereby increasing our threshold even further and limiting error.<br>
  As there could be confounding factors not taken into account due to the data available which may influence the results of this study a few steps were taken to eliminate additional error:<br>
  <ol>
    <li>
      Since the type of prescriptions a doctor in a specialty such as Anesthesiology will write can be drastically different than the prescriptions a Pediatrician would write doctors were only compared to those within their specialty. This could cause disparities between the percentage of brand name prescriptions certain specialties wrote, due to availability for different drugs. 
    </li>
    <li>
      t-tests were only performed on prescriptions and payments within the same year as new drug releases, drug recalls, and copyright expirations could also drastically change the type of drugs prescribed that year. 
    </li>
    <li>
      Only specialties with more than 30 doctors in both the paid and unpaid groups were considered. This eliminated about 20% of the 100+ specialties I was testing as the small sample size in an already limited frame would not have been indicative of the population. Furthermore, certain specialties such as Nurse Practitioners, Physician Assistants, etc. were excluded as companies were not required to release details regarding payments made to these groups resulting in the positive class being 0 and making comparison impossible. 
    </li>
  </ol>

<h4>
  <b>Results</b>
</h4>
<p class="text-primary">Overall, my results were fairly common throughout, through all years there were only 3 specialties which displayed a significant difference in the mean percentage of brand name prescriptions written by paid doctors to those who weren't paid, even when they were compared for all years combined.<br>The specialties with a statistically significant difference were:
  <ol>
    <li>
      <b>Family Practice</b>
      <ul>
        <li>Non-paid Doctors mean brand name prescriptions: <b>12.8%</b></li>
        <li>Paid Doctors mean brand name prescriptions: <b>15.2%</b></li>
        <li>P-value: <b>9.41 x 10<sup>-6</sup></b></li>
      </ul>
    </li>
    <li>
      <b>Internal Medicine</b>
      <ul>
        <li>Non-paid Doctors mean brand name prescriptions: <b>14.8%</b></li>
        <li>Paid Doctors mean brand name prescriptions: <b>17.6%</b></li>
        <li>P-value: <b>2.77 x 10<sup>-4</sup></b></li>
      </ul>
    </li>
    <li>
      <b>Ophthalmology</b>
      <ul>
        <li>Non-paid Doctors mean brand name prescriptions: <b>41.4%</b></li>
        <li>Paid Doctors mean brand name prescriptions: <b>50.1%</b></li>
        <li>P-value: <b>1.43 x 10<sup>-8</sup></b></li>
      </ul>
    </li>
  </ol>
