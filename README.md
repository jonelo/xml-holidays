# XML Holidays

## Description

### What the project is for

The xml-holidays project suggests an XML document type for expressing international holidays.
The project also supports both current and historic holiday definitions for many countries actually.

### What is out of the scope of this project?

This project concentrates on a holiday xml standard and the actual data definitions for holidays on this planet.
This suggested standard should be independent of any programming language.
Therefore an actual software implementation that parses xml data is out of the scope of this project.

### Are there actual implementations that can process xml holidays data?

The free project called NumericalChameleon (a FOSS unit converter written in Java, released under the terms of the GPL)
is able to read and understand xml holidays data. See also http://numericalchameleon.net


## Examples

### Celebrated on a fixed day

#### Example: on January 1
In many countries the New Year's day is always celebrated on January 1, each year.

```xml
<holiday>
    <description language="en" name="New Year's Day"/>
    <description language="de" name="Neujahrstag"/>

    <definition>
        <fixed day="1" month="January"/>
    </definition>
</holiday>
```


### Celebrated on a date that is dependent on Easter

#### Example: two days before Easter Sunday

Easter is celebrated always on a Sunday. Good Friday is celebrated two days before Easter.
Simply use the easter tag at first and subtract two days from it (add -2).

```xml
<holiday>
    <description language="en" name="Good Friday"/>
    <description language="de" name="Karfreitag"/>

    <definition>
        <easter/>
        <add days="-2"/>
    </definition>
</holiday>
```


### Celebrated on the nth weekday in a particular month

#### Example: on the second Sunday in May

In the USA, Mother's Day is celebrated on the second Sunday in May.
The holiday was declared officially by some states beginning in 1912.

```xml
<!-- http://en.wikipedia.org/wiki/Mother%27s_Day -->
<holiday>
    <description language="en" name="Mother's Day"/>

    <definition firstyear="1912">
        <rule nth="2nd" weekday="Sunday" month="May"/>
    </definition>
</holiday>
```


### Celebrated on a date that is dependent on a weekday

#### Example: on October 12 with dependencies on a weekday

In Argentina Columbus Day (Día de la Raza) is celebrated on October 12, but if the date falls on a Tuesday or Wednesday,
the holiday is the preceding Monday. If it falls on a Thursday or on a Friday then the holiday is the following Monday.

```xml
<holiday>
    <description language="es" name="Día de la Raza" type="public holiday"/>
    <description language="en" name="Columbus Day" type="public holiday"/>

    <definition firstyear="1917">
        <fixed day="12" month="October"/>
        <!--
          If the date falls on a Tuesday or Wednesday,
          the holiday is the preceding Monday.
        -->
        <condition ifweekday="Tuesday">
            <add days="-1"/>  <!-- can become the preceding Monday -->
        </condition>
        <condition ifweekday="Wednesday">
            <add days="-2"/>  <!-- can become the preceding Monday -->
        </condition>
        <!--
          If it falls on a Thursday or a Friday then
          the holiday is the following Monday.
        -->
        <condition ifweekday="Thursday">
            <add days="+4"/>  <!-- can become the following Monday -->
        </condition>
        <condition ifweekday="Friday">
            <add days="+3"/> <!-- can become the following Monday -->
        </condition>
    </definition>
</holiday>
```


### Celebrated on the nth weekday in a particular month and on a fixed day in the past

#### Example: on the third Monday in February and on February 22 until 1970

As the first federal holiday to honor an American citizen, Washington's Birthday was celebrated on Washington's
actual birthday, February 22. In 1971 it was shifted to the third Monday in February.
So actually you need two definition blocks in order to define the Washington's birthday holiday correctly.

```xml
<!--
  http://en.wikipedia.org/wiki/Washington%27s_Birthday
  George Washington (February 22, 1732 – December 14, 1799)
 -->
<holiday>
    <description language="en" name="Washington's Birthday (President's Day)" type="federal holiday"/>

    <!-- Titled Washington's Birthday, the federal holiday was originally
         implemented by the United States federal government in 1880 for
         government offices in the District of Columbia (20 Stat. 277) and
         expanded in 1885 to include all federal offices (23 Stat. 516). -->
    <definition firstyear="1885" lastyear="1970">
        <fixed day="22" month="February"/>
    </definition>
    <!-- On January 1, 1971 the federal holiday was shifted to the third
         Monday in February by the Uniform Monday Holiday Act. -->
    <definition firstyear="1971">
        <rule nth="3rd" weekday="Monday" month="February"/>
    </definition>
</holiday>
```


### Celebrated on a particular weekday of the nth full week of a particular month

#### Example: on the Tuesday of the first full week of May

Since 1986 the National Teacher Day is celebrated on Tuesday of the first full week of May.
In the US, the week begins with Sunday, therefore the first full week begins with the first Sunday in May.
If you add two days, you get Tuesday.

```xml
<holiday>
    <description language="en" name="National Teacher Day"/>

    <!-- ... -->
    <definition firstyear="1986">
        <rule nth="1st" weekday="Sunday" month="May"/>
        <add days="+2"/> <!-- will become Tuesday -->
    </definition>
</holiday>
```

#### Example: on the Wednesday of the last full week of April

The National Secretaries Day is observed on the Wednesday of the last full week of April.
In the US, the week begins with Sunday and it ends with Saturday.
The last full week is therefore the week which ends with Saturday (the last Saturday in April).
In order to get the Wednesday of the last full week of April, simply subtract three days.

```xml
<holiday>
    <description language="en" name="National Secretaries Day"/>

    <!-- ... -->
    <definition firstyear="1955" lastyear="1980">
        <rule nth="Last" weekday="Saturday" month="April"/>
        <add days="-3"/> <!-- this is Wednesday -->
    </definition>
</holiday>
```

### Celebrated on a particular weekday nearest a particular date

#### Example: on Monday nearest July 1st

In the Canadian provinces Newfoundland and Labrador, the Memorial Day is observed on the Monday nearest July 1st.
Set the fixed date (July 1) and use the goto-direction called "nearest". The definition:

```xml
<holiday>
    <description language="en" name="Memorial Day" region="Newfoundland and Labrador"/>

    <definition>
        <!-- it is celebrated on the Monday nearest July 1st -->
        <fixed day="1" month="July"/>
        <goto weekday="Monday" direction="nearest"/>
    </definition>
</holiday>
```

### Celebrated in a different calendar system

#### Example: Hebrew Calendar: Fast of Esther in Israel

In Israel the Fast of Esther is observed on the 13th day of the Hebrew month of Adar. So we need to specify
the hebrew calendar for the definition.

```xml
    <holiday>
        <description language="he" name="תַּעֲנִית אֶסְתֵּר" type="Jewish holiday"/>
        <description language="en" name="Fast of Esther" type="Jewish holiday"/>
        <description language="de" name="Ta’anit Esther" type="Jewish holiday"/>

        <definition>
            <!-- The fast is observed on the 13th day of the Hebrew month of Adar -->
            <!-- When the year has two Adar months, it is observed only in the second Adar -->
            <fixed calendar="hebrew" day="13" month="Adar"/>
            <condition calendar="hebrew" ifweekday="Shabbat">
                <!--If the date of the Fast of Esther falls on Shabbat (Saturday),
                the fast is instead observed on the preceding Thursday -->
                <add calendar="hebrew" days="-2"/>
            </condition>
        </definition>
    </holiday>
```

### Seasons: a day dependent on Solstice or Equinox

#### Example: the Solstice in June in Germany

In June 2008 the Solstice was on June 20 at 23:59 UTC (24 h system). This was the begin of summer for people living
in the northern hemisphere and it was the start of winter for people living in the southern hemisphere.
In Germany's caldendars, you find June 21, 2008 for the Solstice.
This is because of the timezone difference of 2 hours from UTC.
For the Europe/Berlin timezone the actual Solstice was on June 21, 2008 at 1:59 (24 h system).
As the actual day of the Solstice (or Equinox) is dependent on the timezone in a country,
you can specify it with the parameter called timezone.

```xml
<holiday>
    <description language="de" name="Sommeranfang (Sommersonnenwende)"/>
    <description language="en" name="Solstice June"/>

    <definition>
        <solstice type="June" timezone="Europe/Berlin"/>
    </definition>
</holiday>
```
