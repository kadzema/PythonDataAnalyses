
# Academy of Py
- Average math and reading scores remained relativey static over grade at each school
- Higher per student budget does not have a positive impact on math and reading scores. In fact, two of the highest performing schools spent the least per student.
- School size appears to have an effect on scores. Small and Medium sized schools (less than 2001 students) out-performed larger schools.


```python
import pandas as pd
import numpy as np
```


```python
schoolFile = "resources/schools_complete.csv"
studentFile = "resources/students_complete.csv"
school = pd.read_csv(schoolFile)
student = pd.read_csv(studentFile)
# rename the column "name" in school df - the "name" column in the student df is the student name
school.rename(columns={"name":"school"}, inplace=True)
```

##### District Summary


```python
# Total Schools
tSchoolCount = school["school"].nunique()
# Total Students
tStudentCount = student["Student ID"].nunique()
# Total Budget
tBudget = school["budget"].sum()
# Average Math Score
tAvgMath = student["math_score"].mean()
# Average Reading Score
tAvgReading = student["reading_score"].mean()
# % Passing Math
tPassMath = (student[student["math_score"]>=70]["Student ID"].count()/student["Student ID"].count()) * 100
# % Passing Reading
tPassReading = (student[student["reading_score"]>=70]["Student ID"].count()/student["Student ID"].count()) * 100
# Overall Passing Rate (Average of the above two)
# tOverall = np.mean([tPassMath,tPassReading])
tOverall = (student[(student["reading_score"]>=70)&(student["math_score"]>=70)]["Student ID"].count()/student["Student ID"].count()) * 100

keyMetrics = pd.DataFrame({"Total Schools":[tSchoolCount],"Total Students":[tStudentCount],
                           "Total Budget":[tBudget],"Average Math Score":[tAvgMath],
                          "Average Reading Score":[tAvgReading],"% Passing Math":[tPassMath],
                          "% Passing Reading":[tPassReading],"Overall Passing Rate":[tOverall]})
keyMetrics["Total Students"] = keyMetrics["Total Students"]
keyMetrics = keyMetrics[["Total Schools","Total Students","Total Budget","Average Math Score","Average Reading Score","% Passing Math","% Passing Reading","Overall Passing Rate"]]
keyMetrics["Total Students"] = keyMetrics["Total Students"].map("{:,}".format)
keyMetrics["Total Budget"] = keyMetrics["Total Budget"].map("${:,}".format)
keyMetrics["Average Math Score"] = keyMetrics["Average Math Score"].map("{:.0f}%".format)
keyMetrics["Average Reading Score"] = keyMetrics["Average Reading Score"].map("{:.0f}%".format)
keyMetrics["% Passing Math"] = keyMetrics["% Passing Math"].map("{:.0f}%".format)
keyMetrics["% Passing Reading"] = keyMetrics["% Passing Reading"].map("{:.0f}%".format)
keyMetrics["Overall Passing Rate"] = keyMetrics["Overall Passing Rate"].map("{:.0f}%".format)
keyMetrics
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Schools</th>
      <th>Total Students</th>
      <th>Total Budget</th>
      <th>Average Math Score</th>
      <th>Average Reading Score</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>Overall Passing Rate</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>15</td>
      <td>39,170</td>
      <td>$24,649,428</td>
      <td>79%</td>
      <td>82%</td>
      <td>75%</td>
      <td>86%</td>
      <td>65%</td>
    </tr>
  </tbody>
</table>
</div>



##### School Summary


```python
# Create an overview table that summarizes key metrics about each school, including:
# School Name
# School Type
# Total Students
# Total School Budget
schoolSummary = school.copy()
# Per Student Budget
schoolSummary["Budget Per Student"] = schoolSummary["budget"]/schoolSummary["size"]
# Average Math Score
schoolAvgMath = student.groupby("school")["math_score"].mean().to_frame("AvgMath")
# Average Reading Score
schoolAvgReading = student.groupby("school")["reading_score"].mean().to_frame("AvgReading")
# % Passing Math
schoolPassMath = ((student[student["math_score"]>=70].groupby("school")["Student ID"].count()/student.groupby("school")["Student ID"].count())*100).to_frame("%PassMath")
# % Passing Reading
schoolPassReading = ((student[student["reading_score"]>=70].groupby("school")["Student ID"].count()/student.groupby("school")["Student ID"].count())*100).to_frame("%PassReading")
schoolPassOverall = ((student[(student["reading_score"]>=70) & (student["math_score"]>=70)].groupby("school")["Student ID"].count()/student.groupby("school")["Student ID"].count())*100).to_frame("%PassOverall")
# Overall Passing Rate (Average of the above two)
schoolMathReading = schoolPassMath.merge(schoolPassReading, left_index=True, right_index=True).merge(schoolPassOverall, left_index=True, right_index=True)
# schoolMathReading["Overall"] = (schoolMathReading["%PassMath"]+schoolMathReading["%PassReading"])/2
schoolSummaryMerge = schoolSummary.merge(schoolAvgMath, left_on="school", right_index=True).merge(schoolAvgReading, left_on="school", right_index=True).merge(schoolMathReading, left_on="school", right_index=True)
# keep a copy of unformatted data for later manipulation
schoolSummaryFormatted = schoolSummaryMerge.copy()
schoolSummaryFormatted["size"] = schoolSummaryFormatted["size"].map("{:,}".format)
schoolSummaryFormatted["budget"]= schoolSummaryFormatted["budget"].map("${:,}".format)
schoolSummaryFormatted["Budget Per Student"] = schoolSummaryFormatted["Budget Per Student"].map("${:,.0f}".format)
schoolSummaryFormatted["AvgMath"] = schoolSummaryFormatted["AvgMath"].map("{:.0f}".format)
schoolSummaryFormatted["AvgReading"] = schoolSummaryFormatted["AvgReading"].map("{:.0f}".format)
schoolSummaryFormatted["%PassMath"]=schoolSummaryFormatted["%PassMath"].map("{:.0f}".format)
schoolSummaryFormatted["%PassReading"]=schoolSummaryFormatted["%PassReading"].map("{:.0f}".format)
schoolSummaryFormatted["%PassOverall"]=schoolSummaryFormatted["%PassOverall"].map("{:.0f}".format)
schoolSummaryFormatted
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Budget Per Student</th>
      <th>AvgMath</th>
      <th>AvgReading</th>
      <th>%PassMath</th>
      <th>%PassReading</th>
      <th>%PassOverall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Huang High School</td>
      <td>District</td>
      <td>2,917</td>
      <td>$1,910,635</td>
      <td>$655</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>54</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2,949</td>
      <td>$1,884,411</td>
      <td>$639</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>53</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>Shelton High School</td>
      <td>Charter</td>
      <td>1,761</td>
      <td>$1,056,600</td>
      <td>$600</td>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>96</td>
      <td>90</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4,635</td>
      <td>$3,022,020</td>
      <td>$652</td>
      <td>77</td>
      <td>81</td>
      <td>67</td>
      <td>81</td>
      <td>54</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1,468</td>
      <td>$917,500</td>
      <td>$625</td>
      <td>83</td>
      <td>84</td>
      <td>93</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2,283</td>
      <td>$1,319,574</td>
      <td>$578</td>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1,858</td>
      <td>$1,081,356</td>
      <td>$582</td>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>Bailey High School</td>
      <td>District</td>
      <td>4,976</td>
      <td>$3,124,928</td>
      <td>$628</td>
      <td>77</td>
      <td>81</td>
      <td>67</td>
      <td>82</td>
      <td>55</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>Holden High School</td>
      <td>Charter</td>
      <td>427</td>
      <td>$248,087</td>
      <td>$581</td>
      <td>84</td>
      <td>84</td>
      <td>93</td>
      <td>96</td>
      <td>89</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858</td>
      <td>$609</td>
      <td>84</td>
      <td>84</td>
      <td>95</td>
      <td>96</td>
      <td>91</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>Wright High School</td>
      <td>Charter</td>
      <td>1,800</td>
      <td>$1,049,400</td>
      <td>$583</td>
      <td>84</td>
      <td>84</td>
      <td>93</td>
      <td>97</td>
      <td>90</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3,999</td>
      <td>$2,547,363</td>
      <td>$637</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>80</td>
      <td>53</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4,761</td>
      <td>$3,094,650</td>
      <td>$650</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>54</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>Ford High School</td>
      <td>District</td>
      <td>2,739</td>
      <td>$1,763,916</td>
      <td>$644</td>
      <td>77</td>
      <td>81</td>
      <td>68</td>
      <td>79</td>
      <td>54</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1,635</td>
      <td>$1,043,130</td>
      <td>$638</td>
      <td>83</td>
      <td>84</td>
      <td>93</td>
      <td>97</td>
      <td>91</td>
    </tr>
  </tbody>
</table>
</div>



##### Top Performing Schools (By Passing Rate)


```python
schoolSummaryFormatted.sort_values(by="%PassOverall", ascending=False).head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Budget Per Student</th>
      <th>AvgMath</th>
      <th>AvgReading</th>
      <th>%PassMath</th>
      <th>%PassReading</th>
      <th>%PassOverall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>Griffin High School</td>
      <td>Charter</td>
      <td>1,468</td>
      <td>$917,500</td>
      <td>$625</td>
      <td>83</td>
      <td>84</td>
      <td>93</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>Wilson High School</td>
      <td>Charter</td>
      <td>2,283</td>
      <td>$1,319,574</td>
      <td>$578</td>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>Cabrera High School</td>
      <td>Charter</td>
      <td>1,858</td>
      <td>$1,081,356</td>
      <td>$582</td>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>Pena High School</td>
      <td>Charter</td>
      <td>962</td>
      <td>$585,858</td>
      <td>$609</td>
      <td>84</td>
      <td>84</td>
      <td>95</td>
      <td>96</td>
      <td>91</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>Thomas High School</td>
      <td>Charter</td>
      <td>1,635</td>
      <td>$1,043,130</td>
      <td>$638</td>
      <td>83</td>
      <td>84</td>
      <td>93</td>
      <td>97</td>
      <td>91</td>
    </tr>
  </tbody>
</table>
</div>



##### Lowest Performing Schools (By Passing Rate)


```python
schoolSummaryFormatted.sort_values(by="%PassOverall").head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>School ID</th>
      <th>school</th>
      <th>type</th>
      <th>size</th>
      <th>budget</th>
      <th>Budget Per Student</th>
      <th>AvgMath</th>
      <th>AvgReading</th>
      <th>%PassMath</th>
      <th>%PassReading</th>
      <th>%PassOverall</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>Figueroa High School</td>
      <td>District</td>
      <td>2,949</td>
      <td>$1,884,411</td>
      <td>$639</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>53</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>Rodriguez High School</td>
      <td>District</td>
      <td>3,999</td>
      <td>$2,547,363</td>
      <td>$637</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>80</td>
      <td>53</td>
    </tr>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>Huang High School</td>
      <td>District</td>
      <td>2,917</td>
      <td>$1,910,635</td>
      <td>$655</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>54</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>Hernandez High School</td>
      <td>District</td>
      <td>4,635</td>
      <td>$3,022,020</td>
      <td>$652</td>
      <td>77</td>
      <td>81</td>
      <td>67</td>
      <td>81</td>
      <td>54</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>Johnson High School</td>
      <td>District</td>
      <td>4,761</td>
      <td>$3,094,650</td>
      <td>$650</td>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>54</td>
    </tr>
  </tbody>
</table>
</div>



##### Math Scores by Grade


```python
# Create a table that lists the average Math Score for students of each grade level (9th, 10th, 11th, 12th) at each school.

math = student.groupby(["school","grade"])["math_score"].mean().to_frame("AvgMathScore")
math = pd.pivot_table(math, values="AvgMathScore", index="school",columns=["grade"],aggfunc=np.mean)
math.columns = ["9th","10th","11th","12th"]
math["9th"] = math["9th"].map("{:.0f}".format)
math["10th"] = math["10th"].map("{:.0f}".format)
math["11th"] = math["11th"].map("{:.0f}".format)
math["12th"] = math["12th"].map("{:.0f}".format)
math
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>77</td>
      <td>78</td>
      <td>76</td>
      <td>77</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>83</td>
      <td>83</td>
      <td>83</td>
      <td>83</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>77</td>
      <td>77</td>
      <td>77</td>
      <td>76</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>78</td>
      <td>77</td>
      <td>76</td>
      <td>77</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>84</td>
      <td>84</td>
      <td>83</td>
      <td>82</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>77</td>
      <td>77</td>
      <td>77</td>
      <td>77</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83</td>
      <td>85</td>
      <td>83</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>76</td>
      <td>76</td>
      <td>77</td>
      <td>77</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>77</td>
      <td>77</td>
      <td>77</td>
      <td>77</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>83</td>
      <td>84</td>
      <td>84</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>77</td>
      <td>76</td>
      <td>78</td>
      <td>77</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83</td>
      <td>83</td>
      <td>84</td>
      <td>83</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>83</td>
      <td>83</td>
      <td>83</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>84</td>
      <td>83</td>
      <td>83</td>
      <td>83</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>84</td>
      <td>84</td>
      <td>84</td>
      <td>83</td>
    </tr>
  </tbody>
</table>
</div>



##### Reading Scores by Grade


```python
reading = student.groupby(["school","grade"])["reading_score"].mean().to_frame("AvgReadingScore")
reading = pd.pivot_table(reading, index="school", columns="grade", values="AvgReadingScore")
reading["9th"] = reading["9th"].map("{:.0f}".format)
reading["10th"] = reading["10th"].map("{:.0f}".format)
reading["11th"] = reading["11th"].map("{:.0f}".format)
reading["12th"] = reading["12th"].map("{:.0f}".format)
reading.columns = ["9th","10th","11th","12th"]
reading
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>9th</th>
      <th>10th</th>
      <th>11th</th>
      <th>12th</th>
    </tr>
    <tr>
      <th>school</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Bailey High School</th>
      <td>81</td>
      <td>81</td>
      <td>81</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Cabrera High School</th>
      <td>84</td>
      <td>84</td>
      <td>84</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Figueroa High School</th>
      <td>81</td>
      <td>81</td>
      <td>81</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Ford High School</th>
      <td>81</td>
      <td>80</td>
      <td>81</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Griffin High School</th>
      <td>84</td>
      <td>84</td>
      <td>84</td>
      <td>83</td>
    </tr>
    <tr>
      <th>Hernandez High School</th>
      <td>81</td>
      <td>81</td>
      <td>81</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Holden High School</th>
      <td>83</td>
      <td>84</td>
      <td>85</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Huang High School</th>
      <td>82</td>
      <td>81</td>
      <td>80</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Johnson High School</th>
      <td>81</td>
      <td>81</td>
      <td>81</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Pena High School</th>
      <td>84</td>
      <td>84</td>
      <td>85</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Rodriguez High School</th>
      <td>81</td>
      <td>81</td>
      <td>80</td>
      <td>81</td>
    </tr>
    <tr>
      <th>Shelton High School</th>
      <td>83</td>
      <td>84</td>
      <td>83</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Thomas High School</th>
      <td>84</td>
      <td>84</td>
      <td>84</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Wilson High School</th>
      <td>84</td>
      <td>84</td>
      <td>84</td>
      <td>84</td>
    </tr>
    <tr>
      <th>Wright High School</th>
      <td>84</td>
      <td>84</td>
      <td>84</td>
      <td>84</td>
    </tr>
  </tbody>
</table>
</div>



##### Scores by School Spending


```python
# Create a table that breaks down school performances based on average Spending Ranges (Per Student). Use 4 reasonable bins to group school spending. Include in the table each of the following:
# Average Math Score
# Average Reading Score
# % Passing Math
# % Passing Reading
# Overall Passing Rate (Average of the above two)
#get min and max to determine range
budgetMin = schoolSummaryMerge["Budget Per Student"].min()
budgetMax = schoolSummaryMerge["Budget Per Student"].max()
# budgetMin $578
# budgetMax $655
budgetRange = np.arange(575,700,25)
budgetLabels = []
schoolSummaryMerge["Budget Range"] = pd.cut(schoolSummaryMerge["Budget Per Student"], budgetRange, right=False)
for index, b in enumerate(budgetRange):
    if index != len(budgetRange)-1:
        budgetLabels.append('\$' + str(b) + " - $" + str(b+24))
schoolSummaryMerge["Budget Range Label"] = pd.cut(schoolSummaryMerge["Budget Per Student"],budgetRange, labels=budgetLabels, right=False)
spend = schoolSummaryMerge.merge(student, left_on="school", right_on="school")
# get the columns we need
spendMathReading=spend[["budget","Budget Range Label", "reading_score","math_score"]]

spendMathAvg =spendMathReading.groupby("Budget Range Label")["math_score"].mean().to_frame("MathAvg")
spendReadingAvg = spendMathReading.groupby("Budget Range Label")["reading_score"].mean().to_frame("ReadingAvg")

spendPassMath = ((spendMathReading[spendMathReading["math_score"]>=70].groupby("Budget Range Label")["math_score"].count()/spendMathReading.groupby("Budget Range Label")["math_score"].count())*100).to_frame("% Passing Math")
spendPassReading = ((spendMathReading[spendMathReading["reading_score"]>=70].groupby("Budget Range Label")["reading_score"].count()/spendMathReading.groupby("Budget Range Label")["reading_score"].count()) * 100).to_frame("% Passing Reading")
spendPassOverall = ((spendMathReading[(spendMathReading["math_score"]>=70)&(spendMathReading["reading_score"]>=70)].groupby("Budget Range Label")["budget"].count()/spendMathReading.groupby("Budget Range Label")["budget"].count())*100).to_frame("% Passing Overall")

spendMathReadingOverallPass = spendPassMath.merge(spendPassReading, left_index=True, right_index=True).merge(spendPassOverall, left_index=True, right_index=True)
# spendMathReadingPass["OverallPassing"] = (spendMathReadingPass["% Passing Math"]+spendMathReadingPass["% Passing Reading"])/2

SpendingScores = spendMathAvg.merge(spendReadingAvg, left_index=True, right_index=True).merge(spendMathReadingOverallPass, left_index=True, right_index=True)
SpendingScores["MathAvg"] = SpendingScores["MathAvg"].map("{:.0f}".format)
SpendingScores["ReadingAvg"] = SpendingScores["ReadingAvg"].map("{:.0f}".format)
SpendingScores["% Passing Math"] = SpendingScores["% Passing Math"].map("{:.0f}".format)
SpendingScores["% Passing Reading"] = SpendingScores["% Passing Reading"].map("{:.0f}".format)
SpendingScores["% Passing Overall"] = SpendingScores["% Passing Overall"].map("{:.0f}".format)
SpendingScores
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>MathAvg</th>
      <th>ReadingAvg</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Passing Overall</th>
    </tr>
    <tr>
      <th>Budget Range Label</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>\$575 - $599</th>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>\$600 - $624</th>
      <td>84</td>
      <td>84</td>
      <td>94</td>
      <td>96</td>
      <td>90</td>
    </tr>
    <tr>
      <th>\$625 - $649</th>
      <td>78</td>
      <td>81</td>
      <td>71</td>
      <td>84</td>
      <td>60</td>
    </tr>
    <tr>
      <th>\$650 - $674</th>
      <td>77</td>
      <td>81</td>
      <td>66</td>
      <td>81</td>
      <td>54</td>
    </tr>
  </tbody>
</table>
</div>



##### Scores by School Size


```python
sizeRange = [0,1000, 2000, 5000]
school["School Size Range"] = pd.cut(school["size"], np.array(sizeRange))
school["School Size Category"] = pd.cut(school["size"], np.array(sizeRange), labels = ["small (0 - 1000)","medium (1001 - 2000)","large (2001 - 5000)"], right=True)
# school
schoolStudents = school.merge(student, left_on="school", right_on="school")
# sizeStudents
# Average Math Score
sizeAvgMath = schoolStudents.groupby("School Size Category")["math_score"].mean().to_frame("AvgMath")
# Average Reading Score
sizeAvgReading = schoolStudents.groupby("School Size Category")["reading_score"].mean().to_frame("AvgReading")
# % Passing Math
sizePassMath = ((schoolStudents[schoolStudents["math_score"] >=70].groupby("School Size Category")["Student ID"].count()/schoolStudents.groupby("School Size Category")["Student ID"].count())*100).to_frame("% Passing Math")
# % Passing Reading
sizePassReading = ((schoolStudents[schoolStudents["reading_score"]>= 70].groupby("School Size Category")["reading_score"].count()/schoolStudents.groupby("School Size Category")["reading_score"].count())*100).to_frame("% Passing Reading")
# Overall Passing Rate (Average of the above two)
sizePassOverall = ((schoolStudents[(schoolStudents["math_score"]>= 70) & (schoolStudents["reading_score"]>= 70)].groupby("School Size Category")["Student ID"].count()/schoolStudents.groupby("School Size Category")["Student ID"].count())*100).to_frame("% Passing Overall")

sizeOverall = sizePassMath.merge(sizePassReading, left_index=True, right_index=True).merge(sizeAvgMath, left_index=True, right_index=True).merge(sizeAvgReading, left_index=True, right_index=True).merge(sizePassOverall, left_index=True, right_index=True)
# sizeOverall.columns = ["AvgReading","AvgMath","% Passing Math","% Passing Reading","% Passing Overall"]
sizeOverall["AvgReading"] = sizeOverall["AvgReading"].map("{:.0f}".format)
sizeOverall["AvgMath"] = sizeOverall["AvgMath"].map("{:.0f}".format)
sizeOverall["% Passing Math"] = sizeOverall["% Passing Math"].map("{:.0f}".format)
sizeOverall["% Passing Reading"] = sizeOverall["% Passing Reading"].map("{:.0f}".format)
sizeOverall["% Passing Overall"] = sizeOverall["% Passing Overall"].map("{:.0f}".format)
sizeOverall = sizeOverall.loc[:,["AvgReading","AvgMath","% Passing Math","% Passing Reading","% Passing Overall"]]
sizeOverall.sort_index(ascending=False)
# sizePassMath
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>AvgReading</th>
      <th>AvgMath</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Passing Overall</th>
    </tr>
    <tr>
      <th>School Size Category</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>small (0 - 1000)</th>
      <td>84</td>
      <td>84</td>
      <td>94</td>
      <td>96</td>
      <td>90</td>
    </tr>
    <tr>
      <th>medium (1001 - 2000)</th>
      <td>84</td>
      <td>83</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>large (2001 - 5000)</th>
      <td>81</td>
      <td>77</td>
      <td>69</td>
      <td>82</td>
      <td>57</td>
    </tr>
  </tbody>
</table>
</div>



##### Scores by School Type 


```python
typeAvgMath = schoolStudents.groupby("type")["math_score"].mean().to_frame("AvgMath")
typeAvgReading = schoolStudents.groupby("type")["reading_score"].mean().to_frame("AvgReading")
typePassMath = ((schoolStudents[schoolStudents["math_score"]>=70].groupby("type")["math_score"].count()/schoolStudents.groupby("type")["math_score"].count())*100).to_frame("% Passing Math")
typePassReading = ((schoolStudents[schoolStudents["reading_score"]>=70].groupby("type")["reading_score"].count()/schoolStudents.groupby("type")["reading_score"].count())*100).to_frame("% Passing Reading")
typePassBoth = ((schoolStudents[(schoolStudents["math_score"]>=70) & (schoolStudents["reading_score"]>=70)].groupby("type")["Student ID"].count()/schoolStudents.groupby("type")["Student ID"].count())*100).to_frame("% Passing Overall")

typePassOverall = typePassMath.merge(typePassReading, left_index=True, right_index=True).merge(typeAvgMath, left_index=True, right_index=True).merge(typeAvgReading,left_index=True, right_index=True).merge(typePassBoth,left_index=True, right_index=True)
# typePassOverall["Overall Passing"] = (typePassOverall["% Passing Math"]+typePassOverall["% Passing Reading"])/2
typePassOverall["AvgReading"] = typePassOverall["AvgReading"].map("{:.0f}".format)
typePassOverall["AvgMath"] = typePassOverall["AvgMath"].map("{:.0f}".format)
typePassOverall["% Passing Math"] = typePassOverall["% Passing Math"].map("{:.0f}".format)
typePassOverall["% Passing Reading"] = typePassOverall["% Passing Reading"].map("{:.0f}".format)
typePassOverall["% Passing Overall"] = typePassOverall["% Passing Overall"].map("{:.0f}".format)
typePassOverall = typePassOverall.loc[:,["AvgMath","AvgReading","% Passing Math","% Passing Reading","% Passing Overall"]]
typePassOverall
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>AvgMath</th>
      <th>AvgReading</th>
      <th>% Passing Math</th>
      <th>% Passing Reading</th>
      <th>% Passing Overall</th>
    </tr>
    <tr>
      <th>type</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Charter</th>
      <td>83</td>
      <td>84</td>
      <td>94</td>
      <td>97</td>
      <td>91</td>
    </tr>
    <tr>
      <th>District</th>
      <td>77</td>
      <td>81</td>
      <td>67</td>
      <td>81</td>
      <td>54</td>
    </tr>
  </tbody>
</table>
</div>


