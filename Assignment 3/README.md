Assignment 3
================
Carmen Chen
11/3/2021

**APIs**

1.  Using the NCBI API, look for papers that show up under the term
    “sars-cov-2 trial vaccine.” Look for the data in the pubmed
    database, and then retrieve the details of the paper as shown in
    lab 7. How many papers were you able to find?

``` r
#Download the website
website <- xml2::read_html("https://pubmed.ncbi.nlm.nih.gov/?term=sars-cov-2+trial+vaccine")

#Find the counts
counts <- xml2::xml_find_first(website, "/html/body/main/div[9]/div[2]/div[2]/div[1]/div[1]")

#Turn it into text
counts <- as.character(counts)

#Extract the data using regex
stringr::str_extract(counts, "[0-9,]+")
```

    ## [1] "2,339"

``` r
library(httr)

#Make query
query_ids <- GET(
  url = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/esearch.fcgi",
  query = list(
    db = "pubmed",
    term = "sars-cov-2 trial vaccine",
    retmax = 250
  )
)

#Extract the content of the response of GET
ids <- httr::content(query_ids)
```

``` r
#Turn the result into a character vector
ids <- as.character(ids)

#Find all the ids
ids <- stringr::str_extract_all(ids, "<Id>[[:digit:]]+</Id>")[[1]]

#Remove all the leading and trailing <Id> </Id>
ids <- stringr::str_remove_all(ids, "<Id>|</Id>")
head(ids)
```

    ## [1] "34739520" "34739037" "34735795" "34735426" "34735018" "34729549"

``` r
paste(ids, collapse =",")
```

    ## [1] "34739520,34739037,34735795,34735426,34735018,34729549,34726743,34715931,34713912,34711598,34707602,34704204,34703690,34702753,34698827,34697214,34697213,34696316,34696233,34696198,34691078,34691071,34690266,34689821,34689348,34689329,34688948,34688944,34674742,34671773,34671153,34670021,34667427,34666989,34665829,34664616,34659237,34656181,34655522,34649298,34642699,34642255,34635537,34635140,34633259,34632800,34625925,34624681,34624095,34620531,34620207,34615860,34607353,34601675,34601658,34597941,34597298,34594036,34588689,34587382,34585882,34582072,34580673,34580303,34580150,34579229,34578426,34578287,34576859,34565332,34561414,34558459,34557558,34555007,34555004,34551225,34551104,34547465,34545372,34545367,34541571,34539248,34537835,34536349,34535094,34534516,34526698,34526310,34526104,34526086,34525277,34525276,34522017,34521677,34519540,34519154,34516531,34515857,34508342,34499628,34496115,34494032,34494017,34493842,34490414,34488843,34480858,34480857,34480056,34475443,34472778,34464194,34456331,34455307,34452774,34451985,34451950,34451494,34449681,34447374,34446699,34446634,34445700,34445670,34444549,34441048,34438245,34437985,34417165,34416193,34414930,34414368,34413782,34411531,34405364,34401888,34400116,34396154,34393789,34388532,34388395,34379917,34379915,34378087,34375655,34374951,34373623,34373443,34370971,34365034,34364311,34362855,34362060,34359986,34358163,34358090,34349145,34348019,34347950,34344823,34343152,34336136,34335627,34331051,34330945,34330895,34325728,34324836,34323685,34316063,34316051,34313516,34310400,34308319,34303849,34297792,34290390,34289273,34285402,34277027,34273397,34273260,34272224,34270458,34270449,34267764,34267349,34267185,34260849,34260716,34258603,34254894,34251417,34250456,34249853,34249101,34246358,34244768,34244681,34244387,34243845,34242356,34241782,34238338,34236264,34234059,34230477,34225791,34225463,34224667,34222848,34213848,34212944,34212511,34209711,34206727,34202573,34202429,34200720,34197764,34197281,34192426,34190575,34189538,34181880,34180347,34179072,34178577,34174875,34174097,34171559,34170788,34166399,34162739,34162141,34158283,34158099,34136133,34154428,34153264,34153099,34150443,34146892,34145414,34145413,34143581,34132395,34130883,34124693,34119845"

2.  Using the list of pubmed ids you retrieved, download each papers’
    details using the query parameter rettype = abstract. If you get
    more than 250 ids, just keep the first 250.

``` r
publications <- GET(
  url   = "https://eutils.ncbi.nlm.nih.gov/entrez/eutils/efetch.fcgi",
  query = list(
   db = "pubmed",
   id =  I(paste(ids, collapse = ",")),
   retmax = 250,
   rettype = "abstract"
    )
)

# Turning the output into character vector
publications <- httr::content(publications)
publications_txt <- as.character(publications)
```

3.  As we did in lab 7. Create a dataset containing the following:
    Pubmed ID number, Title of the paper, Name of the journal where it
    was published, Publication date, and Abstract of the paper (if any).

``` r
pub_char_list <- xml2::xml_children(publications)
pub_char_list <- sapply(pub_char_list, as.character)

abstracts <- str_extract(pub_char_list, "<Abstract>[[:print:][:space:]]+</Abstract>")
abstracts <- str_remove_all(abstracts, "</?[[:alnum:]- =\"]+>") # '</?[[:alnum:]- ="]+>'
abstracts <- str_replace_all(abstracts, "[[:space:]]+", " ")

titles <- str_extract(pub_char_list, "<ArticleTitle>[[:print:][:space:]]+</ArticleTitle>")
titles <- str_remove_all(titles, "</?[[:alnum:]- =\"]+>")

journals <- str_extract(pub_char_list, "<Title>[[:print:][:space:]]+</Title>")
journals <- str_remove_all(journals, "</?[[:alnum:]- =\"]+>")

date <- str_extract(pub_char_list, "<PubDate>[[:print:][:space:]]+</PubDate>")
date <- str_remove_all(date, "</?[[:alnum:]- =\"]+>")
date <- str_replace_all(date, "[[:space:]]+", " ")
  
database <- data.frame(
  PubMed = ids,
  Title = titles,
  Jounal = journals,
  PubDate = date,
  Abstract = abstracts
)

knitr::kable(database[1:10,], caption = "Papers about sars-cov-2 trial vaccine")
```

| PubMed   | Title                                                                                                                                                                              | Jounal                                                                                               | PubDate     | Abstract                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|:---------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------|:------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 34739520 | Is the BNT162b2 COVID-19 vaccine effective in elderly populations? Results from population data from Bavaria, Germany.                                                             | PloS one                                                                                             | 2021        | The efficacy of the BioNTech-Pfizer BNT162b2 vaccination in the elderly (=80 years) could not be fully assessed in the BioNTech-Pfizer trial due to low numbers in this age group. We aimed to evaluate the effectiveness of the BioNTech-Pfizer (BNT162b2) vaccine to prevent SARS-CoV-2 infection and severe outcomes in octo- and novo-generians in a German state setting. A prospective observational study of 708,187 persons aged =80 years living in Bavaria, Germany, was conducted between Jan 9 to Apr 11, 2021. We assessed the vaccine effectiveness (VE) for two doses of the BNT162b2 vaccine with respect to SARS-CoV-2 infection and related hospitalisations and mortality. Additionally, differences in VE by age groups =80 to =89 years and =90 years were studied. Analyses were adjusted by sex. By the end of follow-up, 63.8% of the Bavarian population =80 years had received one dose, and 52.7% two doses, of the BNT162b2 vaccine. Two doses of the BNT162b2 vaccine lowered the proportion of SARS-CoV-2 infections and related outcomes, resulting in VE estimates of 68.3% (95% confidence interval (CI) 65.5%, 70.9%) for infection, 73.2% (95% CI 65.3%, 79.3%) for hospitalisation, and 85.1% (95% CI 80.0%, 89.0%) for mortality. Sex differences in the risk of COVID-19 outcomes observed among unvaccinated persons disappeared after two BNT162b2 vaccine doses. Overall, the BNT162b2 vaccine was equally effective in octo- and novo-genarians. Two doses of BioNTech-Pfizer’s BNT162b2 vaccine is highly effective against COVID-19 outcomes in elderly persons.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| 34739037 | Evaluating the Neutralizing Ability of a CpG-Adjuvanted S-2P Subunit Vaccine Against Severe Acute Respiratory Syndrome Coronavirus 2 (SARS-CoV-2) Variants of Concern.             | Clinical infectious diseases : an official publication of the Infectious Diseases Society of America | 2021 Nov 05 | Variants of concern (VoCs) have the potential to diminish the neutralizing capacity of antibodies elicited by vaccines. MVC-COV1901 is a severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2) vaccine consisting of recombinant prefusion stabilized spike protein S-2P adjuvanted with CpG 1018 and aluminum hydroxide. We explored the effectiveness of MVC-COV1901 against the VoCs. Serum samples were taken from rats and phase 1 clinical trial human subjects immunized with a low, medium, or high dose of MVC-COV1901. The neutralizing titers of serum antibodies were assayed with pseudoviruses coated with the SARS-CoV-2 spike protein of the wild-type (WT), D614G, Alpha, or Beta variants. Rats vaccinated twice with vaccine containing high doses of antigen retained high levels of neutralization activity against the Beta variant, albeit with a slight reduction compared to WT. After the third dose, neutralizing titers against the Beta variant were noticeably enhanced regardless of the amount of antigen used for immunization. In humans, vaccinated phase 1 subjects still showed appreciable neutralization abilities against the D614G, Alpha, and Beta variants, although neutralizing titers were significantly reduced against the Beta variant. Two doses of MVC-COV1901 were able to elicit neutralizing antibodies against SARS-CoV-2 variants with an overall tendency of inducing higher immune response at a higher dose level. The neutralizing titers to the Beta variant in rats and humans were lower than those for WT and the Alpha variant. An additional third dose in rats was able to partially compensate for the reduction in neutralization against the Beta variant. We have demonstrated that immunization with MVC-COV1901 was effective against VoCs. © The Author(s) 2021. Published by Oxford University Press for the Infectious Diseases Society of America.                                                                                                                                                                                                                                                                                                                                                                                                   |
| 34735795 | Immunogenicity of standard and extended dosing intervals of BNT162b2 mRNA vaccine.                                                                                                 | Cell                                                                                                 | 2021 Oct 16 | Extension of the interval between vaccine doses for the BNT162b2 mRNA vaccine was introduced in the United Kingdom to accelerate population coverage with a single dose. At this time, trial data were lacking, and we addressed this in a study of United Kingdom healthcare workers. The first vaccine dose induced protection from infection from the circulating alpha (B.1.1.7) variant over several weeks. In a substudy of 589 individuals, we show that this single dose induces severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2) neutralizing antibody (NAb) responses and a sustained B and T cell response to the spike protein. NAb levels were higher after the extended dosing interval (6-14 weeks) compared with the conventional 3- to 4-week regimen, accompanied by enrichment of CD4+ T cells expressing interleukin-2 (IL-2). Prior SARS-CoV-2 infection amplified and accelerated the response. These data on dynamic cellular and humoral responses indicate that extension of the dosing interval is an effective immunogenic protocol. Copyright © 2021 The Author(s). Published by Elsevier Inc. All rights reserved.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| 34735426 | Effectiveness of 2-Dose Vaccination with mRNA COVID-19 Vaccines Against COVID-19-Associated Hospitalizations Among Immunocompromised Adults - Nine States, January-September 2021. | MMWR. Morbidity and mortality weekly report                                                          | 2021 Nov 05 | Immunocompromised persons, defined as those with suppressed humoral or cellular immunity resulting from health conditions or medications, account for approximately 3% of the U.S. adult population (1). Immunocompromised adults are at increased risk for severe COVID-19 outcomes (2) and might not acquire the same level of protection from COVID-19 mRNA vaccines as do immunocompetent adults (3,4). To evaluate vaccine effectiveness (VE) among immunocompromised adults, data from the VISION Network\* on hospitalizations among persons aged =18 years with COVID-19-like illness from 187 hospitals in nine states during January 17-September 5, 2021 were analyzed. Using selected discharge diagnoses,† VE against COVID-19-associated hospitalization conferred by completing a 2-dose series of an mRNA COVID-19 vaccine =14 days before the index hospitalization date§ (i.e., being fully vaccinated) was evaluated using a test-negative design comparing 20,101 immunocompromised adults (10,564 \[53%\] of whom were fully vaccinated) and 69,116 immunocompetent adults (29,456 \[43%\] of whom were fully vaccinated). VE of 2 doses of mRNA COVID-19 vaccine against COVID-19-associated hospitalization was lower among immunocompromised patients (77%; 95% confidence interval \[CI\] = 74%-80%) than among immunocompetent patients (90%; 95% CI = 89%-91%). This difference persisted irrespective of mRNA vaccine product, age group, and timing of hospitalization relative to SARS-CoV-2 (the virus that causes COVID-19) B.1.617.2 (Delta) variant predominance in the state of hospitalization. VE varied across immunocompromising condition subgroups, ranging from 59% (organ or stem cell transplant recipients) to 81% (persons with a rheumatologic or inflammatory disorder). Immunocompromised persons benefit from mRNA COVID-19 vaccination but are less protected from severe COVID-19 outcomes than are immunocompetent persons, and VE varies among immunocompromised subgroups. Immunocompromised persons receiving mRNA COVID-19 vaccines should receive 3 doses and a booster, consistent with CDC recommendations (5), practice nonpharmaceutical interventions, and, if infected, be monitored closely and considered early for proven therapies that can prevent severe outcomes. |
| 34735018 | COVID-19 Therapeutics and Vaccines: A Race to save Lives.                                                                                                                          | Toxicological sciences : an official journal of the Society of Toxicology                            | 2021 Nov 04 | COVID-19 (Coronavirus Disease 2019), the disease caused by SARS-CoV-2 (Severe Acute Respiratory Syndrome Coronavirus-2) is an ongoing global public health emergency. As understanding of the health effects of COVID-19 have improved, companies and agencies worldwide have worked together to identify therapeutic approaches, fast-track clinical trials and pathways for emergency use, and approve therapies for patients. This work has resulted in therapies that not only improve survival, reduce time of hospitalization and time to recovery, but also include preventative measures, such as vaccines. This manuscript discusses development programs for three products that are approved or authorized for emergency use at the time of writing: VEKLURY (remdesivir, direct acting antiviral from Gilead Sciences, Inc.), REGEN-COV (casirivimab and imdevimab antibody cocktail from Regeneron Pharmaceuticals Inc.) and Comirnaty (Pfizer-BioNTech COVID-19 Vaccine \[Pfizer, Inc.-BioNTech\]), and perspectives from the US Food and Drug Administration (FDA). Published by Oxford University Press 2021.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
| 34729549 | Adverse events of active and placebo groups in SARS-CoV-2 vaccine randomized trials: A systematic review.                                                                          | The Lancet regional health. Europe                                                                   | 2021 Oct 28 | For safety assessment in clinical trials, adverse events (AEs) are reported for the drug under evaluation and compared with AEs in the placebo group. Little is known about the nature of the AEs associated with clinical trials of SARS-CoV-2 vaccines and the extent to which these can be traced to nocebo effects, where negative treatment-related expectations favor their occurrence. In our systematic review, we compared the rates of solicited AEs in the active and placebo groups of SARS-CoV-2 vaccines approved by the Western pharmaceutical regulatory agencies.We implemented a search strategy to identify trial-III studies of SARS-CoV-2 vaccines through the PubMed database. We adopted the PRISMA Statement to perform the study selection and the data collection and identified three trial: two mRNA-based (37590 participants) and one adenovirus type (6736 participants). Relative risks showed that the occurrence of AEs reported in the vaccine groups was higher compared with the placebo groups. The most frequently AEs in both groups were fatigue, headache, local pain, as injection site reactions, and myalgia. In particular, for first doses in placebo recipients, fatigue was reported in 29% and 27% in BNT162b2 and mRNA-1273 groups, respectively, and in 21% of Ad26.COV2.S participants. Headache was reported in 27% in both mRNA groups and in 24% of Ad26.COV2.S recipients. Myalgia was reported in 10% and 14% in mRNA groups (BNT162b2 and mRNA-1273, respectively) and in 13% of Ad26.COV2.S participants. Local pain was reported in 12% and 17% in mRNA groups (BNT162b2 and mRNA-1273, respectively), and in 17% of Ad26.COV2.S recipients. These AEs are more common in the younger population and in the first dose of placebo recipients of the mRNA vaccines. Our results are in agreement with the expectancy theory of nocebo effects and suggest that the AEs associated with COVID-19 vaccines may be related to the nocebo effect. Fondazione CRT - Cassa di Risparmio di Torino, IT (grant number 66346, “GAIA-MENTE” 2019). © 2021 The Authors.                                                                                                                                                                                                                 |
| 34726743 | Analysis of the Effectiveness of the Ad26.COV2.S Adenoviral Vector Vaccine for Preventing COVID-19.                                                                                | JAMA network open                                                                                    | 2021 11 01  | Continuous assessment of the effectiveness and safety of the US Food and Drug Administration-authorized SARS-CoV-2 vaccines is critical to amplify transparency, build public trust, and ultimately improve overall health outcomes. To evaluate the effectiveness of the Johnson & Johnson Ad26.COV2.S vaccine for preventing SARS-CoV-2 infection. <AbstractText Label="Design, Setting, and Participants">This comparative effectiveness research study used large-scale longitudinal curation of electronic health records from the multistate Mayo Clinic Health System (Minnesota, Arizona, Florida, Wisconsin, and Iowa) to identify vaccinated and unvaccinated adults between February 27 and July 22, 2021. The unvaccinated cohort was matched on a propensity score derived from age, sex, zip code, race, ethnicity, and previous number of SARS-CoV-2 polymerase chain reaction tests. The final study cohort consisted of 8889 patients in the vaccinated group and 88 898 unvaccinated matched patients. Single dose of the Ad26.COV2.S vaccine. The incidence rate ratio of SARS-CoV-2 infection in the vaccinated vs unvaccinated control cohorts, measured by SARS-CoV-2 polymerase chain reaction testing. The study was composed of 8889 vaccinated patients (4491 men \[50.5%\]; mean \[SD\] age, 52.4 \[16.9\] years) and 88 898 unvaccinated patients (44 748 men \[50.3%\]; mean \[SD\] age, 51.7 \[16.7\] years). The incidence rate ratio of SARS-CoV-2 infection in the vaccinated vs unvaccinated control cohorts was 0.26 (95% CI, 0.20-0.34) (60 of 8889 vaccinated patients vs 2236 of 88 898 unvaccinated individuals), which corresponds to an effectiveness of 73.6% (95% CI, 65.9%-79.9%) and a 3.73-fold reduction in SARS-CoV-2 infections. This study’s findings are consistent with the clinical trial-reported efficacy of Ad26.COV2.S and the first retrospective analysis, suggesting that the vaccine is effective at reducing SARS-CoV-2 infection, even with the spread of variants such as Alpha or Delta that were not present in the original studies, and reaffirm the urgent need to continue mass vaccination efforts globally.                                                                                                                                                      |
| 34715931 | Lessons from Israel’s COVID-19 Green Pass program.                                                                                                                                 | Israel journal of health policy research                                                             | 2021 10 29  | As of the beginning of March 2021, Israeli law requires the presentation of a Green Pass as a precondition for entering certain businesses and public spheres. Entitlement for a Green Pass is granted to Israelis who have been vaccinated with two doses of COVID-19 vaccine, who have recovered from COVID-19, or who are participating in a clinical trial for vaccine development in Israel. The Green Pass is essential for retaining immune individuals’ freedom of movement and for promoting the public interest in reopening the economic, educational, and cultural spheres of activity. Nonetheless, and as the Green Pass imposes restrictions on the movement of individuals who had not been vaccinated or who had not recovered, it is not consonant with solidarity and trust building. Implementing the Green Pass provision while advancing its effectiveness on the one hand, and safeguarding equality, proportionality, and fairness on the other hand may imbue this measure with ethical legitimacy despite involving a potential breach of trust and solidarity. © 2021. The Author(s).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                         |
| 34713912 | Vaccine development and technology for SARS-CoV-2: current insights.                                                                                                               | Journal of medical virology                                                                          | 2021 Oct 29 | SARS-CoV-2 is associated to a severe respiratory disease in China, that rapidly spread across continents. Since the beginning of the pandemic, available data suggested the asymptomatic transmission and patients were treated with specific drugs with efficacy and safety data not always satisfactory. The aim of this review is to describe the vaccines developed by three companies, Pfizer-BioNTech, Moderna and University of Oxford/AstraZeneca, in terms of both technological and pharmaceutical formulation, safety, efficacy and immunogenicity. A critical analysis of phase 1, 2 and 3 clinical trial results available was conducted, comparing the three vaccine candidates, underlining their similarities and differences. All candidates showed consistent efficacy and tolerability; although some differences can be noted, such as their technological formulation, temperature storage, which will be related to logistics and costs. Further studies will be necessary to evaluate long-term effects and to assess the vaccine safety and efficacy in the general population. This article is protected by copyright. All rights reserved. This article is protected by copyright. All rights reserved.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| 34711598 | BCG vaccination to reduce the impact of COVID-19 in healthcare workers: Protocol for a randomised controlled trial (BRACE trial).                                                  | BMJ open                                                                                             | 2021 10 28  | BCG vaccination modulates immune responses to unrelated pathogens. This off-target effect could reduce the impact of emerging pathogens. As a readily available, inexpensive intervention that has a well-established safety profile, BCG is a good candidate for protecting healthcare workers (HCWs) and other vulnerable groups against COVID-19. This international multicentre phase III randomised controlled trial aims to determine if BCG vaccination reduces the incidence of symptomatic and severe COVID-19 at 6 months (co-primary outcomes) compared with no BCG vaccination. We plan to randomise 10 078 HCWs from Australia, The Netherlands, Spain, the UK and Brazil in a 1:1 ratio to BCG vaccination or no BCG (control group). The participants will be followed for 1 year with questionnaires and collection of blood samples. For any episode of illness, clinical details will be collected daily, and the participant will be tested for SARS-CoV-2 infection. The secondary objectives are to determine if BCG vaccination reduces the rate, incidence, and severity of any febrile or respiratory illness (including SARS-CoV-2), as well as work absenteeism. The safety of BCG vaccination in HCWs will also be evaluated. Immunological analyses will assess changes in the immune system following vaccination, and identify factors associated with susceptibility to or protection against SARS-CoV-2 and other infections. Ethical and governance approval will be obtained from participating sites. Results will be published in peer-reviewed open-access journals. The final cleaned and locked database will be deposited in a data sharing repository archiving system. ClinicalTrials.gov NCT04327206. © Author(s) (or their employer(s)) 2021. Re-use permitted under CC BY. Published by BMJ.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |

Papers about sars-cov-2 trial vaccine

**Text Mining**

Dowload the data

``` r
fn <- "pubmed.csv"
if(!file.exists(fn))
  download.file("https://raw.githubusercontent.com/USCbiostats/data-science-data/master/03_pubmed/pubmed.csv", destfile = fn)

pubmed <- read.csv(fn)
pubmed <- as_tibble(pubmed)
```

1.  Tokenize the abstracts and count the number of each token. Do you
    see anything interesting? Does removing stop words change what
    tokens appear as the most frequent? What are the 5 most common
    tokens for each search term after removing stopwords?

``` r
pubmed %>%
  unnest_tokens(output = token, input = abstract) %>%
  count(token, sort = TRUE) %>%
  top_n(20) %>%
  ggplot(aes(x = n, y = fct_reorder(token, n))) +
  geom_col()
```

    ## Selecting by n

![](README_files/figure-gfm/tokenize%20the%20abstracts-1.png)<!-- -->

There is not much information in the figure because most of the words
are stop words.

``` r
pubmed %>%
  unnest_tokens(output = word, input = abstract) %>%
  count(word, sort = TRUE) %>%
  anti_join(stop_words, by = "word") %>%
  filter(!grepl("^[0-9]+$", x = word)) %>%
  top_n(20) %>%
  ggplot(aes(x = n, y = fct_reorder(word, n))) +
  geom_col()
```

    ## Selecting by n

![](README_files/figure-gfm/remove%20stop%20words-1.png)<!-- -->

After removing the stop words, the most frequent token is “covid”.

``` r
pubmed %>%
  group_by(term) %>%
  unnest_tokens(output = word, input = abstract) %>%
  count(word, sort = TRUE) %>%
  anti_join(stop_words, by = "word") %>%
  filter(!grepl("^[0-9]+$", x = word)) %>%
  top_n(5) %>%
  ggplot(aes(x = n, y = fct_reorder(word, n))) +
  geom_col() +
  facet_wrap(~term)
```

    ## Selecting by n

![](README_files/figure-gfm/tokens%20by%20search%20term-1.png)<!-- -->

The 5 most frequent tokens for search terms are as follow: “covid”,
“pandemic”, “patients”, “disease”, “health”, and “coronavirus” for
search term “covid;”fibrosis“,”cystic“, patients”, “disease”, and “Cf”
for search term “cystic fibrosis”; “patients”, “meningitis”,
“meningeal”, “csf”, and “clinical” for search term “meningitis”; “pre”,
“eclampsia”, “preeclampsia”, “women”, and “pregnancy” for search term
“preeclampsia”; “cancer”, “prostate”, “treatment”, “patients”, and
“disease” for search term “prostate cancer”.

2.  Tokenize the abstracts into bigrams. Find the 10 most common bigram
    and visualize them with ggplot2.

``` r
library(Rcpp)
```

    ## Warning: package 'Rcpp' was built under R version 4.1.1

``` r
pubmed %>%
  unnest_ngrams(output = bigram, input = abstract, n = 2) %>%
  count(bigram, sort = TRUE) %>%
  anti_join(stop_words, by = c("bigram" = "word")) %>%
  filter(!grepl("^[0-9]+$", x = bigram)) %>%
  top_n(10) %>%
  ggplot(aes(x = n, y = fct_reorder(bigram, n))) +
  geom_col()
```

    ## Selecting by n

![](README_files/figure-gfm/unnamed-chunk-1-1.png)<!-- -->

3.  Calculate the TF-IDF value for each word-search term combination.
    (here you want the search term to be the “document”) What are the 5
    tokens from each search term with the highest TF-IDF value? How are
    the results different from the answers you got in question 1?

The 5 tokens from each search term with the highest TF-IDF value are in
the following table:

``` r
pubmed %>%
  unnest_tokens(abstract, abstract) %>%
  group_by(term) %>%
  count(abstract, term) %>%
  bind_tf_idf(abstract, term, n) %>%
  top_n(5) %>%
  arrange(desc(tf_idf)) %>%
  knitr::kable()
```

    ## Selecting by tf_idf

| term            | abstract        |    n |        tf |       idf |   tf\_idf |
|:----------------|:----------------|-----:|----------:|----------:|----------:|
| covid           | covid           | 7275 | 0.0371050 | 1.6094379 | 0.0597183 |
| prostate cancer | prostate        | 3832 | 0.0311890 | 1.6094379 | 0.0501967 |
| preeclampsia    | eclampsia       | 2005 | 0.0142784 | 1.6094379 | 0.0229802 |
| preeclampsia    | preeclampsia    | 1863 | 0.0132672 | 1.6094379 | 0.0213527 |
| meningitis      | meningitis      |  429 | 0.0091942 | 1.6094379 | 0.0147974 |
| cystic fibrosis | cf              |  625 | 0.0127188 | 0.9162907 | 0.0116541 |
| cystic fibrosis | fibrosis        |  867 | 0.0176435 | 0.5108256 | 0.0090127 |
| cystic fibrosis | cystic          |  862 | 0.0175417 | 0.5108256 | 0.0089608 |
| meningitis      | meningeal       |  219 | 0.0046935 | 1.6094379 | 0.0075539 |
| covid           | pandemic        |  800 | 0.0040803 | 1.6094379 | 0.0065670 |
| covid           | coronavirus     |  647 | 0.0032999 | 1.6094379 | 0.0053110 |
| meningitis      | pachymeningitis |  149 | 0.0031933 | 1.6094379 | 0.0051394 |
| meningitis      | csf             |  206 | 0.0044149 | 0.9162907 | 0.0040453 |
| prostate cancer | androgen        |  305 | 0.0024824 | 1.6094379 | 0.0039953 |
| prostate cancer | psa             |  282 | 0.0022952 | 1.6094379 | 0.0036940 |
| meningitis      | meninges        |  106 | 0.0022718 | 1.6094379 | 0.0036562 |
| preeclampsia    | pregnancy       |  969 | 0.0069006 | 0.5108256 | 0.0035250 |
| covid           | sars            |  372 | 0.0018973 | 1.6094379 | 0.0030536 |
| preeclampsia    | maternal        |  797 | 0.0056757 | 0.5108256 | 0.0028993 |
| cystic fibrosis | cftr            |   86 | 0.0017501 | 1.6094379 | 0.0028167 |
| prostate cancer | prostatectomy   |  215 | 0.0017499 | 1.6094379 | 0.0028164 |
| covid           | cov             |  334 | 0.0017035 | 1.6094379 | 0.0027417 |
| cystic fibrosis | sweat           |   83 | 0.0016891 | 1.6094379 | 0.0027184 |
| preeclampsia    | gestational     |  191 | 0.0013602 | 1.6094379 | 0.0021891 |
| prostate cancer | castration      |  148 | 0.0012046 | 1.6094379 | 0.0019387 |

There are some tokens not in the question 1, such as “sweat”,
“gestional”, “castration”, “sars”, “psa”, “androgen”, “pachymeningitis”.
The tokens in this question seems more relevant and reasonable to the
search terms, compared the tokens in question 1.
