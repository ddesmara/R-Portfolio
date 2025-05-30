
# BV20_Families Data Analysis

**Dominique Desmarattes**
**May 31, 2021**

## Introduction
In this study, we are analyzing the genetic evolution of a lepidopteran species *Helicaverpa zea (H.zea)* and their resistance to transgenic Bt crops. The goal of the project is to understand how this lepidopteran species has evolved to become resistant to Cry toxins produced by Bt-expressing corn. This species was analyzed under four different treatment groups and was compared to a control group that did not have any resistance to the toxins.

The four treatment groups are:

* Obsession Control (OCL)
* Obsession Diagnostic Dose (ODD)
* Providence Control (PCL)
* Providence Diagnostic Dose (PDD)

The control group that is being compared to all these treatments is Benzon. Benzon was also split into four treatment groups to properly compare itself with the experimental group.

Terms to know:

* Preweight/Set Assay - The weight of 4 caterpillars together. All of the individuals that were loaded into the same square would have all been weighed together. The actual weight of individuals is much smaller. These caterpillars were weighed together because an individual larva was too small to obtain a weight on the scale. These  In the BV20_Families data frame, the four caterpillars will share the same weight and the column name is X4.Caterpillar.Weight.

* Postweight/Scored Assay - The weight of the larva after 7 days of growth. Each larva were weighed individually because we were now able to obtain a weight from each one on the scale, and we were able to obtain more data by weighing each larva individually. In the BV20_Families data frame, the column name for the postweight is called Individual Larva Weight.

* Intercross - A family of larvae that was derived from a field-collected larva, that has resistance to the Cry toxins, was mated with a Benzon control, this would be the P1 generation. The offsprings from that mating (F1 generation) were then mated with one another and produced offspring of their own which will be the F2 generation. The F2 generation are the larvae that were weighed for our experiment.

* Backcross - A family of larvae that was derived from an F2 generation larva male mating with a Benzon female. By crossing an F2 larva with a Benzon larva, the offsprings produced from this cross will have more genes that are susceptible to the Cry toxins.

## Getting Started with R

To begin the analysis, all the necessary packages must be installed.
```{r}
library(tidyverse)
library(janitor)
library(tidyr)
library(data.table)
dfnames = list.files(pattern="*BV20")
for (i in 1:length(dfnames)){
  assign(dfnames[i], read.csv(dfnames[i]))
}
```
All the CSV files that contains the preweight and postweight of each family is retrieved by creating a for loop.
```{r}
dfnames = list.files(pattern="*BV20")
for (i in 1:length(dfnames)){
  assign(dfnames[i], read.csv(dfnames[i]))
}
```
After retrieving all CSV files needed for the analysis, I made sure to exclude any NA values within the necessary data frames. Preweight data frames were saved as "F2 Set Assays" while the postweight data frames were saved as "Individual Larva Weight" (ILW).

```{r}
`ILW-BV20-G7-O4BZF-C3.csv` <- `ILW-BV20-G7-O4BZF-C3.csv`%>%
  select_if(~any(!is.na(.))) %>%
  drop_na()
`ILW_BV20_AA27_O8_BZF_14_BZF.csv` <- `ILW_BV20_AA27_O8_BZF_14_BZF.csv`%>%
  select_if(~any(!is.na(.))) %>%
  drop_na()
`F2 Set Assays 2-BV20 EH138 P1 BZF E5.csv` <- `F2 Set Assays 2-BV20 EH138 P1 BZF E5.csv` %>%
  select_if(~any(!is.na(.)))
`F2 Set Assays 2-BV20 EV152 P8 BZF 13 BZF.csv` <- `F2 Set Assays 2-BV20 EV152 P8 BZF 13 BZF.csv` %>%
  select_if(~any(!is.na(.)))
```
Next, create one large data frame that combines the preweight data with the postweight data. To do so, I renamed each CSV file in order to properly match the preweight of one family with the postweight of the same family. I merged the preweight and postweight data frames by Tray, Square, Row, and Treatment so that the data frames can line up with one another properly. After the preweight and postweight data frames merged, some families had extra columns that prevented R from combining all the families when using the rbind function, so the columns were removed so that each data frame had the same number of columns to bind properly. The large data frame was named BV20_families.

```{r}
Preweight1 <- `F2 Set Assays 2-BV20 AA27 O1 BZF D4.csv`
Postweight1 <- `ILW-BV20-AA27-O1BZF-D4.csv`
BV20_AA27_O1_BZF_D4 <- merge(Preweight1, Postweight1, c("Tray","Square","Row","Treatment"))

Preweight2 <- `F2 Set Assays 2-BV20 AA27 O8 BZF 14 BZF.csv`
Postweight2 <- `ILW_BV20_AA27_O8_BZF_14_BZF.csv`
BV20_AA27_O8_BZF_14_BZF <- merge(Preweight2, Postweight2, c("Tray","Square","Row","Treatment"))

Preweight3 <- `F2 Set Assays 2-BV20 AA27 O8 BZF A1.csv`
Postweight3 <- `ILW-BV20-AA27-O8BZF-A1.csv`
BV20_AA27_O8_BZF_A1 <- merge(Preweight3, Postweight3, c("Tray","Square","Row","Treatment"))

Preweight4 <- `F2 Set Assays 2-BV20 AA27 P6 BZM C3.csv`
Postweight4 <- `ILW-BV20-AA27-P6BZM-C3.csv`
BV20_AA27_P6_BZM_C3 <- merge(Preweight4, Postweight4, c("Tray","Square","Row","Treatment"))
BV20_AA27_P6_BZM_C3 <- BV20_AA27_P6_BZM_C3 %>%
  select_if(~any(!is.na(.))) %>%
  drop_na()

Preweight5 <- `F2 Set Assays 2-BV20 AA27 P8 BZM E5.csv`
Postweight5 <- `ILW-BV20-AA27-P8BZM-E5.csv`
BV20_AA27_P8_BZM_E5 <- merge(Preweight5, Postweight5, c("Tray","Square","Row","Treatment"))

Prewieght6 <- `F2 Set Assays 2-BV20 BP68 O8 BZM C3.csv`
Postweight6 <- `ILW-BV20-BP68-08BZM-C3.csv`
BV20_BP68_O8_BZM_C3 <- merge(Prewieght6, Postweight6, c("Tray","Square","Row","Treatment"))

Preweight7 <- `F2 Set Assays 2-BV20 EH138 P1 BZF 10 BZF.csv`
Postweight7 <- `ILW-BV20-EH138-P1BZF-10BZF.csv`
BV20_EH138_P1_BZF_10BZF <- merge(Preweight7, Postweight7, c("Tray","Square","Row","Treatment"))

Preweight8 <- `F2 Set Assays 2-BV20 EH138 P1 BZF E5.csv`
Postweight8 <- `ILW-BV20-EH138-P1BZF-E5.csv`
BV20_EH138_P1_BZF_E5 <- merge(Preweight8, Postweight8, c("Tray","Square","Row","Treatment"))

Preweight9 <- `F2 Set Assays 2-BV20 EI139 P5 BZM A1.csv`
Postweight9 <- `ILW-BV20-EI139-P5BZM-A1.csv`
BV20_EI139_P5BZM_A1 <- merge(Preweight9, Postweight9, c("Tray","Square","Row","Treatment"))

Preweight10 <- `F2 Set Assays 2-BV20 EV152 P8 BZF 13 BZF.csv`
Postweight10 <- `ILW-BV20-EV152-P8BZF-13BZF.csv`
BV20_EV152_P8BZF_13BZF <- merge(Preweight10, Postweight10, c("Tray","Square","Row","Treatment"))

Preweight11 <- `F2 Set Assays 2-BV20 EV152 P8 BZF 19.csv`
Postweight11 <- `ILW-BV20-EV152-P8BZF-19.csv`
BV20_EV152_P8BZF_19 <- merge(Preweight11, Postweight11, c("Tray","Square","Row","Treatment"))

Preweight12 <- `F2 Set Assays 2-BV20 G7 O4 BZF C3.csv`
Postweight12 <- `ILW-BV20-G7-O4BZF-C3.csv`
BV20_G7_O4BZF_C3 <- merge(Preweight12, Postweight12, c("Tray","Square","Row","Treatment"))

Preweight13 <- `F2 Set Assays 2-BV20 GI 191 O12 BZF E5.csv`
Postweight13 <- `ILW_BV20_GI_191_O12_BZF_E5.csv`
BV20_GI191_O12_BZF_E5 <- merge(Preweight13, Postweight13, c("Tray","Square","Row","Treatment"))

Preweight14 <- `F2 Set Assays 2-BV20 GI 191 P4 BZF B2.csv`
Postweight14 <- `ILW_BV20_GI_191_P4_BZF_B2.csv`
BV20_GI191_P4_BZF_B2 <- merge(Preweight14, Postweight14, c("Tray","Square","Row","Treatment"))

BV20_EH138_P1_BZF_10BZF <- subset(BV20_EH138_P1_BZF_10BZF, select = -c(X, Population.x))

BV20_EI139_P5BZM_A1 <- subset(BV20_EI139_P5BZM_A1, select = -c(X, Population.x))

BV20_EV152_P8BZF_13BZF <- subset(BV20_EV152_P8BZF_13BZF, select = -c(X, Population.x))

BV20_AA27_O1_BZF_D4 <- subset(BV20_AA27_O1_BZF_D4, select= -c(Population.x))

BV20_AA27_O8_BZF_14_BZF <- subset(BV20_AA27_O8_BZF_14_BZF, select= -c(X, X.1, X.2, X.3, X.4, X.5, X.6, X.7, X.8, X.9, X.10, X.11, X.12, X.13, X.14, X.15, Population.x))

BV20_AA27_O8_BZF_A1 <- subset(BV20_AA27_O8_BZF_A1, select= -c(Population.x))

BV20_AA27_P6_BZM_C3 <- subset(BV20_AA27_P6_BZM_C3, select= -c(Population.x))

BV20_AA27_P8_BZM_E5 <- subset(BV20_AA27_P8_BZM_E5, select= -c(Population.x))

BV20_BP68_O8_BZM_C3 <- subset(BV20_BP68_O8_BZM_C3, select= -c(Population.x))

BV20_EH138_P1_BZF_E5 <- subset(BV20_EH138_P1_BZF_E5, select= -c(Population.x))

BV20_EV152_P8BZF_19 <- subset(BV20_EV152_P8BZF_19, select= -c(Population.x))

BV20_G7_O4BZF_C3 <- subset(BV20_G7_O4BZF_C3, select= -c(Population.x, Notes.x))

BV20_GI191_O12_BZF_E5<- subset(BV20_GI191_O12_BZF_E5, select = -c(Population.x))

BV20_GI191_P4_BZF_B2 <- subset(BV20_GI191_P4_BZF_B2, select = -c(Population.x))

BV20_Families <- rbindlist(list(BV20_AA27_O1_BZF_D4, BV20_AA27_O8_BZF_14_BZF, BV20_AA27_O8_BZF_A1, BV20_AA27_P6_BZM_C3, BV20_AA27_P8_BZM_E5, BV20_BP68_O8_BZM_C3, BV20_EH138_P1_BZF_10BZF, BV20_EH138_P1_BZF_E5, BV20_EI139_P5BZM_A1, BV20_EV152_P8BZF_13BZF, BV20_EV152_P8BZF_19, BV20_G7_O4BZF_C3))

BV20_Families <- BV20_Families %>%
  rename(
    Population = Population.y
  )
```
After creating the large data frame, two new columns were added to identify the diet strain and cross type of each larva. Diet strain is whether the larva is a part of the Obsession strain or the Providence strain. Cross type identifies whether the larva is from a backcross or an intercross. Backcrosses can be identified because the family name ends with BZF. The mutate function is used to add new variables.

```{r}
BV20_Families <- BV20_Families%>%
  mutate(
    Diet.Strain = case_when(
      Treatment == "ODD" ~ "O",
      Treatment == "OCL" ~ "O",
      Treatment == "PDD" ~ "P",
      Treatment == "PCL" ~ "P"
    )
  )%>%
      mutate(
        Cross.Type = case_when(
          Population == "BV20-AA27-O1BZF-D4" ~ "Intercross",
          Population == "BV20-AA27-O8BZF-14BZF" ~ "Backcross",
          Population == "BV20-AA27-O8BZF-A1" ~ "Intercross",
          Population == "BV20-AA27-P6BZM-C3" ~ "Intercross",
          Population == "BV20-AA27-P8BZM-E5" ~ "Intercross",
          Population == "BV20-BP68-08BZM-C3" ~ "Intercross",
          Population == "BV20-EH138-P1BZF-10BZF" ~ "Backcross",
          Population == "BV20-EH138-P1BZF-E5" ~ "Intercross",
          Population == "BV20-EI139-P5BZM-A1" ~ "Intercross",
          Population == "BV20-EV152-P8BZF-13BZF" ~ "Backcross",
          Population == "BV20-EV152-P8BZF-19" ~ "Intercross",
          Population == "BV20-G7-O4BZF-C3" ~ "Intercross"
        )
      )
```
The mutate function has been used multiple times during this project to add new variables that we can compare with the larval end weights. A new column was created to include the Parent Weights.

```{r}
BV20_Families <- BV20_Families%>%
  mutate(
    Parent.Weight = case_when(
          Population == "BV20-AA27-O1BZF-D4" ~ "0.0929",
          Population == "BV20-AA27-O8BZF-14BZF" ~ "0.0636",
          Population == "BV20-AA27-O8BZF-A1" ~ "0.0636",
          Population == "BV20-AA27-P6BZM-C3" ~ "0.1127",
          Population == "BV20-AA27-P8BZM-E5" ~ "0.1093",
          Population == "BV20-BP68-08BZM-C3" ~ "0.0691",
          Population == "BV20-EH138-P1BZF-10BZF" ~ "0.0812",
          Population == "BV20-EH138-P1BZF-E5" ~ "0.0812",
          Population == "BV20-EI139-P5BZM-A1" ~ "0.0962",
          Population == "BV20-EV152-P8BZF-13BZF" ~ "0.0969",
          Population == "BV20-EV152-P8BZF-19" ~ "0.0969",
          Population == "BV20-G7-O4BZF-C3" ~ "0.0589"
    )
  ) 
```
To calculate the mean values of the end weights, the group_by function was used to group the population and treatment together and display the means based on those two variables.
```{r}
BV20_Means <- group_by(BV20_Families,Population,Treatment)%>%
summarize(mean = mean(as.numeric(Individual.Larva.Weight), na.rm = TRUE), n = n())

BV20_Means <- BV20_Means %>% filter_all(all_vars(!is.na(.)))
```
To display a data table of the survivorship of larvae in each family, the group_by and summarise functions helped create a chart with the number of larvae that survived the experiment under each treatment.
```{r}
BV20_Survivors <- BV20_Families%>%
  filter(Individual.Larva.Weight != "dead no eating"|Individual.Larva.Weight != "dead some eating"|Individual.Larva.Weight != "dead lots of eating"|Individual.Larva.Weight != "dead lots eating"|Individual.Larva.Weight != "dead"|Individual.Larva.Weight !="large; dead"|Individual.Larva.Weight !=" ")%>%
  group_by(Population,Treatment)%>%
  summarise(n= n())
```
The BV20_families data frame was edited to exclude the Benzon family data.
```{r}
BV20_Families <- filter(BV20_Families, Population != "Benzon")
```
To understand the amount of larva that didn't survive the duration of the experiment, a table was created to identify the mortality in each family, separated by treatment. This helps identify how much each treatment affected the survival of the larvae.
```{r}
BV20_Mortality <- BV20_Families%>%
  filter(Individual.Larva.Weight == "dead no eating"|Individual.Larva.Weight == "dead some eating"|Individual.Larva.Weight == "dead lots of eating"|Individual.Larva.Weight == "dead lots eating"|Individual.Larva.Weight == "dead"|Individual.Larva.Weight =="large; dead")%>%
  group_by(Population,Treatment)%>%
  summarise(n= n())
```
Removing NA columns in a scatter plot (graph with NA column).
```{r}
BV20_Families%>%
  filter(Treatment == "PCL")%>%
  select(Population, X4.Caterpillar.Weight, Individual.Larva.Weight)%>%
  ggplot(aes(x= X4.Caterpillar.Weight, y= as.numeric(Individual.Larva.Weight))) + geom_point(position = position_jitter(width = 0.5), alpha=0.5) + labs(x= "Larva Preweight (g)", y= "Larva Postweight (g)", title= "Larva Weight Scatterplot for BV20 Families-PCL Treatment") + geom_smooth(method= lm, se= F) + theme(axis.text.x = element_text(angle = 90))
```

Graph with NA column omitted. To remove the NA column, I piped a filter command to filter out any blank values in the preweight column (named X4.Caterpillar.Weight).
```{r}
BV20_Families%>%
  filter(Treatment == "PCL")%>%
  filter(X4.Caterpillar.Weight != "")%>%
  select(Population, X4.Caterpillar.Weight, Individual.Larva.Weight)%>%
  ggplot(aes(x= X4.Caterpillar.Weight, y= as.numeric(Individual.Larva.Weight))) + geom_point(position = position_jitter(width = 0.5), alpha=0.5) + labs(x= "Larva Preweight (g)", y= "Larva Postweight (g)", title= "Larva Weight Scatterplot for BV20 Families-PCL Treatment") + geom_smooth(method= lm, se= F) + theme(axis.text.x = element_text(angle = 90))
```
