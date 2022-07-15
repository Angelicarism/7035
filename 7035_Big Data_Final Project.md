# 7035_Big Data_Final Project_Innovation

- ***CHENG Xinyi 3035888449***
- ***GUO Jing 3035878860***
- ***YU Ruiqi 303588188***

## 1. **Some interesting facts about Chinese innovative activity**

**The tables we used in this section are** patents.patentview_rawassignee, patents.patentview_rawlocation and patents.stoffman_data_2020

We choose 3 factors to measure the overall picture of innovation:

- **xi_real:** The value of innovation(xi_real) is equal to the nominal value of innovation(xi_nominal) deflated using the CPI

- **cites:** how many times the patent is cited

- **num:** total number of patents



### **1.1 China compares to the world**

```sql
select year, china.xi_real_total, china.xi_real_total/world.xi_real_total as xi_real_percent, 
china.cites_total, china.cites_total/world.cites_total as cites_percent, 
china.num, china.num/world.num as num_percent
from
(select country, year, sum(xi_real) as xi_real_total,
sum(cites) as cites_total, count(*) as num 
from
(select patent_id, country from
(select patent_id, rawlocation_id from
patents.patentview_rawassignee) pra 
inner join 
(select column0, column4 as country from
patents.patentview_rawlocation) prl 
on pra.rawlocation_id = prl.column0) b
inner join (
select substring(issue_date2,1,4) as year, 
toString(patent_num) as id, xi_real, cites
from patents.stoffman_data_2020 sd) a
on b.patent_id = a.id
where country = 'CN'
group by year, country) china
inner JOIN 
(select year, sum(xi_real) as xi_real_total,
sum(cites) as cites_total, count(*) as num
from
(select patent_id, country from
(select patent_id, rawlocation_id from
patents.patentview_rawassignee) pra 
inner join 
(select column0, column4 as country from
patents.patentview_rawlocation) prl 
on pra.rawlocation_id = prl.column0) b
inner join (
select substring(issue_date2,1,4) as year, 
toString(patent_num) as id, xi_real, cites
from patents.stoffman_data_2020 sd) a
on b.patent_id = a.id
group by year) world
using year 
order by year ASC
```

![1.jpg](https://github.com/Angelicarism/7035/blob/main/1.jpg?raw=true)

![2.jpg](https://github.com/Angelicarism/7035/blob/main/2.jpg?raw=true)

![3.jpg](https://github.com/Angelicarism/7035/blob/main/3.jpg?raw=true)

![4.jpg](https://github.com/Angelicarism/7035/blob/main/4.jpg?raw=true)

![5.jpg](https://github.com/Angelicarism/7035/blob/main/5.jpg?raw=true)

From the pictures above we find that xi_real and num for China are quickly growing in the 21st Century. The xi_real has increase from below 100 in 2000 to over 6100 in 2020, a 61 times growths in just 20 years. The total numbers also have a 100 times growth in this period. Such a high growth rates elevate China’s status of innovation worldwide, which can be proved through the upward-going lines of the percentages of these factors of China to those of world.

As for cites, this factor has time lag because people need time to discover how to use the patent or the hidden value of the patent. That’s why we can observe that patents in recent years are less cited in China. However, the percentage line of China to the world reveals that more and more patents in China are cited, or in other words, considered valuable, another proof of increasing innovation in China.



### **1.2 Most innovative cities**

```sql
select city, sum(xi_real) as xi_real_total,
sum(cites) as cites_total, count(*) as num 
from
(select patent_id, city from
(select patent_id, rawlocation_id from
patents.patentview_rawassignee) pra 
inner join 
(select column0, column2 as city from
patents.patentview_rawlocation
where column4 = 'CN' and column2 is not NULL) prl 
on pra.rawlocation_id = prl.column0) b
inner join (
select 
toString(patent_num) as id, xi_real, cites
from patents.stoffman_data_2020 sd) a
on b.patent_id = a.id
group by city
order by xi_real_total DESC
```

- **order by xi_real_total**

![6.jpg](https://github.com/Angelicarism/7035/blob/main/6.jpg?raw=true)

- **order by num**

  ![7.jpg](https://github.com/Angelicarism/7035/blob/main/7.jpg?raw=true)

- **order by cites_total**

  ![8.jpg](https://github.com/Angelicarism/7035/blob/main/8.jpg?raw=true)

We also use the 3 factors to check which cities are most innovative. From the tables above, we find that Beijing, Shanghai, Shenzhen and Hong Kong are ranked Top10 in all of the 3 factors. Especially, Beijing and Shanghai always take the first and second place in the 3 tables and have a huge gap in number with the third one. The result is consistent with the fact that these two are the best developed cities in China.



### **1.3 Most innovative organizations**

```sql
select REPLACE(org,',', '') as name, sum(xi_real) as xi_real_total,
sum(cites) as cites_total, count(*) as num 
from
(select patent_id, upper(REPLACE(organization,'.', '')) as org from
(select patent_id, rawlocation_id, organization from
patents.patentview_rawassignee) pra 
inner join 
(select column0 from
patents.patentview_rawlocation
where column4 = 'CN' and column2 is not NULL) prl 
on pra.rawlocation_id = prl.column0) b
inner join (
select 
toString(patent_num) as id, xi_real, cites
from patents.stoffman_data_2020 sd) a
on b.patent_id = a.id
group by name
order by xi_real_total DESC
```

- **order by xi_real_total**

![9.jpg](https://github.com/Angelicarism/7035/blob/main/9.jpg?raw=true)

- **order by num**

  ![10.jpg](https://github.com/Angelicarism/7035/blob/main/10.jpg?raw=true)

- **order by cites_total**

  ![11.jpg](https://github.com/Angelicarism/7035/blob/main/11.jpg?raw=true)

From the perspective of xi_value, Baidu is the leader while as for cites and num, semiconductor manufacturing international ranks first. It’s possibly because Baidu is an internet company which is highly valued in recent decades and semiconductor industry has high technical barriers that generates large number of patents in technical details.



## 2. **Metaverse and 5G track**

In this section, we will track two top techs in recent years metaverse and 5G. 

We create a set of key words for mapping, the detailed taxonomy is listed below:

- **Metaverse:**  

​	'%virtual reality%'

​	%VR%

​	'%augmented reality%'

​	%AR%

​	%nonfungible tokens%

​	%nft%

​	'%blockchain%'

​	'%metaverse%'

​	%3d modeling%

​	%IOT%

​	%INTERNET OF THINGS%

​	%brain%computer%interface%

​	'%human%computer%interface%'

​	'%muscle%computer%interface%'

​	%direct%neural%interface%'

- **5G:**

​	%Fifth-generation technology%

​	%Fifth-generation mobile network%

​	%Fifth-generation network%

​	%5G%

​	%$5^{TH}generation technology$ %

​	%$5^{TH generation} mobile network$%

​	%5TH generation network%

​	%Orthogonal frequency-division multiplexing%

​	%OFDM%

​	%IOT%

​	%INTERNET OF THINGS%

​	%enhanced mobile broadband%

​	%Virtual Radio Access Network%

​	%Vran%$



### **2.1 Trend of metaverse**

```sql
select SUBSTRING(date,1,4) as year, count(*) as num
from patents.patentview_patent pp  
where UPPER(abstract) LIKE UPPER('%virtual reality%')
   or UPPER(abstract) LIKE UPPER('%vr%')
   or UPPER(abstract) LIKE UPPER('% augmented reality %')
   or UPPER(abstract) LIKE UPPER('% ar%')
   or UPPER(abstract) LIKE UPPER('%nonfungible tokens %')
   or UPPER(abstract) LIKE UPPER('%nft%')
   or UPPER(abstract) LIKE UPPER('% blockchain%')
   or UPPER(abstract) LIKE UPPER('%metaverse%')
   or UPPER(abstract) LIKE UPPER('%3d modeling%')
   or UPPER(abstract) LIKE UPPER('%human%computer%interface%')
   or UPPER(abstract) LIKE UPPER('%muscle%computer%interface%')
   or UPPER(abstract) LIKE UPPER('%direct%neural%interface%')
   or UPPER(abstract) LIKE UPPER('%brain%computer%interface%')
group by year 
ORDER by year ASC
```

![12.jpg](https://github.com/Angelicarism/7035/blob/main/12.jpg?raw=true)

![13.jpg](https://github.com/Angelicarism/7035/blob/main/13.jpg?raw=true)





### **2.2 Metaverse by country**

```sql
select country, count(distinct id) as metaverse from 
(select a.patent_id, b.country from patents.patentview_patent_assignee as a inner join 
(select id, country from patents.patentview_location ) as b
ON a.location_id = b.id) as c inner join
(select id from patents.patentview_patent pp  
where UPPER(abstract) LIKE UPPER('%virtual reality%')
   or UPPER(abstract) LIKE UPPER('%vr%')
   or UPPER(abstract) LIKE UPPER('% augmented reality %')
   or UPPER(abstract) LIKE UPPER('% ar%')
   or UPPER(abstract) LIKE UPPER('%nonfungible tokens %')
   or UPPER(abstract) LIKE UPPER('%nft%')
   or UPPER(abstract) LIKE UPPER('% blockchain%')
   or UPPER(abstract) LIKE UPPER('%metaverse%')
   or UPPER(abstract) LIKE UPPER('%3d modeling%')
   or UPPER(abstract) LIKE UPPER('%human%computer%interface%')
   or UPPER(abstract) LIKE UPPER('%muscle%computer%interface%')
   or UPPER(abstract) LIKE UPPER('%direct%neural%interface%')
   or UPPER(abstract) LIKE UPPER('%brain%computer%interface%')) as d
ON c.patent_id = d.id
group by country
order by count(*) desc;
```

![14.jpg](https://github.com/Angelicarism/7035/blob/main/14.jpg?raw=true)

![15.jpg](https://github.com/Angelicarism/7035/blob/main/15.jpg?raw=true)



### **2.3 Metaverse by organization**

```sql
select organization, count(*) as num from 
(select a.patent_id, b.organization from patents.patentview_patent_assignee as a 
inner join 
(select organization, id 
from patents.patentview_assignee
where organization is not null
) as b
ON a.assignee_id = b.id) as c inner join
(select id from patents.patentview_patent
where UPPER(abstract) LIKE UPPER('%virtual reality%')
   or UPPER(abstract) LIKE UPPER('%vr%')
   or UPPER(abstract) LIKE UPPER('% augmented reality %')
   or UPPER(abstract) LIKE UPPER('% ar%')
   or UPPER(abstract) LIKE UPPER('%nonfungible tokens %')
   or UPPER(abstract) LIKE UPPER('%nft%')
   or UPPER(abstract) LIKE UPPER('% blockchain%')
   or UPPER(abstract) LIKE UPPER('%metaverse%')
   or UPPER(abstract) LIKE UPPER('%3d modeling%')
   or UPPER(abstract) LIKE UPPER('%human%computer%interface%')
   or UPPER(abstract) LIKE UPPER('%muscle%computer%interface%')
   or UPPER(abstract) LIKE UPPER('%direct%neural%interface%')
   or UPPER(abstract) LIKE UPPER('%brain%computer%interface%')) as d
ON c.patent_id = d.id
group by organization
order by num desc;
```

![16.jpg](https://github.com/Angelicarism/7035/blob/main/16.jpg?raw=true)

![17.jpg](https://github.com/Angelicarism/7035/blob/main/17.jpg?raw=true)

**Generally speaking, the total number of metaverse related patents increased quickly, especially after 2010, the total number doubled in just 10 years, indicating that scientists are enthusiastic about metaverse.**

U.S. are absolutely the leader of metaverse, followed by Japan and German. China ranks 6th , but only 1/30 of U.S., there is still a long way to go.

As for organization, IBM is the top player with more than 80k patents related to metaverse. One interesting fact is that most of the most innovative companies are hardware related, meaning that infrastructure of metaverse is the current focus point, and software is still in the beginning period.



### **2.4 5G trend**

```sql
select SUBSTRING(date,1,4) as year, count(*) as num 
from patents.patentview_patent pp  
where UPPER(abstract) LIKE UPPER('%5G%')
   or UPPER(abstract) LIKE UPPER('%Fifth-generation technology%')
   or UPPER(abstract) LIKE UPPER('%Fifth-generation mobile network%')
   or UPPER(abstract) LIKE UPPER('%Fifth generation network%')
   or UPPER(abstract) LIKE UPPER('%5th generation network%')
   or UPPER(abstract) LIKE UPPER('%5th generation technology%')
   or UPPER(abstract) LIKE UPPER('%5th generation mobile network%')
   or UPPER(abstract) LIKE UPPER('%Orthogonal frequency-division multiplexing%')
   or UPPER(abstract) LIKE UPPER('% VRAN %')
   or UPPER(abstract) LIKE UPPER('%OFDM%')
   or UPPER(abstract) LIKE UPPER('%IOT%')
   or UPPER(abstract) LIKE UPPER('%internet of things%')
   or UPPER(abstract) LIKE UPPER('%enhanced mobile broadband%')
   or UPPER(abstract) LIKE UPPER('%Virtual Radio Access Network%')
group by year 
ORDER by year ASC
```

![18.jpg](https://github.com/Angelicarism/7035/blob/main/18.jpg?raw=true)

![19.jpg](https://github.com/Angelicarism/7035/blob/main/19.jpg?raw=true)



### **2.5 5G by country**

```sql
select country, count(distinct id) as 5G from 
(select a.patent_id, b.country from patents.patentview_patent_assignee as a inner join 
(select id, country from patents.patentview_location ) as b
ON a.location_id = b.id) as c inner join
(select * from patents.patentview_patent pp  
where UPPER(abstract) LIKE UPPER('%5G%')
   or UPPER(abstract) LIKE UPPER('%Fifth-generation technology%')
   or UPPER(abstract) LIKE UPPER('%Fifth-generation mobile network%')
   or UPPER(abstract) LIKE UPPER('%Fifth generation network%')
   or UPPER(abstract) LIKE UPPER('%5th generation network%')
   or UPPER(abstract) LIKE UPPER('%5th generation technology%')
   or UPPER(abstract) LIKE UPPER('%5th generation mobile network%')
   or UPPER(abstract) LIKE UPPER('%Orthogonal frequency-division multiplexing%')
   or UPPER(abstract) LIKE UPPER('% VRAN %')
   or UPPER(abstract) LIKE UPPER('%OFDM%')
   or UPPER(abstract) LIKE UPPER('%IOT%')
   or UPPER(abstract) LIKE UPPER('%internet of things%')
   or UPPER(abstract) LIKE UPPER('%enhanced mobile broadband%')
   or UPPER(abstract) LIKE UPPER('%Virtual Radio Access Network%')) as d
ON c.patent_id = d.id
group by country
order by count(*) desc;
```

![20.jpg](https://github.com/Angelicarism/7035/blob/main/20.jpg?raw=true)

![21.jpg](https://github.com/Angelicarism/7035/blob/main/21.jpg?raw=true)



### **2.6 5G by organization**

```sql
select organization, count(*) as num from 
(select a.patent_id, b.organization from patents.patentview_patent_assignee as a inner join 
(select organization, id 
from patents.patentview_assignee 
WHERE organization is not null) as b
ON a.assignee_id = b.id) as c inner join
(select * from patents.patentview_patent
where UPPER(abstract) LIKE UPPER('%5G%')
   or UPPER(abstract) LIKE UPPER('%Fifth-generation technology%')
   or UPPER(abstract) LIKE UPPER('%Fifth-generation mobile network%')
   or UPPER(abstract) LIKE UPPER('%Fifth generation network%')
   or UPPER(abstract) LIKE UPPER('%5th generation network%')
   or UPPER(abstract) LIKE UPPER('%5th generation technology%')
   or UPPER(abstract) LIKE UPPER('%5th generation mobile network%')
   or UPPER(abstract) LIKE UPPER('%Orthogonal frequency-division multiplexing%')
   or UPPER(abstract) LIKE UPPER('% VRAN %')
   or UPPER(abstract) LIKE UPPER('%OFDM%')
   or UPPER(abstract) LIKE UPPER('%IOT%')
   or UPPER(abstract) LIKE UPPER('%internet of things%')
   or UPPER(abstract) LIKE UPPER('%enhanced mobile broadband%')
   or UPPER(abstract) LIKE UPPER('%Virtual Radio Access Network%')) as d
ON c.patent_id = d.id
group by organization
order by num desc;
```

![22.jpg](https://github.com/Angelicarism/7035/blob/main/22.jpg?raw=true)

![23.jpg](https://github.com/Angelicarism/7035/blob/main/23.jpg?raw=true)

**5G has a more rapid growth trend than that of metaverse. The new-assigned patents related to 5G almost kept constant before 2010, and increase 6 times in the decade afterwards, a burst in this area. This is reasonable because only after the completion of 3G, 4G will engineers pay attention to 5G. Nowadays the time is coming.**

However, the result of top countries and top organizations are inconsistent with common sense. Huawei should be at least top3 organizations, but only ranks 8th in our result. China is also the leader in 5G, but ranks 6th with only 1/20 of patents in number. We think there are two possible reasons, one is that the database itself is incomplete, many of which has little access to patents in China, especially the key technologies like 5G; the other is that our key words are lack of representatives, a great number of related patents are not selected.

# Appendix

- **codes to draw the charts:**

  ```python
  import pyecharts.options as opts
  from pyecharts.charts import Line,Bar
  from pyecharts.commons.utils import JsCode
  from pyecharts.faker import Faker
  import pandas as pd
  import os
  ```

  ```python
  os.chdir(r"C:\Users\lenovo\Desktop")
  ```

  ```python
  #Q1 charts
  data = pd.read_csv('1.1.csv')
  
  x = data.year.map(lambda x: int(x)).tolist()
  
  y1 = data.xi_real_total.map(lambda x: int(x)).tolist()
  y2 = data.cites_total.tolist()
  y3 = data.num.tolist()
  
  bar1 = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("xi_real_total", y1)
      .set_global_opts(title_opts=opts.TitleOpts(title="sum(xi_real)"))
  )
  bar1.render()
  
  bar2 = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("cities_total", y2)
      .set_global_opts(title_opts=opts.TitleOpts(title="sum(cities)"))
  )
  bar2.render()
  
  bar3 = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("num", y3)
      .set_global_opts(title_opts=opts.TitleOpts(title="count(*)"))
  )
  bar3.render()
  
  x = data.year.map(lambda x: str(x)).tolist()
  l1 = data.xi_real_percent.map(lambda x: round(x*10000,2)).tolist()
  l2 = data.cites_percent.map(lambda x: round(x*10000,2)).tolist()
  l3 = data.num_percent.map(lambda x: round(x*10000,2)).tolist()
  
  line = (
      Line(init_opts=opts.InitOpts(width="1200px"))
          .add_xaxis(x)
          .add_yaxis("xi_real_percent", l1, label_opts=opts.LabelOpts(formatter=JsCode("function (params) {return params.value[1] + '‱'}")))
          .add_yaxis("cites_percent", l2, label_opts=opts.LabelOpts(formatter=JsCode("function (params) {return params.value[1] + '‱'}")))
          .add_yaxis("num_percent", l3, label_opts=opts.LabelOpts(formatter=JsCode("function (params) {return params.value[1] + '‱'}")))
          .set_global_opts(
              yaxis_opts=opts.AxisOpts(axislabel_opts=opts.LabelOpts(formatter="{value} ‱")))
          .set_series_opts(label_opts=opts.LabelOpts(is_show=False))
  )
  line.render()
  ```

  ```python
  #Q2 chart
  
  #2.1
  data2 = pd.read_csv('2.1.csv')
  x = data2.year.map(lambda x: str(x)).tolist()
  y = data2.num.tolist()
  
  line2 = (
      Line(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("num", y)
      .set_global_opts(title_opts=opts.TitleOpts(title="count(*)"))
      .set_series_opts(label_opts=opts.LabelOpts(is_show=False))
  )
  line2.render()
  
  #2.2
  data2 = pd.read_csv('2.2.csv').head(15)
  x = data2.country.tolist()
  y = data2.metaverse.tolist()
  
  bar = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("metaverse", y)
      .set_global_opts(title_opts=opts.TitleOpts(title="Top 15 country--count(distinct id)"))
  )
  bar.render()
  
  # 2.3
  data3 = pd.read_csv('2.3.csv').head(15)
  x = data3.organization.tolist()
  y = data3.num.tolist()
  
  bar = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("num", y)
      .set_global_opts(title_opts=opts.TitleOpts(title="Top 15 organization--count(*)"))
  )
  bar.render()
  
  #2.4
  data4 = pd.read_csv('2.4.csv')
  x = data4.year.map(lambda x: str(x)).tolist()
  y = data4.num.tolist()
  
  line = (
      Line(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("num", y)
      .set_global_opts(title_opts=opts.TitleOpts(title="5G trend——count(*)"))
      .set_series_opts(label_opts=opts.LabelOpts(is_show=False))
  )
  line.render()
  
  #2.5
  data5 = pd.read_csv('2.5.csv').head(15)
  x = data5.country.tolist()
  y = data5['5G'].tolist()
  
  bar = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("5G", y)
      .set_global_opts(title_opts=opts.TitleOpts(title="Top 15 country--count(distinct id)"))
  )
  bar.render()
  
  #2.6
  data6 = pd.read_csv('2.6.csv').head(15)
  x = data6.organization.tolist()
  y = data6.num.tolist()
  
  bar = (
      Bar(init_opts=opts.InitOpts(width="1200px"))
      .add_xaxis(x)
      .add_yaxis("num", y)
      .set_global_opts(title_opts=opts.TitleOpts(title="Top 15 organization--count(*)"))
  )
  bar.render()
  ```

  