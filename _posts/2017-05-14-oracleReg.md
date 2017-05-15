---
layout: post
title: oracle应用正则表达式
date: 2017-05-14 11:29:08 +0800
tags: [数据库 函数]
---


现在有这么一个需求， 数据库中的一个手输的‘籍贯’字段，要按一定的规范截取显示在报表上，比如，如果‘籍贯’的内容是：‘山东省潍坊市昌乐县’， 那么报表里要显示为：‘山东昌乐’， 如果‘籍贯’是山东省潍坊市寒亭区 那么就要出来 山东潍坊，简化的基本需求就是这样，取省一级和县一级，如果县一级是个区，那就取到市一级，

没有县一级的话，是数据不规范，但是先将就着取省一级和市一级。

要实现这个目的，第一是要从数据库里把相应的字段取出来， 第二是要写一个oracle截取字符串的函数处理下‘籍贯’字段。

第一步比较简单，就略过，只看我们的截取函数。

```            

    create or replace function f_addr(address in varchar2) return varchar2 as           
    new_addr varchar2(100);
    tempstr1 gscodeitems.name%type;
    tempstr2 varchar2(100);
    tempstr3 varchar2(100);
    tempstr4 varchar2(100);
    tempn0 number;
    tempn1 number;
    tempn2 number;
    tempn3 number;
    tempnn number;
    cursor minzustrs is
    select name from gscodeitems where gscodeitems.codesetnm = '411CBC8F-35D8-4634-944B-0C1065782F48’;
    begin
    /*单字县*/
    if REGEXP_COUNT(address, 
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族县])(县))$') > 0
    then new_addr := REGEXP_REPLACE(address, 
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族县])(县))$', '\1\6县');
    /*新疆维吾尔*/
    elsif REGEXP_COUNT(address, '^新疆') > 0
    then new_addr := REGEXP_REPLACE(address, 
    '^(新疆)(维吾尔自治区)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$', '\1\5\8’);
     /*特殊处理双民族两个字的民族在前, 策略是把两个字的民族干掉，就恢复成正常模式*/
    elsif regexp_count(address, '([^族]+)族', 4) >= 2   
    then tempstr1 := regexp_substr(address, '([^族]{2})族', 3);
    for minzustr in minzustrs loop    
    if tempstr1 = minzustr.name then    
    tempnn := 1; exit;    
    else tempnn := 0;    
    end if; end loop;    
    if tempnn = 1 then    
    tempn0 := length(tempstr1);    
    tempn1 := regexp_instr(address,tempstr1);    
    tempn2 := tempn1 - 1;    
    tempn3 := tempn1 + tempn0;    
    tempstr2 := substr(address,0,tempn2);    
    tempstr3 := substr(address, tempn3);    
    tempstr4 := concat(tempstr2,tempstr3);    
    new_addr := REGEXP_REPLACE(tempstr4,     
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$',  <br>   '\1\6\9');    
    else    
    tempstr1 := regexp_substr(address, '([^族])族', 3);      
    tempn0 := length(tempstr1);    
    tempn1 := regexp_instr(address,tempstr1);    
    tempn2 := tempn1 - 1;    
    tempn3 := tempn1 + tempn0;    
    tempstr2 := substr(address,0,tempn2);    
    tempstr3 := substr(address, tempn3);    
    tempstr4 := concat(tempstr2,tempstr3);    
    new_addr := REGEXP_REPLACE(tempstr4,     
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$',     '\1\6\9');        
    end if; /*特殊处理单民族 两个字的民族，先干掉广西和宁夏，再取族前两个字判断是否合法，如果合法，就干掉一个字，然后走正常流程*/
    elsif regexp_count(address, '([^族]+)族', 4) = 1 then
    tempstr1 := regexp_substr(address, '([^族]{2})族', 3);
     for minzustr in minzustrs loop    
    if tempstr1 = minzustr.name then    
    tempnn := 1; exit;    
    else tempnn := 0;    
    end if;
    end loop;    
    if tempnn = 1 then    
    tempn0 := length(tempstr1);    
    tempn1 := regexp_instr(address,tempstr1);    
    tempn2 := tempn1 - 1;    
    tempn3 := tempn1 + tempn0 - 2;    
    tempstr2 := substr(address,0,tempn2);    
    tempstr3 := substr(address, tempn3);    
    tempstr4 := concat(tempstr2,tempstr3);    
    new_addr := REGEXP_REPLACE(tempstr4,     
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$', <br>    '\1\6\9');    
    else      
    new_addr := REGEXP_REPLACE(address,     
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$',<br>     '\1\6\9');    
    end if;
    else
    new_addr := REGEXP_REPLACE(address, 
    '^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$',<br> '\1\6\9');
    end if;
    return new_addr;
    end f_addr;
```

这个函数里，比较关键的，其实是一个正则表达式，函数主体用了几个if else 处理几种特殊情况，没有覆盖全部的情况，只是覆盖了几种，这个可以稍后讨论，直接看最后一个else， 这里的这个正则就是用到的最基本的正则了：

```       
new_addr := REGEXP_REPLACE(address, 
'^([^省区族]+)(省|(.族)?自治区|市)((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$',
'\1\6\9'); 
```
这个内置函数，查下oracle的API即可，看到含义也跟猜到个差不多， 是一个返回一个新字符串来替代原字符串的功能，此处简单说下，第一个参数address，是源字符串，
第二个就是我们的正则pattern, 第三个是返回的字符串， 第三个参数`‘\1\6\9’`，如果熟悉正则的同学，可能会觉得似曾相识，这是取子表达式用的，从左到右，左括号的排序，
就是我们子表达式的排序，取第一第六第九个子表达式，直观的拿出来，就是取 `([^省区族]+)和([^族]+)和(.+)`，你找对了么。

分析下这里的这个正则，其实很简单，整体用两个括号分为两部分：头和尾。
第一个：`^([^省区族]+)(省|(.族)?自治区|市)`， 
第二个：`((.+市|.+地区|.+州)?([^族]+)(县|市|.族自治县)|((.+)市)?([^族]+)(区|旗))$`
简单的说，
第一个是匹配省、自治区和直辖市的
第二个是匹配市县两级和市区两级的，
\1自然是取省的名称，比如：山东省潍坊市昌乐县，\1就会取到：山东，
\6是取县一级的名称的，比如：山东省潍坊市昌乐县，\6就会取到：昌乐，
\9是取省市区这种行政级别的市一级的名称，比如：山东省潍坊市寒亭区，\9就会取到潍坊
当表达式匹配到\6的时候，\9的返回值会是空字符串，匹配到\9的时候，\6也会返回空字符串，所以这一个表达式，妥妥的满足我们的需求，
其他的情况，在这里先不表，就是特殊处理了下民族自治县，自治州，新疆啊等。

欢迎同学们有疑问的共同探讨。

