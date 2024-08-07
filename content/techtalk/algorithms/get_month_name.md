+++
date = 2024-07-30T10:07:43-04:00
title = "Get Month Name"
categories = ["techtalk","code"]
tags = ["josdem","techtalks","programming","technology","Java","Algorithms"]
description = "Map the given integer to a month."
+++


Map the given integer to a month.


**Explanation**

For month = 1, the output should be

```
getMonthName(month) = "Jan"
```

For month = 0, the output should be

```
getMonthName(month) = "invalid month"
```


**Solution**

```java
import java.util.Locale;
import java.text.DateFormatSymbols;

public class MonthNamer {

  private final static INVALID_MONTH_LABEL = "invalid month";

  private String getMonthName(Integer month){
	if(month < 1 || month > 12){
	  return INVALID_MONTH_LABEL;
	}
	return new DateFormatSymbols(Locale.ENGLISH).getShortMonths()[month-1];
  }

  public static void main(String[] args){
	Integer month = 1;
	String result = new MonthNamer().getMonthName(month);
	assert result == 'Jan'
  }

}
```

To download the code:

```bash
git clone https://github.com/josdem/algorithms-workshop.git
cd get-month-name
```

To run the code:

```bash
javac MonthNamer.java
java -ea MonthNamer
```


[Return to the main article](/techtalk/algorithms)
